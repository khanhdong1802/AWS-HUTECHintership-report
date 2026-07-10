---

title: "Translated Blog Posts"
date: 2026-07-07
weight: 3
chapter: false
pre: " <b> 3. </b> "
--------------------

During my internship and AWS learning journey, I translated and studied three blog posts covering important topics in modern cloud architecture: private access to Amazon S3, content delivery using Amazon CloudFront, and the development of healthcare data lakes based on a microservices architecture.

This process helped me better understand how AWS services can be combined to address practical requirements related to network security, system performance, scalability, and data processing.

---

### [Blog 1 - Privately Accessing Amazon S3 from a VPC Using a VPC Endpoint](3.1-Blog1/)

This blog explains how an **EC2 instance located in a private subnet** can access **Amazon S3** without using an Internet Gateway or NAT Gateway.

The main solution is to use a **Gateway VPC Endpoint for Amazon S3**, which creates a private connection between resources inside the VPC and Amazon S3. This model is suitable for internal backend systems that need to upload logs, store backups, or export reports to S3 without connecting directly to the public internet.

A secure implementation requires several components:

* Gateway VPC Endpoint.
* Route Table.
* IAM Role attached to the EC2 instance.
* Endpoint Policy.
* S3 Bucket Policy.

Through this blog, I learned that a VPC Endpoint only provides private network connectivity. It does not automatically grant permission to access data stored in Amazon S3. Access must still be controlled through IAM policies and bucket policies.

The condition `aws:sourceVpce` can also be added to the bucket policy to ensure that requests are accepted only when they pass through a specified VPC Endpoint.

This blog helped me better understand the concept of **Defense in Depth**, in which a system is protected through multiple security layers rather than relying on a single mechanism.

---

### [Blog 2 - Amazon CloudFront: Accelerating Content Delivery from Edge to Origin](3.2-Blog2/)

This blog introduces **Amazon CloudFront**, AWS’s Content Delivery Network service, which distributes content to users through a global network of edge locations.

The basic CloudFront request flow is:

**User → CloudFront Edge Location → Origin**

When requested content is already cached at an edge location, CloudFront can return it directly to the user without contacting the origin. If the content is not available in the cache, CloudFront retrieves it from the origin, returns it to the user, and stores a copy for future requests.

CloudFront can be applied to many types of systems, including:

* Static websites.
* Web applications.
* E-commerce platforms.
* Media services.
* APIs.
* Applications serving users across multiple geographical regions.

In addition to improving content delivery speed, CloudFront reduces the workload on the origin, supports HTTPS, allows customized cache behaviors, and integrates with **AWS WAF** to strengthen application security.

Through this blog, I learned that CloudFront is not limited to distributing images or static files. It can also serve as an important architectural layer between users and backend systems, improving application performance, availability, scalability, and security.

---

### [Blog 3 - Getting Started with Healthcare Data Lakes Using Microservices](3.3-Blog3/)

This blog presents an approach to building a **healthcare data lake** using a **microservices architecture** to store, process, and analyze different types of healthcare data.

Healthcare data may come from multiple sources, such as:

* Electronic health records.
* Laboratory results.
* Medical devices.
* Clinical data.
* HL7v2 messages.

Dividing the system into independent microservices allows each component to focus on a specific responsibility. Each service can be maintained, updated, and scaled separately when the amount of data or processing workload increases.

An important part of the architecture is the **publish/subscribe model**, in which services exchange data through asynchronous messages instead of calling one another directly. This approach reduces dependencies between components and supports an event-driven architecture.

The AWS services used in the architecture include:

* **Amazon S3** for data storage.
* **Amazon DynamoDB** for catalog management.
* **AWS Lambda** for processing logic.
* **Amazon SNS** as a publish/subscribe messaging hub.
* **Amazon API Gateway** as the API entry point.
* **Amazon Cognito** for authentication and authorization.
* **AWS Step Functions** for coordinating data-processing workflows.

Through this blog, I learned that microservices architecture is not simply about dividing source code into smaller sections. It requires clearly defining the responsibilities, boundaries, and communication mechanisms of each system component.

---

### Overall Summary

Although the three translated blog posts address different technical problems, they share the same objective: building cloud systems that are more secure, flexible, and scalable.

* Blog 1 helped me understand how to establish private connectivity between a VPC and Amazon S3.
* Blog 2 helped me understand how CloudFront accelerates content delivery and reduces the workload on the origin.
* Blog 3 helped me understand how microservices and event-driven architecture can be applied to healthcare data-processing systems.

Through the translation and analysis process, I learned not only how individual AWS services work but also how they can be integrated to solve practical requirements involving **networking, security, performance, scalability, and data processing** in a real-world cloud architecture.
