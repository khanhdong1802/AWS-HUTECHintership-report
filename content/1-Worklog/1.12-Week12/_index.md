---
title: "Week 12 Worklog"
date: 2026-07-06
weight: 12
chapter: false
pre: " <b> 1.12. </b> "
---

### Key Objectives for Week 12:

- Finalize the **Integration, Test and Fix** flow for the InboxIQ project.
- Integrate the Frontend interface (Flutter app) with the AWS Backend system (Cognito, API Gateway).
- Successfully connect with external services: Gmail API (OAuth 2.0 flow) and OpenAI API (NLP processing).
- Conduct End-to-End (E2E) Testing to evaluate latency and load-bearing capacity.
- Use AWS X-Ray to trace data flows and debug.
- Collect visual proof screenshots (Console Screenshots) and compile the FCAJ 2026 internship acceptance report.

---

### Detailed Action Plan:

| Day | Task Details | Start Date | Completion Date | Reference Materials |
| --- | --- | --- | --- | --- |
| Monday | - Code Flutter app: Integrate Amplify/Cognito SDK library <br> - Build login flow and retrieve JWT Token | 07/06/2026 | 07/06/2026 | Flutter Docs / AWS Amplify |
| Tuesday | - Integrate Gmail API: Handle OAuth callback flow to get Access Token <br> - Securely store the user's Gmail token in DynamoDB | 07/07/2026 | 07/07/2026 | Google Workspace API Docs |
| Wednesday | - Integrate OpenAI API: Configure prompts for GPT-4o-mini <br> - Program the email extraction, summarization, and priority labeling flow | 07/08/2026 | 07/08/2026 | OpenAI API Reference |
| Thursday | - Integrate WebSocket on Flutter (using `web_socket_channel` package) <br> - Complete full connection flow: App -> REST API -> SQS -> Lambda -> OpenAI -> WebSocket -> App | 07/09/2026 | 07/09/2026 | Internal Team Documents |
| Friday | - Conduct Test & Fix: Test send a batch of 50 emails <br> - Activate AWS X-Ray to trace logs, optimize bottlenecks | 07/10/2026 | 07/10/2026 | AWS X-Ray Docs |
| Saturday | - Refine application edges (UI/UX) <br> - Take screenshots of AWS resources and successful CloudWatch logs as proof | 07/11/2026 | 07/11/2026 | InboxIQ App |
| Sunday | - Clean up unnecessary draft resources <br> - Finalize the entire FCAJ 2026 internship report <br> - Prepare content for the acceptance presentation | 07/12/2026 | 07/12/2026 | FCAJ Report Form |

---

### Summary of Achieved Results:

#### Technical & Integration
- Successfully "stitched" all the pieces together: The Flutter mobile app communicated smoothly with AWS Serverless via Cognito's JWT tokens.
- Elegantly solved the asynchronous problem: When users click "Summarize Email", the app immediately displays a "Processing..." status (thanks to the 202 response from the REST API). A few seconds later, the system automatically pushes classification results from OpenAI via WebSocket, making the UI update in real-time without freezing the app.
- The AI Provider Abstraction integration scenario worked well; the Node.js source code interacted smoothly with GPT-4o-mini via environment variables and keys retrieved from SSM.

#### Testing & Evaluation (Test & Fix)
- The X-Ray Tracing flow helped the team clearly see the time consumed at each stage (e.g., SQS took 50ms, calling Gmail took 400ms, calling OpenAI took 2.5s). This provided a realistic view of system latency.
- Tested Fault Tolerance: When intentionally passing an incorrect Gmail API Key, the system accurately pushed the message to the DLQ (Dead-Letter Queue) after 3 retries, proving the reliability of the architecture.
- The actual operational testing cost remained completely within the safety fund (only costing about $0.8 USD for OpenAI tokens throughout the testing process, AWS Cost = $0).

---

### Challenges and Obstacles Faced:
- Maintaining the WebSocket connection on mobile devices was sometimes intermittent when the device switched from Wifi to 4G.
- The Gmail Access Token expired quite quickly, leading to authentication errors when the Lambda Worker attempted to download emails.
- Parsing the return results from OpenAI encountered errors because sometimes the AI generated non-standard JSON formats unlike what was expected.

### Solutions and Lessons Learned:
- **WebSocket:** Coded an Auto-reconnect logic on the Flutter app when detecting an intermittently closed socket signal.
- **Gmail OAuth:** Wrote a supplementary Refresh Token flow on the Lambda Worker. When the Access Token reports a 401 error, Lambda will use the Refresh Token to get a new code before retrying to read emails.
- **OpenAI:** Configured the `response_format: { type: "json_object" }` parameter in the payload sent to OpenAI and fine-tuned the System Prompt to force the AI to return a strict JSON structure, avoiding parse errors.

### Final Evaluation of InboxIQ Project:
- The project excellently completed all objectives set out in the Week 10 Proposal.
- Successfully solved the email overload problem using AI with a 100% Serverless, Event-Driven architecture.
- Ensured compliance with information security principles (Least Privilege, KMS Encryption, Token encryption).
- The system is ready for the First Cloud AI Journey (FCAJ) 2026 acceptance review with a complete set of report documents, diagrams, and visual proofs. Thank you for a 12-week journey full of effort!