---
title: "Lambda Worker"
date: 2026-07-07
weight: 2
chapter: false
pre: " <b> 5.3.2. </b> "
---

## Vai trò

Được SQS trigger tự động khi có message mới. Lấy OpenAI key, lấy Gmail token, gọi Gmail API lấy email chưa đọc, gọi OpenAI tóm tắt, lưu DynamoDB, push kết quả qua WebSocket.

## Global scope cache — fix vấn đề "gọi Secrets Manager lặp lại"

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

Biến `cachedOpenAIKey` khai báo ngoài `handler`, ở global scope — Lambda container được tái sử dụng giữa các lượt gọi, nên chỉ gọi Secrets Manager đúng 1 lần cho cả vòng đời container thay vì mỗi message một lần.

> **Sự cố gặp phải:** Lần đầu test, Worker crash với `SyntaxError: Expected property name or '}' in JSON at position 1` — do secret `inboxiq/openai-api-key` bị lưu sai định dạng JSON (mất dấu ngoặc kép do PowerShell nuốt mất lúc tạo secret). Đã khắc phục bằng cách ghi lại secret qua file JSON (`Out-File -Encoding ascii`) rồi trỏ `--secret-string file://...` thay vì gõ JSON trực tiếp trên dòng lệnh.

![Lỗi SyntaxError khi parse OpenAI key](/AWS-HUTECHintership-report/images/5-Workshop/5.3-Backend-serverless/worker-syntax-error.jpg)

## Partial Batch Response — fix vấn đề "retry cả batch khi 1 email lỗi"

```javascript
export const handler = async (event) => {
  const batchItemFailures = [];
  for (const record of event.Records) {
    try {
      // ... xử lý từng message
    } catch (err) {
      batchItemFailures.push({ itemIdentifier: record.messageId });
    }
  }
  return { batchItemFailures };
};
```

Kết hợp với cấu hình `ReportBatchItemFailures` trong SAM template — SQS chỉ retry đúng message bị lỗi trong batch, không phải toàn bộ batch.

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

3 Lambda phụ trợ cho WebSocket:
- **`inboxiq-ws-authorizer`** — verify JWT qua query string (`wss://...?token=xxx`) bằng thư viện `aws-jwt-verify`
- **`inboxiq-ws-connect`** — lưu mapping `connectionId ↔ userId` vào DynamoDB khi client kết nối
- **`inboxiq-ws-disconnect`** — xóa mapping khi client ngắt kết nối

> **Sửa từ code review:** 3 Lambda Permission ban đầu chỉ có `Principal: apigateway.amazonaws.com` mà không giới hạn `SourceArn` — lỗ hổng "confused deputy" cho phép bất kỳ API Gateway nào cũng có thể invoke các hàm này. Đã thêm `SourceArn: !Sub "arn:aws:execute-api:${AWS::Region}:${AWS::AccountId}:${WebSocketApi}/*"` để giới hạn đúng phạm vi WebSocket API của InboxIQ.

## IAM Role — Least Privilege

Cả 5 Lambda (Producer, Worker, Authorizer, ws-connect, ws-disconnect) dùng chung 1 role `inboxiq-lambda-role`, chỉ cấp đúng quyền cần thiết: DynamoDB (Get/Put/Update/Delete/Query trên đúng 6 bảng), SQS (Send/Receive/Delete trên đúng 2 queue), Secrets Manager (GetSecretValue trên đúng path secret), KMS Decrypt, WebSocket ManageConnections, CloudWatch Logs, X-Ray — không cấp quyền thừa.

![IAM Role Permissions](/AWS-HUTECHintership-report/images/5-Workshop/5.3-Backend-serverless/iam-role-permissions.jpg)
