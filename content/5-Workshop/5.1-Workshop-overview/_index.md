---
title : "Introduction"
date : 2026-07-07 
weight : 1 
chapter : false
pre : " <b> 5.1. </b> "
---
## Project Introduction

**InboxIQ** is an AI assistant that summarizes and categorizes emails, helping users (students, office workers) who receive 50–100 emails/day and lack the time to read them all. The system automatically summarizes content, categorizes priority levels, and suggests actions for each email.

- **Client:** Flutter (mobile app)
- **Architecture:** Serverless, Event-Driven on AWS
- **AI Provider:** OpenAI API (GPT-4o-mini)
- **AWS Region:** us-east-1

> **Why use OpenAI instead of Amazon Bedrock?** AWS Free Tier accounts are not granted access to Bedrock. The system is designed following **AI Provider Abstraction** — separating the AI invocation layer from the business logic — so it can be switched to Bedrock later without modifying the overall architecture.

## Overall Architecture (5 layers)

![InboxIQ Architecture](/images/5-Workshop/5.1-Workshop-overview/architecture.png)

| Layer | Component |
|---|---|
| **1. User** | Users, Web/Mobile App (Flutter) |
| **2. Backend** | Cognito, API Gateway (REST + WebSocket), Lambda (Producer + Worker) |
| **3. Workflow** | SQS (Main Queue + Dead-Letter Queue), EventBridge Scheduler (optional) |
| **4. Storage** | DynamoDB (6 tables), Secrets Manager, Systems Manager Parameter Store |
| **5. Monitoring** | CloudWatch, X-Ray, IAM Role |
| **External** | Gmail API, OpenAI API (outside AWS Cloud) |

## Processing Flow (14 steps)

| # | From → To | Description |
|---|---|---|
| setup | App → API Gateway WebSocket | Connect with JWT → Lambda Authorizer verifies → receives `connectionId` |
| 1 | Users → App | Open app, click "Check Gmail" |
| 2 | App → Cognito | Authenticate, receive JWT |
| 3 | App → API Gateway REST | Call API with JWT + `connectionId` |
| 4 | API Gateway REST → Lambda Producer | Verify JWT, invoke Producer |
| 5 | Lambda Producer → SQS Main Queue | Package message (including connectionId), return `202 Accepted` immediately |
| 6 | SQS Main Queue → Lambda Worker | Trigger asynchronous processing (Partial Batch Response) |
| 7a | Lambda Worker → Secrets Manager | Retrieve OpenAI API key (global scope cache) |
| 7b | Lambda Worker → DynamoDB | Query Gmail OAuth token by UserID |
| 8 | Lambda Worker → Gmail API | Fetch unread emails list |
| 9 | Lambda Worker → OpenAI API | Summarize + prioritize (timeout fail-fast) |
| 10 | Lambda Worker → DynamoDB | Write summary (EmailSummaries table, 30-day TTL) |
| 11 | Lambda Worker → WebSocket → App | Push real-time results using connectionId |
| 13 | SQS Main → SQS DLQ | When retry > 3 times |
| 14 | EventBridge Scheduler → SQS Main Queue | Auto-trigger every 30 mins (optional) |

## Database Tables (6 DynamoDB tables)

| Table | Partition Key | Sort Key | TTL | Purpose |
|---|---|---|---|---|
| `inboxiq-users` | `userId` | — | ❌ | Basic user information |
| `inboxiq-user-settings` | `userId` | — | ❌ | Personalization settings |
| `inboxiq-gmail-connections` | `userId` | — | ❌ | Gmail OAuth token |
| `inboxiq-email-summaries` | `userId` | `emailId` | ✅ 30 days | Email summaries |
| `inboxiq-processing-jobs` | `jobId` | — | ✅ 7 days | Processing status (debug) |
| `inboxiq-ws-connections` | `connectionId` | — | ✅ 2 hours | Mapping connectionId ↔ userId |

## Estimated Costs (Free Tier + OpenAI)

| Cost Source | /month at demo scale |
|---|---|
| AWS (Cognito, API GW, Lambda, SQS, DynamoDB, SSM, CloudWatch, X-Ray, EventBridge) | ~$0 (within free tier) |
| WebSocket connection-minute | ~$0.10 |
| OpenAI GPT-4o-mini | ~$0.30–1.00 |
| **Total** | **~$0.50–1.50/month** |

## Limitations to Note

1. No multi-region failover yet — acceptable for an MVP, mention as a future development direction.
2. No CI/CD pipeline yet — manual deployment via SAM CLI is sufficient for the report.
3. Rate limiting currently only relies on general API Gateway throttling, no per-user usage plan yet.