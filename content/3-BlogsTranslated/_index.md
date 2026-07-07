---
title: "Translated Blog Posts"
date: 2026-07-07
weight: 3
chapter: false
pre: " <b> 3. </b> "
---

During my internship and AWS learning process, I translated and studied three blog posts related to important topics in modern cloud architecture. These blogs focus on private access to Amazon S3, content delivery optimization with Amazon CloudFront, and healthcare data lake architecture using microservices.

Translating and analyzing these blogs helped me better understand how AWS services can be applied to real-world problems such as network security, website performance optimization, origin load reduction, healthcare data processing, and flexible system design.

---

### [Blog 1 - Accessing Amazon S3 Privately from a VPC Using a VPC Endpoint](3.1-Blog1/)

This blog explains how to design a private access model for **Amazon S3** from an **EC2 Instance located in a private subnet** without using an Internet Gateway or NAT Gateway. The main focus is the use of a **Gateway VPC Endpoint for S3** to create a private network path between resources inside a VPC and Amazon S3. This approach improves security, reduces dependency on the public internet, and optimizes operational cost.

The blog describes a common scenario where an internal backend service needs to upload logs, export reports, or back up data to S3, while still remaining isolated inside a private subnet. Instead of allowing the private subnet to access the internet through a NAT Gateway, the architecture uses a VPC Endpoint, route table configuration, IAM Role, Endpoint Policy, and Bucket Policy to control both the request path and access permissions.

Through this blog, I learned that a VPC Endpoint only solves the problem of **network connectivity**. It does not automatically grant access to Amazon S3. To build a secure system, multiple security layers must work together, including the EC2 IAM Role, Endpoint Policy, and Bucket Policy with conditions such as `aws:sourceVpce`. This is a clear example of the **Defense in Depth** approach in cloud architecture.

The most valuable lesson from this blog is that resources inside a private subnet are not completely isolated from AWS services. With proper network and security design, they can still communicate with services like Amazon S3 safely, privately, and efficiently. This knowledge is highly useful for backend systems, internal services, data processing workloads, and batch jobs in production environments.

---

### [Blog 2 - Amazon CloudFront: Accelerating Content Delivery from Edge to Origin](3.2-Blog2/)

This blog introduces **Amazon CloudFront**, AWS’s Content Delivery Network service that helps deliver content faster through a global network of edge locations. The blog explains why a website or web application may become slow when every request goes directly to the origin, especially when users access the system from different geographical regions.

The basic request flow is described as: **User → CloudFront Edge Location → Origin**. When content is already cached at an edge location, CloudFront can respond directly to the user without forwarding the request to the origin. When a cache miss occurs, CloudFront retrieves the content from the origin, returns it to the user, and stores a copy for future requests.

The blog highlights several practical use cases, including static websites, modern web applications, e-commerce platforms, media systems, API services, and security layers in front of the origin. Besides improving page load speed, CloudFront also reduces load on the origin, supports HTTPS, allows flexible cache behavior configuration, and integrates well with **AWS WAF** to protect applications from abnormal requests and common web attacks.

From this blog, I learned that CloudFront is not only useful for serving images or static files. It is also an important component in modern cloud architecture. It improves performance, increases system stability, reduces backend load, and enhances security when placed between users and the origin.

---

### [Blog 3 - Getting Started with Healthcare Data Lakes: Using Microservices](3.3-Blog3/)

This blog introduces how to build a **healthcare data lake** using a **microservices architecture**. The main idea is to design a system that can store, process, and analyze many types of healthcare data while still maintaining security, maintainability, and scalability.

The blog explains that a data lake is a centralized repository that can store both raw and processed data for analytics. In healthcare, data may come from many different sources, such as electronic health records, laboratory results, medical devices, or HL7v2 messages. Because of this complexity, splitting the system into smaller microservices helps each data processing component remain independent, easier to modify, and easier to scale.

One important concept in the blog is the **pub/sub hub** model, where microservices communicate asynchronously through messages instead of directly calling one another. This architecture reduces service coupling, supports event-driven design, and makes the system more flexible when new data connectors or processing components need to be added.

The AWS services discussed include **Amazon S3** for data storage, **Amazon DynamoDB** as a data catalog, **AWS Lambda** for processing logic, **Amazon SNS** as the pub/sub hub, **Amazon API Gateway** as the front door, **Amazon Cognito** for authentication and authorization, and **AWS Step Functions** for orchestrating ER7 message processing workflows.

Through this blog, I learned that microservices are not simply about splitting code into smaller parts. They are about defining clear responsibility boundaries between system components. For a healthcare data lake, microservices improve maintainability, scalability, team ownership, and the ability to process complex data pipelines with different formats and requirements.

---

### Overall Summary

Through these three translated blogs, I realized that although the topics are different, they all share the same goal: building cloud systems that are **more secure, more flexible, and more scalable**.

- Blog 1 helped me understand how to secure private connectivity between a private subnet and Amazon S3 using a VPC Endpoint.
- Blog 2 helped me understand how to accelerate content delivery and reduce origin load using Amazon CloudFront.
- Blog 3 helped me understand how microservices can be used to build a healthcare data lake capable of handling complex data processing requirements.

These topics helped me strengthen my cloud architecture mindset. I did not only learn how to use individual AWS services, but also how multiple services can work together to solve real-world problems related to networking, security, performance, scalability, and data processing.
