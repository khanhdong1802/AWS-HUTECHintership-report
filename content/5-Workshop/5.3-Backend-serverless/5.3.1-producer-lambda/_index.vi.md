---
title: "Lambda Producer"
date: 2026-07-07
weight: 1
chapter: false
pre: " <b> 5.3.1. </b> "
---

## Vai trò

Nhận request REST từ client (kèm JWT đã được Cognito Authorizer verify), đóng gói message, đẩy vào SQS Main Queue, trả `202 Accepted` ngay lập tức — không đợi Worker xử lý xong.

## Code chính (`src/producer/index.mjs`)

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

`userId` được lấy trực tiếp từ `event.requestContext.authorizer.claims.sub` — API Gateway đã tự verify JWT qua Cognito Authorizer trước khi Lambda này được gọi, không cần verify token thủ công trong code.

## SAM Template — expose thành REST endpoint

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

> **Sửa từ code review:** Bản đầu có 2 resource trùng lặp (`ProducerFunction` không gắn Events + `CheckGmailApiEvent` mới thật sự nhận request) — đã gộp lại thành 1 resource duy nhất như trên, tránh dead code trong template.

## Kết quả deploy

REST API endpoint: `https://rjc76njhya.execute-api.us-east-1.amazonaws.com/prod`

Test bằng `curl.exe` kèm JWT token thật (lấy qua `aws cognito-idp initiate-auth`), nhận về đúng `202 Accepted`:

```json
{"message":"Processing started","jobId":"job-1783438359558-mq2qy4"}
```

![Test Producer thành công](/images/5-Workshop/5.3-Backend-serverless/producer-test-202.jpg)
