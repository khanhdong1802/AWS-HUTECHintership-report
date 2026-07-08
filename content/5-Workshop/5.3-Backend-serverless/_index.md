---
title: "Serverless Backend"
date: 2026-07-07
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

## Overview

The InboxIQ backend is built entirely with AWS CLI + AWS SAM, with no manual Console steps (except a few mandatory ones like Cognito and S3 permissions). Build order:

1. Cognito User Pool (user authentication)
2. 6 DynamoDB tables (data storage)
3. Secrets Manager (stores the OpenAI API key)
4. SQS Main Queue + Dead-Letter Queue (asynchronous processing queue)
5. Dedicated IAM Role for Lambda (Least Privilege)
6. 5 Lambda functions (deployed via SAM)
7. CloudWatch Alarms (monitoring)

## 1. Cognito User Pool

Created through Cognito's new Quick Setup UI, application type **Mobile app**, sign-in with **Email**, no client secret generated (a Flutter mobile app cannot safely store a secret).

![Cognito User Pool](/images/5-Workshop/5.3-Backend-serverless/cognito-overview.jpg)

| Value | Result |
|---|---|
| User Pool ID | `us-east-1_eCU5t53Fl` |
| App Client ID | `5jbrrakldheal208d4kfi1iqci` |
| Region | us-east-1 |

## 2. DynamoDB — 6 tables

Created via AWS CLI (`aws dynamodb create-table`) in `PAY_PER_REQUEST` mode (billed per actual read/write, suited to the MVP's low, uneven traffic).

![6 DynamoDB tables](/images/5-Workshop/5.3-Backend-serverless/dynamodb-tables.jpg)

| Table | Partition Key | Sort Key | TTL |
|---|---|---|---|
| `inboxiq-users` | userId | — | ❌ |
| `inboxiq-user-settings` | userId | — | ❌ |
| `inboxiq-gmail-connections` | userId | — | ❌ |
| `inboxiq-email-summaries` | userId | emailId | ✅ 30 days |
| `inboxiq-processing-jobs` | jobId | — | ✅ 7 days |
| `inboxiq-ws-connections` | connectionId | — | ✅ 2 hours |

TTL is enabled separately on 3 tables via `aws dynamodb update-time-to-live`, automatically deleting expired items with no manual cleanup needed.

## 3. Secrets Manager

Stores the OpenAI API key instead of SSM Parameter Store — Secrets Manager provides detailed audit logging and supports auto-rotation.

![Secrets Manager](/images/5-Workshop/5.3-Backend-serverless/secrets-manager.jpg)

> **Issue encountered:** The first time the secret was created from PowerShell, the double quotes in the JSON `{"apiKey":"..."}` were swallowed, storing the secret incorrectly as `{apiKey:sk-proj-...}` — not valid JSON — which crashed the Worker Lambda with a `SyntaxError`. Fixed by writing the JSON to a file (`Out-File -Encoding ascii`) and pointing `--secret-string file://...` at it instead of typing JSON directly on the command line.

## 4. SQS — Main Queue + Dead-Letter Queue

The DLQ is created first, then the Main Queue's redrive policy points to it (`maxReceiveCount: 3`).

![SQS Dead-letter queue](/images/5-Workshop/5.3-Backend-serverless/sqs-dlq-config.jpg)

`VisibilityTimeout = 330s` = Worker Lambda timeout (300s) + 30s buffer — it must exceed the Lambda's processing time so SQS doesn't return the message to the queue while the Lambda is still running.

## 5. IAM Role for Lambda (Least Privilege)

The `inboxiq-lambda-role` grants only what's needed: DynamoDB (Get/Put/Update/Delete/Query on exactly the 6 tables), SQS (Send/Receive/Delete on exactly the 2 queues), Secrets Manager (GetSecretValue on the exact secret path), KMS Decrypt, WebSocket ManageConnections, CloudWatch Logs, X-Ray — no excess permissions.

![IAM Role permissions](/images/5-Workshop/5.3-Backend-serverless/iam-role-permissions.jpg)
