---
title: "Week 11 Worklog"
date: 2026-06-29
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
---

### Key Objectives for Week 11:

- Transition from the design phase to the **System Build Project** phase.
- Deploy the entire Storage & Config Layer.
- Set up the asynchronous message queue with Amazon SQS.
- Program and deploy AWS Lambda functions (Node.js) acting as Producer and Worker.
- Configure the API Gateway network (REST and WebSocket) to communicate with the Client.
- Integrate Amazon Cognito security and strict IAM authorization.

---

### Detailed Action Plan:

| Day | Task Details | Start Date | Completion Date | Reference Materials |
| --- | --- | --- | --- | --- |
| Monday | - Initialize Amazon Cognito User Pool <br> - Configure 6 Amazon DynamoDB tables as designed <br> - Save API Keys to SSM Parameter Store | 06/29/2026 | 06/29/2026 | AWS Console |
| Tuesday | - Initialize Amazon SQS (`inboxiq-main` and `inboxiq-dlq`) <br> - Set up IAM Roles (Least Privilege) for Lambda functions | 06/30/2026 | 06/30/2026 | AWS IAM & SQS Docs |
| Wednesday | - Write source code (Node.js) for Lambda Authorizer <br> - Write source code for Lambda Producer (receives request, throws into SQS) | 07/01/2026 | 07/01/2026 | AWS SDK for JavaScript v3 |
| Thursday | - Write source code for Lambda Worker (handles core logic) <br> - Configure SQS trigger to invoke the Lambda Worker | 07/02/2026 | 07/02/2026 | Node.js / AWS SDK |
| Friday | - Configure Amazon API Gateway (REST) integrated with Lambda Producer <br> - Configure API Gateway (WebSocket) integrated with the result return flow | 07/03/2026 | 07/03/2026 | AWS API Gateway Docs |
| Saturday | - Perform internal connection test (Unit test) between API Gateway -> SQS -> Lambda <br> - Apply Global Caching mechanism in Lambda | 07/04/2026 | 07/04/2026 | Postman / wscat |
| Sunday | - Review logs on CloudWatch to check for errors <br> - Optimize Lambda timeout duration <br> - Write the week 11 report | 07/05/2026 | 07/05/2026 | AWS CloudWatch |

---

### Summary of Achieved Results:

#### Knowledge & Operations
- Mastered the process of connecting AWS services together through the Event-Driven mechanism (Event triggers Event).
- Understood the importance of assigning accurate IAM Roles: Lambda Producer is only granted `sqs:SendMessage`, while Lambda Worker requires `sqs:ReceiveMessage` and `dynamodb:PutItem`.
- Grasped Lambda optimization techniques: Caching parameters from SSM into global variables outside the `handler()` function to leverage the execution context reuse feature (Warm start), helping reduce throttling caused by excessive SSM API calls.

#### Practical Implementation
- Successfully created the Cognito User Pool cluster for authentication.
- Successfully deployed 6 DynamoDB tables with the TTL (Time to Live) data self-destruction mechanism to automatically delete old logs after 30 days, saving storage capacity.
- Successfully coded and deployed the Lambda cluster using Node.js.
- Successfully configured the SQS `ReportBatchItemFailures` mechanism, allowing the Lambda Worker to reprocess only the specific failed emails instead of re-running the entire message batch.
- Used the `wscat` tool (on terminal) to successfully test establishing a WebSocket connection channel via API Gateway.

---

### Challenges and Obstacles Faced:
- CORS (Cross-Origin Resource Sharing) errors continuously appeared when testing the REST API Gateway via a browser.
- Forgot to grant the `execute-api:ManageConnections` permission to the Lambda Worker, resulting in this function being unable to push data back to the API Gateway WebSocket, causing a 403 Forbidden error.
- Lambda Worker experienced Timeouts because the AWS default is only 3 seconds, whereas calling the OpenAI and Gmail APIs takes much longer.

### Solutions and Lessons Learned:
- Activated the Enable CORS feature on API Gateway and fine-tuned the response headers from the Lambda Producer (`Access-Control-Allow-Origin: '*'`).
- Immediately added the IAM permission `execute-api:ManageConnections` to the Lambda Worker's Role.
- Increased the Lambda Worker's Timeout configuration to 5 minutes (300 seconds) to ensure sufficient waiting time for OpenAI to generate email summary data.

### Plan and Roadmap for Next Week:
- Integrate the Mobile app (Flutter) with the AWS Backend.
- Handle connection flows with External APIs: Gmail API (OAuth) and OpenAI API (GPT-4o-mini).
- Perform End-to-End Testing of the entire system, fix bugs, and prepare data for the FCAJ internship acceptance report.