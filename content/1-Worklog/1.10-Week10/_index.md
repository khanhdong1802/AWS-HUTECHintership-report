---
title: "Week 10 Worklog"
date: 2026-06-22
weight: 10
chapter: false
pre: " <b> 1.10. </b> "
---

### Key Objectives for Week 10:

- Kick off the final capstone project: **InboxIQ - AI-powered email triage**.
- Evaluate and finalize the Project Proposal, clearly defining the problem and the Serverless solution.
- Deeply research the AWS services to be used in the architecture: API Gateway (REST & WebSocket), Amazon SQS, AWS Lambda, DynamoDB, and Cognito.
- Design and draw the Architecture Diagram & Dataflow Diagram for the 5-tier system.
- Create a Cost Estimation spreadsheet based on the AWS Free Tier and set up AWS Budgets to establish a safe financial barrier.
- Prepare the account and environment to be ready for infrastructure deployment in the following week.

---

### Detailed Action Plan:

| Day | Task Details | Start Date | Completion Date | Reference Materials |
| --- | --- | --- | --- | --- |
| Monday | - Team meeting to finalize the InboxIQ project proposal <br> - Analyze the email overload problem and shape the Event-Driven solution | 06/22/2026 | 06/22/2026 | InboxIQ Project Proposal |
| Tuesday | - Understand the operating mechanism of API Gateway WebSocket for real-time <br> - Research Amazon SQS (Main & Dead-Letter Queue) | 06/23/2026 | 06/23/2026 | AWS Documentation (API Gateway, SQS) |
| Wednesday | - Research Amazon Cognito for authentication <br> - Research how Lambda securely connects to SSM Parameter Store | 06/24/2026 | 06/24/2026 | AWS Documentation (Cognito, SSM) |
| Thursday | - Draw the overall Architecture Diagram <br> - Sketch the Dataflow Diagram from Client to OpenAI | 06/25/2026 | 06/25/2026 | Draw.io / Lucidchart |
| Friday | - Deconstruct data, design the schema for 6 DynamoDB tables <br> - Determine the necessary Partition Keys and Sort Keys | 06/26/2026 | 06/26/2026 | AWS DynamoDB Best Practices |
| Saturday | - Create a system Cost Estimation spreadsheet <br> - Configure AWS Budgets and a Hard Limit on OpenAI ($5.00) | 06/27/2026 | 06/27/2026 | AWS Pricing Calculator |
| Sunday | - Review the entire architecture design with the Mentor <br> - Finalize the deployment plan for week 11 <br> - Write the week 10 report | 06/28/2026 | 06/28/2026 | Internal Team Documents |

---

### Summary of Achieved Results:

#### Knowledge & Design
- Finalized the **Project Proposal** for InboxIQ, clearly defining the ROI and system risks.
- Clearly understood why **Amazon SQS** must be used as a buffer: it helps decouple the process of receiving user requests from the process of calling the OpenAI API (which is time-consuming), avoiding client-side timeouts.
- Grasped the mechanism of maintaining a two-way connection with **API Gateway WebSocket** to push results back from AWS Lambda to the mobile app without the client having to continuously "poll".
- Completely designed the 5-tier architecture diagram (User, Backend, Workflow, Storage, Monitoring).
- Strategized the secure storage of sensitive API keys (OpenAI Key, Gmail Client Secret) into **SSM Parameter Store** instead of hard-coding them in the Lambda source code.

#### Practical Implementation
- Finished designing the NoSQL database schema for 6 specialized DynamoDB tables (User, Session, EmailMetadata, etc.).
- Completed the cost estimation. Thanks to the 100% On-Demand architecture, the estimated AWS cost is $0.00 (completely within the Free Tier).
- Successfully activated AWS Budgets alert sets ($5 and $10 thresholds) to prevent cost overruns.

---

### Challenges and Obstacles Faced:
- Designing the WebSocket connection flow was logically complex, especially regarding how to store the `connectionId` in DynamoDB so the Lambda Worker knows exactly which user's device to return the results to.
- Had to weigh heavily when designing the Partition Key for DynamoDB to avoid a "Hot Partition" situation when queries are concentrated on a specific user.
- Confused in determining parameters for the Dead-Letter Queue (DLQ), such as the maximum number of retries before moving a message to the isolation area.

### Solutions and Lessons Learned:
- Drew a detailed Sequence Diagram for the WebSocket Handshake process so the team could easily visualize how the `connectionId` is created and stored.
- Read more AWS NoSQL design documentation and decided to use a combination of `userId` and `messageId` as a composite partition key to distribute data evenly.
- Set the SQS retry count (`maxReceiveCount`) to 3; if the Lambda Worker fails processing 3 consecutive times, the message is automatically pushed to the DLQ to avoid infinite loops that incur costs.

### Plan and Roadmap for Next Week:
- Enter the "combat" phase: Deploy the Backend infrastructure configuration on AWS.
- Initialize Cognito, DynamoDB, and SSM Parameter Store.
- Write Node.js code for the Lambda cluster (Producer & Worker) and configure SQS queues.