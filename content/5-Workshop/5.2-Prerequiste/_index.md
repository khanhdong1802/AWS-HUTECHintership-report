---
title : "Prerequiste"
date : 2024-01-01 
weight : 2 
chapter : false
pre : " <b> 5.2. </b> "
---
## Why This Step is Important

Before writing any line of Lambda code, it is essential to set up a cost "safety net" — because the OpenAI API is the only variable cost in this architecture, and a loop bug in Lambda could accidentally trigger continuous API calls, incurring unintended charges.

## 1. AWS Budgets Alert

Create a $20/month budget with 4 alert thresholds at 10%, 25%, 50%, and 100%.

![Budget alert](/images/5-Workshop/5.2-Prerequisite/budget-created.jpg)

## 2. OpenAI Spend Limit

Set a project spend limit of $5/month, along with an alert at $2.50.

![OpenAI limit](/images/5-Workshop/5.2-Prerequisite/openai-limit.jpg)

## 3. Local Tool Installation

| Tool | Version |
|---|---|
| AWS CLI | 2.35.16 |
| Node.js | v24.14.1 |
| AWS SAM CLI | 1.163.0 |
| Flutter | 3.38.2 |

## 4. Create a Dedicated IAM User (Do Not Use Root)

AWS recommends against creating access keys for the root account because its permissions are unlimited and cannot be restricted. Create a dedicated IAM user named `inboxiq-dev` and attach the following policies: `AWSLambda_FullAccess`, `AmazonSQSFullAccess`, `AmazonDynamoDBFullAccess`, `AmazonAPIGatewayAdministrator`, `SecretsManagerReadWrite`, `CloudWatchFullAccess`, and `IAMFullAccess`.

![IAM user](/images/5-Workshop/5.2-Prerequisite/iam-user-created.jpg)

## 5. Configure AWS CLI

```bash
aws configure
aws sts get-caller-identity