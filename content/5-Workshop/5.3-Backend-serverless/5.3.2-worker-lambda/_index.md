---
title: "5.3.2 Worker Lambda"
date: 2026-07-07
weight: 2
chapter: false
pre: " <b> 5.3.2. </b> "
---

## Role

Triggered automatically by SQS when a new message arrives. Fetches the OpenAI key, fetches the Gmail token, calls the Gmail API for unread emails, calls OpenAI to summarize them, saves the result to DynamoDB, and pushes it to the client over WebSocket.

## Global-scope cache — fixing "repeated Secrets Manager calls"

```javascript
let cachedOpenAIKey = null;

async function getOpenAIKey() {
  if (cachedOpenAIKey) return cachedOpenAIKey;
  const result = await secrets.send(new GetSecretValueCommand({
    SecretId: "inboxiq/openai-api-key"
  }));
  cachedOpenAIKey = JSON.parse(result.SecretString).apiKey;
  return cachedOpenAIKey;
}
```

The `cachedOpenAIKey` variable is declared outside the `handler`, at global scope — the Lambda container is reused across invocations, so Secrets Manager is called only once per container lifetime instead of once per message.

> **Issue encountered:** On the first test run, the Worker crashed with `SyntaxError: Expected property name or '}' in JSON at position 1` — the `inboxiq/openai-api-key` secret was stored in invalid JSON format (double quotes were swallowed by PowerShell when the secret was created). Fixed by writing the secret to a JSON file (`Out-File -Encoding ascii`) and pointing `--secret-string file://...` at it instead of typing JSON directly on the command line.

![SyntaxError parsing the OpenAI key](/images/5-Workshop/5.3-Backend-serverless/worker-syntax-error.jpg)

## Partial Batch Response — fixing "retrying the whole batch when one email fails"

```javascript
export const handler = async (event) => {
  const batchItemFailures = [];
  for (const record of event.Records) {
    try {
      // ... process each message
    } catch (err) {
      batchItemFailures.push({ itemIdentifier: record.messageId });
    }
  }
  return { batchItemFailures };
};
```

Combined with `ReportBatchItemFailures` in the SAM template — SQS only retries the specific message that failed within the batch, not the whole batch.

## SAM Template

```yaml
WorkerFunction:
  Type: AWS::Serverless::Function
  Properties:
    FunctionName: inboxiq-worker
    CodeUri: src/worker/
    Handler: index.handler
    Timeout: 300
    Role: !Ref LambdaRoleArn
    Environment:
      Variables:
        WS_ENDPOINT: !Sub "https://${WebSocketApi}.execute-api.${AWS::Region}.amazonaws.com/prod"
    Events:
      SQSTrigger:
        Type: SQS
        Properties:
          Queue: !Ref MainQueueArn
          BatchSize: 5
          FunctionResponseTypes:
            - ReportBatchItemFailures
```

## WebSocket Authorizer + Connect/Disconnect

3 supporting Lambdas for WebSocket:
- **`inboxiq-ws-authorizer`** — verifies the JWT from the query string (`wss://...?token=xxx`) using the `aws-jwt-verify` library
- **`inboxiq-ws-connect`** — saves the `connectionId ↔ userId` mapping to DynamoDB when a client connects
- **`inboxiq-ws-disconnect`** — removes the mapping when a client disconnects

> **Fixed from code review:** The 3 Lambda Permissions originally only had `Principal: apigateway.amazonaws.com` with no `SourceArn` restriction — a "confused deputy" vulnerability that would let any API Gateway invoke these functions. Added `SourceArn: !Sub "arn:aws:execute-api:${AWS::Region}:${AWS::AccountId}:${WebSocketApi}/*"` to scope it to InboxIQ's WebSocket API only.

## IAM Role — Least Privilege

All 5 Lambdas (Producer, Worker, Authorizer, ws-connect, ws-disconnect) share a single `inboxiq-lambda-role` granting only what's needed: DynamoDB (Get/Put/Update/Delete/Query on exactly the 6 tables), SQS (Send/Receive/Delete on exactly the 2 queues), Secrets Manager (GetSecretValue on the exact secret path), KMS Decrypt, WebSocket ManageConnections, CloudWatch Logs, X-Ray — no excess permissions.

![IAM Role Permissions](/images/5-Workshop/5.3-Backend-serverless/iam-role-permissions.jpg)
