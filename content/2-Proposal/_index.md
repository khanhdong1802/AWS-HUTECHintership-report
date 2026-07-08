---
title: "Proposal"
date: 2026-07-04
weight: 2
chapter: false
pre: "  2.  "
---

# InboxIQ

## AI-powered email triage on Serverless AWS

### 1. Executive Summary

InboxIQ is an intelligent virtual assistant designed to address the issue of email overload for modern users (students and office workers). The mobile application (built with Flutter) automatically connects to the Gmail API and leverages an OpenAI artificial intelligence model (GPT-4o-mini) to summarize content, categorize emails, and evaluate priority levels in real-time. The system runs entirely on a Serverless and Event-Driven architecture on the AWS platform (deployed in the us-east-1 region) to optimize asynchronous processing performance, ensure stringent security, and minimize operational costs within the framework of the First Cloud AI Journey (FCAJ) 2026 internship program.

### 2. Problem Statement

*Current Problem* Modern users frequently face the challenge of receiving 50–100 emails daily, resulting in excessive time spent filtering information and an increased risk of missing critical messages. Existing email management solutions are often manual, lack deep semantic analysis capabilities, or rely on expensive third-party AI integrations that raise data privacy concerns due to centralized processing.

*Solution* InboxIQ addresses this by constructing an automated, distributed email processing system utilizing a Serverless Event-Driven architecture. Upon a user's request, the Flutter application interacts securely via AWS API Gateway (REST & WebSocket) authenticated by Amazon Cognito. Requests are pushed into an Amazon SQS queue to trigger an AWS Lambda Worker for asynchronous processing (reducing client-side load and optimizing backend resources). The Lambda Worker queries the Gmail OAuth token stored in DynamoDB, fetches unread emails via the Gmail API, sends them to the OpenAI API for generative processing (summarization and classification), saves the results back to DynamoDB (with a self-deleting TTL mechanism), and pushes the final output back to the client in real-time over WebSocket. Due to AWS Free Tier limitations on Amazon Bedrock permissions, the solution utilizes the OpenAI API but is designed with an AI Provider Abstraction layer to allow seamless migration to Bedrock in the future.

*Benefits and Return on Investment (ROI)* The system saves up to 80% of the time spent reading and sorting daily emails through concise summaries and smart priority tagging. Adopting a 100% AWS Serverless model eliminates fixed hardware infrastructure costs entirely. Operational costs are extremely low (fitting comfortably within the AWS Free Tier for compute and storage), with only minimal token usage fees from the OpenAI API. The ROI in terms of user productivity and time saved is realized within the very first month of deployment.

### 3. Solution Architecture

The system is organized into 5 distinct functional layers following a Serverless Event-Driven model, ensuring isolated scalability and high fault tolerance:

```
LAYER 1 · USER        → Users, Mobile App (Flutter)
LAYER 2 · BACKEND     → Cognito, API Gateway (REST + WebSocket), Lambda (Producer + Worker)
LAYER 3 · WORKFLOW     → SQS (Main Queue + Dead-Letter Queue), EventBridge Scheduler (optional)
LAYER 4 · STORAGE      → DynamoDB (6 tables), Systems Manager Parameter Store
LAYER 5 · MONITORING   → CloudWatch, X-Ray, IAM Role
EXTERNAL SERVICES      → Gmail API, OpenAI API (Outside AWS Cloud)

```

*AWS Services Utilized* - *Amazon Cognito*: Manages user identity, authentication, and secure JWT token issuance.

* *Amazon API Gateway*: Provides HTTP REST Endpoints for synchronous tasks and WebSocket Endpoints to maintain two-way real-time communication.
* *AWS Lambda*: Implemented as independent compute functions (Lambda Authorizer, Lambda Producer for orchestration, and Lambda Worker for core processing).
* *Amazon SQS*: Manages asynchronous message queues, including `inboxiq-main` (main processing queue featuring Partial Batch Response) and `inboxiq-dlq` (Dead-Letter Queue to isolate failed messages after 3 retries).
* *Amazon DynamoDB*: A NoSQL database that stores user configurations, WebSocket session mappings, and cached email summaries.
* *AWS Systems Manager Parameter Store*: Securely stores system configurations and OpenAI API Keys as encrypted strings (SecureString).
* *Amazon EventBridge Scheduler*: Automatically triggers the email checking process periodically every 30 minutes.
* *AWS CloudWatch & AWS X-Ray*: Gathers operational logs, tracks metrics, and provides end-to-end distributed tracing for Lambda functions.

*Component Design* - *Mobile App (Flutter)*: Provides the user interface, connects to the WebSocket endpoint, triggers analysis requests, and displays real-time results.

