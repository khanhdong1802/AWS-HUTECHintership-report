---
title: "Workshop"
date: 2026-07-07
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Building the InboxIQ Application on the AWS Serverless Platform

#### Overview

**InboxIQ** is a smart email management application developed on the **AWS Serverless** platform, integrated with artificial intelligence services to help users read, analyze, and summarize email content quickly. Instead of using a traditional server model, the system leverages fully managed AWS services to reduce operational costs, increase scalability, and simplify the deployment process.

In this workshop, we will step-by-step build and deploy a Serverless Backend using the **AWS Serverless Application Model (AWS SAM)**. At the same time, we will configure and integrate key AWS services such as **Amazon API Gateway**, **AWS Lambda**, **Amazon Cognito**, **Amazon DynamoDB**, **Amazon SQS**, **AWS Secrets Manager**, and **Amazon CloudWatch** to create a complete system capable of authenticating users, processing data, storing information, and monitoring application activities.

Besides AWS services, the workshop also provides instructions on how to integrate **Gmail OAuth 2.0** so users can securely grant access to their Gmail inbox. Once authorized, the system will use the **Gmail API** to retrieve email content, combined with the **OpenAI API** to summarize, categorize, and analyze information, thereby helping users easily keep track of important emails without having to read the entire content.

Through this workshop, learners will understand the process of deploying a real-world Serverless application on AWS, from preparing the development environment, deploying infrastructure using Infrastructure as Code, and building business processing APIs, to integrating third-party services, testing, and operating the system. This is also an opportunity to get familiar with Event-Driven architecture and modern application development methods on the AWS cloud computing platform.

#### Table of Contents

1. [Workshop Overview](5.1-Workshop-overview/)
2. [Prerequisites](5.2-Prerequiste/)
3. [Deploying Serverless Backend on AWS](5.3-Backend/)
4. [Integrating Gmail OAuth](5.4-Gmail-OAuth/)
5. [VPC Endpoint Policies](5.5-Flutter-app/)
6. [Clean up](5.6-Cleanup/)