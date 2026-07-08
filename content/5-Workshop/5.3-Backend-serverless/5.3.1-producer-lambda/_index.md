---
title: "Producer Lambda"
date: 2026-07-07
weight: 1
chapter: false
pre: " <b> 5.3.1. </b> "
---

## Role

Receives REST requests from the client (including the JWT already verified by the Cognito Authorizer), packages the message, pushes it to the SQS Main Queue, and immediately returns `202 Accepted` — without waiting for the Worker to finish processing.

## Main Code (`src/producer/index.mjs`)

```javascript
export const handler = async (event) => {
  const userId = event.requestContext.authorizer.claims.sub;
  const body = JSON.parse(event.body || "{}");
  const { connectionId, action = "check-gmail" } = body;

  const message = {
    jobId: `job-${Date.now()}-${Math.random().toString(36).slice(2, 8)}`,
    userId, connectionId, action,
    requestedAt: new Date().toISOString()
  };

  await sqs.send(new SendMessageCommand({
    QueueUrl: QUEUE_URL,
    MessageBody: JSON.stringify(message)
  }));

  return {
    statusCode: 202,
    body: JSON.stringify({ message: "Processing started", jobId: message.jobId })
  };
};

```

`userId` is retrieved directly from `event.requestContext.authorizer.claims.sub` — API Gateway automatically verifies the JWT via the Cognito Authorizer before this Lambda is invoked, so there is no need to manually verify the token in the code.

## SAM Template — expose as a REST endpoint

```yaml
ProducerFunction:
  Type: AWS::Serverless::Function
  Properties:
    FunctionName: inboxiq-producer
    CodeUri: src/producer/
    Handler: index.handler
    Timeout: 10
    Role: !Ref LambdaRoleArn
    Environment:
      Variables:
        SQS_QUEUE_URL: !Ref MainQueueUrl
    Events:
      CheckGmail:
        Type: Api
        Properties:
          RestApiId: !Ref RestApi
          Path: /check-gmail
          Method: POST

```

> **Correction from code review:** The initial version had 2 duplicate resources (`ProducerFunction` without attached Events + `CheckGmailApiEvent` that actually received the request) — they have been merged into a single resource as shown above to avoid dead code in the template.

## Deployment Results

REST API endpoint: `https://rjc76njhya.execute-api.us-east-1.amazonaws.com/prod`

Tested using `curl.exe` with a real JWT token (retrieved via `aws cognito-idp initiate-auth`), correctly returning `202 Accepted`:

```json
{"message":"Processing started","jobId":"job-1783438359558-mq2qy4"}
```
![Test Producer Complete](/AWS-HUTECHintership-report/images/5-Workshop/5.3-Backend-serverless/producer-test-202.jpg)