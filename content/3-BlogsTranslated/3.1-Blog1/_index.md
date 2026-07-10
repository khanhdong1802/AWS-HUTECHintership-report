---
title: "Blog 1"
date: 2026-07-02
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# Accessing Amazon S3 Privately from a VPC Using a VPC Endpoint

_This article is shared by the Otokoshi team based on our learning and hands-on practice with AWS._

When deploying demo environments on AWS, the simplest way for an EC2 instance to upload or download data from Amazon S3 is to place the EC2 instance in a public subnet, assign a public IP, route traffic through an Internet Gateway, and use the AWS CLI or SDK.

However, for production systems (backends, internal services, data processing) running in a **private subnet**, routing data over the public internet is not an optimal solution in terms of security or cost. This article will guide you through designing an architecture to access Amazon S3 completely privately from within a VPC using a **Gateway VPC Endpoint**.

---

## Context and Problem Statement

Suppose our system has an internal backend running on an EC2 instance within a private subnet, which is not directly exposed to the internet for security reasons. This backend needs to perform the following tasks:

- Store system log files.
- Export periodic reports.
- Backup configurations and batch data to Amazon S3.

Without an internet connection, the EC2 instance cannot connect to the default S3 endpoint. Instead of configuring the private subnet to route outbound traffic through a NAT Gateway (which incurs costly data processing fees), we can use a **Gateway VPC Endpoint for S3** to establish a private, more secure, and cost-effective internal route.

---

## Overall Architecture and Workflow

The MVP (Minimum Viable Product) model consists of the following core components:

- **EC2 Instance**: Located in a private subnet (no public IP).
- **Amazon S3 Bucket**: Stores the target data, with _Block Public Access_ enabled.
- **Gateway VPC Endpoint**: A Gateway-type VPC endpoint dedicated to Amazon S3.
- **Route Table**: The private subnet's route table configured to route S3-destined traffic through the endpoint.
- **IAM & Policies**: Includes an IAM Role for the EC2 instance, an Endpoint Policy, and a Bucket Policy to regulate access control.

### Data Processing Workflow:

1. The EC2 instance in the private subnet sends a request (read/write) to Amazon S3.
2. The subnet's route table identifies that the traffic destination is S3 (via an AWS Prefix List) and routes the request through the **Gateway VPC Endpoint**.
3. Amazon S3 receives the request and undergoes multi-layer authentication: verifying the EC2 instance's IAM Role, the Endpoint policy, and the Bucket policy.
4. If all security layers are valid, the EC2 instance interacts with the bucket without needing an Internet Gateway or a NAT Gateway.

   ![Illustration image](/AWS-HUTECHintership-report/images/3-BlogsTranslated/Blog1/blog1(0).jpg)
---

## Choosing a Solution: VPC Endpoint vs. NAT Gateway

A VPC Endpoint does not completely replace a NAT Gateway. Below is a comparison table clarifying the role of each technology in a network architecture:

| Criteria                  | Gateway VPC Endpoint (S3/DynamoDB)                                       | NAT Gateway                                                                       |
| ------------------------- | ------------------------------------------------------------------------ | --------------------------------------------------------------------------------- |
| **Connection Scope**      | Only connects privately to supported AWS services (S3, DynamoDB).        | Connects to the entire internet (calling external APIs, updating packages, etc.). |
| **Traffic Path**          | Stays entirely within the internal AWS backbone network.                 | Passes through the public internet.                                               |
| **Cost**                  | **Free** hourly usage and data processing fees.                          | Charges per hour of operation plus data processing fees per GB.                   |
| **Network Configuration** | Updates the Route Table with the Endpoint ID (`vpce-xxx`) as the target. | Updates the Route Table with the NAT Gateway ID (`nat-xxx`) as the target.        |

---

## Critical Security Layers to Note

> **Architectural Tip:** A VPC Endpoint only addresses network connectivity; it does not automatically grant data access. To keep the system robust and secure, you must tightly integrate a **Defense in Depth** mechanism across three policy layers:

- **IAM Role (EC2)**: Defines what actions the EC2 instance itself is allowed to perform on S3 (e.g., `s3:GetObject`, `s3:PutObject`).
- **Endpoint Policy**: Restricts which actions or buckets are allowed to transmit traffic through this specific endpoint.
- **Bucket Policy**: Controls incoming requests to the bucket. To tighten security, you should configure the bucket policy to only accept requests coming from a specific VPC Endpoint using the `aws:sourceVpce` condition.

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "Allow-Access-From-Specific-VPCE-Only",
      "Effect": "Deny",
      "Principal": "*",
      "Action": "s3:*",
      "Resource": [
        "arn:aws:s3:::your-bucket-name",
        "arn:aws:s3:::your-bucket-name/*"
      ],
      "Condition": {
        "StringNotEquals": {
          "aws:sourceVpce": "vpce-1a2b3c4d"
        }
      }
    }
  ]
}
```

### Connection Troubleshooting & Debugging Tips:

When an EC2 instance fails to connect or is denied access to S3, look beyond the application source code. Go through this checklist systematically:

- [ ] **IAM Role**: Is the correct IAM Role attached to the EC2 instance with Least Privilege permissions?
- [ ] **Route Table**: Does the private subnet have a route directing S3 traffic to `vpce-xxx`?
- [ ] **DNS & Region**: Are the bucket name and region configured accurately in the code?
- [ ] **Policies**: Is there an accidental `Deny` block in either the Bucket Policy or the Endpoint Policy?

---

## Key Takeaways

Through this hands-on implementation of private Amazon S3 access, our team gathered three valuable lessons:

1. **Private Subnets do not mean complete isolation**: When network paths are properly designed with a VPC Endpoint, resources inside a private subnet can seamlessly and securely communicate with required AWS services.
2. **Integrated Security Mindset**: A VPC Endpoint is more than just a network configuration; it reduces the attack surface by completely eliminating the need for public internet connections when they aren't necessary.
3. **Synergy of Multiple Services**: A well-architected cloud system is the intersection of Network knowledge (Route tables, Endpoints) and Security practices (IAM, Bucket Policies, Logging). Understanding exactly where a request travels and at which layer permissions are enforced is the key to running a secure, optimized, and operational system.

## Screenshot of blog post:
![Screenshot](/AWS-HUTECHintership-report/images/3-BlogsTranslated/Blog1/blog1(1).jpg)

## LinkBlog:[Link blog](https://www.facebook.com/groups/awsstudygroupfcj/posts/2201893240575636/?notif_id=1782978457405305&notif_t=tagged_with_story&ref=notif)