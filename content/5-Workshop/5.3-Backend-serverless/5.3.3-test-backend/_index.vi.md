---
title: "Deploy và Kiểm thử Backend"
date: 2026-07-07
weight: 3
chapter: false
pre: " <b> 5.3.3. </b> "
---

## Deploy bằng AWS SAM

```bash
sam build
sam deploy --resolve-s3
```

## Sự cố gặp phải trong lúc deploy

| # | Sự cố | Nguyên nhân | Cách khắc phục |
|---|---|---|---|
| 1 | `S3 Bucket not specified` | Lần đầu deploy không dùng `--guided`, chưa có bucket quản lý | Dùng cờ `--resolve-s3` để SAM tự tạo managed bucket |
| 2 | Stack `aws-sam-cli-managed-default` bị `ROLLBACK_COMPLETE` | IAM user `inboxiq-dev` thiếu quyền `s3:CreateBucket` và `cloudformation:*` | Gắn thêm `AmazonS3FullAccess` và `AWSCloudFormationFullAccess`, xóa stack lỗi, deploy lại |
| 3 | `USER_PASSWORD_AUTH flow not enabled` lúc lấy JWT test | App Client mặc định chỉ bật `ALLOW_USER_SRP_AUTH` | Bật thêm `ALLOW_USER_PASSWORD_AUTH` bằng `update-user-pool-client` (giữ nguyên các flow cũ) |
| 4 | Worker Lambda `SyntaxError` khi parse OpenAI key | Tạo secret qua PowerShell làm mất dấu ngoặc kép trong JSON | Ghi JSON ra file (`-Encoding ascii`), trỏ `--secret-string file://...` |

## Kết quả deploy

```
Outputs
-----------------------------------------------
RestApiEndpoint: https://rjc76njhya.execute-api.us-east-1.amazonaws.com/prod
WebSocketEndpoint: wss://xmz4gyqica.execute-api.us-east-1.amazonaws.com/prod

Successfully created/updated stack - inboxiq-backend in us-east-1
```

![Deploy thành công](/images/5-Workshop/5.3-Backend-serverless/deploy-success-outputs.jpg)

## CloudWatch Alarms

3 alarm giám sát: error rate của Worker, message rơi vào DLQ, và chi phí ước tính vượt $5.

![CloudWatch Alarms](/images/5-Workshop/5.3-Backend-serverless/cloudwatch-alarms.jpg)

## Test end-to-end

Tạo user test qua `aws cognito-idp admin-create-user`, lấy JWT token qua `initiate-auth`, gọi REST API bằng `curl.exe` kèm token.

**Kết quả:** nhận `202 Accepted`, message vào SQS, Worker Lambda tự động được trigger, xử lý qua các bước: lấy OpenAI key (Secrets Manager) → query Gmail token (DynamoDB) → **dừng đúng chỗ dự kiến** vì user test chưa có Gmail OAuth token:

```
Worker received 1 messages
Processing jobId=job-1783439894695-xlcyzo for userId=d4480488-...
ERROR: No Gmail token for user d4480488-c031-7014-0d67-6eed60f23063
    at getGmailToken (file:///var/task/index.mjs:43:27)
```

![Log Worker dừng đúng chỗ dự kiến](/images/5-Workshop/5.3-Backend-serverless/worker-log-no-gmail-token.jpg)

> Lỗi này **không phải bug** — xác nhận toàn bộ luồng Producer → SQS → Worker → Secrets Manager → DynamoDB chạy đúng thiết kế, chỉ dừng lại đúng điểm còn thiếu (Gmail OAuth token), sẽ hoàn thiện ở mục tiếp theo — **5.4 Gmail OAuth Setup**.