* *Orchestration Layer (Producer)*: Receives requests from API Gateway, validates them, encapsulates the data along with the `connectionId` into SQS, and immediately returns a `202 Accepted` status to the client.
* *Processing Layer (Worker)*: Consumes messages from SQS, executes external integration API calls (Gmail API & OpenAI API), stores data in the database, and pushes the status directly back to the client via the corresponding WebSocket connection.
* *Storage Layer*: Deserializes data into 6 specialized DynamoDB tables to optimize NoSQL query structures and data lifecycle management.

### 4. Technical Implementation

*Implementation Phases* The project is divided into 4 main phases, aligned with the FCAJ 2026 internship reporting schedule:

1. *Context Research & Architecture Drafting*: Evaluate the email overload problem, build the 5-layer system design document, select OpenAI as the alternative AI provider, and complete architecture review rounds with system mentors (January).
2. *Cost Estimation & Budget Guardrails*: Set up the AWS account, configure AWS Budgets to prevent exceeding the Free Tier limits, and set a hard cap spending limit on the OpenAI Platform (January).
3. *Infrastructure Provisioning & Core Backend Development*: Create storage resources (DynamoDB, SSM, SQS), configure API Gateway endpoints, set up IAM Roles following the Principle of Least Privilege, write Lambda function source code, and implement global scope caching to prevent API throttling (February).
4. *Flutter Integration, Testing & Deployment*: Implement the OAuth callback flow to retrieve Gmail tokens, develop the Flutter UI, perform end-to-end dataflow testing via X-Ray, and finalize the internship report (March).

*Technical Requirements* - *Client Application*: Developed using the Flutter Mobile SDK, integrating the Amplify/Cognito SDK for user authentication, and utilizing the `web_socket_channel` package to maintain real-time communication.

* *Backend Infrastructure*: All Lambda source code is written in Node.js. The Worker function is configured with a maximum execution timeout of 5 minutes and must enable the SQS `ReportBatchItemFailures` property to handle partial failures within a batch. All sensitive data at rest must be encrypted using default AWS KMS keys.

### 5. Roadmap & Milestones

* *January (Initiation)*: Design the architecture, finalize dataflow diagrams, register AWS/OpenAI accounts, and complete budget safety configurations (AWS Budgets).
* *February (Core Development)*: Complete the provisioning of 6 DynamoDB tables, configure API Gateway (REST & WebSocket with Authorizer), deploy the Lambda Producer and Worker code, and successfully integrate SQS with a functional DLQ mechanism.
* *March (Integration & Finalization)*: Implement the Gmail OAuth connection flow from the Flutter app. Successfully integrate OpenAI API calls. Conduct comprehensive end-to-end testing, capture all AWS Console configuration screenshots, and compile the final FCAJ internship report.

### 6. Budget Estimation

The infrastructure is strictly optimized under an On-Demand model to fully exploit the AWS Free Tier, minimizing the risk of unexpected costs during the internship.

*AWS Infrastructure Costs (Estimated Monthly)* - AWS Lambda: $0.00 (Within the 1 million free requests/month allocation).

* Amazon SQS & API Gateway: ~$0.00 (MVP testing volume is well below Free Tier thresholds).
* Amazon DynamoDB: $0.00 (Configured under On-Demand mode for automatic scaling).
* AWS Systems Manager & AWS X-Ray: $0.00.

*External Service Integration Costs* - OpenAI API (GPT-4o-mini): ~$1.00 - $2.00/month (Based on a testing workload of roughly 50-100 emails/day). The maximum monthly spending limit (Hard Limit) is strictly locked at **$5.00/month** on the platform to prevent financial risks.

### 7. Risk Assessment

*Risk Matrix* - Gmail OAuth Token expiration or disconnection: High Impact, Medium Probability.

* API Throttling from OpenAI or AWS SSM: Medium Impact, Low Probability.
* Unexpected AWS infrastructure costs: High Impact, Low Probability.

*Mitigation Strategies* - Token Disconnection: Build a dedicated token status table, validate token validity prior to processing, and implement an automated refresh token flow.

* Throttling: Cache secure API keys within the Global Scope of the Lambda functions to avoid repetitive queries to the Parameter Store on every single request. Enable SQS Partial Batch Response to retry only failed email messages instead of re-processing the entire batch.
* Budget Risks: Set up AWS Budgets to automatically send email notifications when costs hit $5, $10, and $20, and configure a Budget Action to revoke IAM User permissions if the total cost reaches $50.

### 8. Expected Outcomes

*Technical Improvements*: Successfully build a complete asynchronous event-driven backend system that handles high-throughput data streams smoothly without freezing the mobile UI, thanks to the real-time WebSocket response model.
*Long-term Value*: Provide a foundational blueprint that can be repurposed for other serverless natural language processing (NLP) applications, while serving as practical, hands-on evidence (via Console screenshots) for an outstanding FCAJ internship report.