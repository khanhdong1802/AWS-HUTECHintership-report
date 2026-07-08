---
title: "Xây dựng Backend Serverless"
date: 2026-07-07
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

## Tổng quan

Backend InboxIQ được dựng hoàn toàn bằng AWS CLI + AWS SAM, không thao tác thủ công qua Console (trừ vài bước bắt buộc như Cognito, S3 permission). Thứ tự dựng hạ tầng:

1. Cognito User Pool (xác thực người dùng)
2. 6 bảng DynamoDB (lưu trữ dữ liệu)
3. Secrets Manager (lưu OpenAI API key)
4. SQS Main Queue + Dead-Letter Queue (hàng đợi xử lý bất đồng bộ)
5. IAM Role riêng cho Lambda (Least Privilege)
6. 5 Lambda function (deploy qua SAM)
7. CloudWatch Alarms (giám sát)

## 1. Cognito User Pool

Tạo qua giao diện Quick Setup mới của Cognito, application type **Mobile app**, sign-in bằng **Email**, không generate client secret (vì Flutter mobile không giữ được secret an toàn).

![Cognito User Pool](/AWS-HUTECHintership-report/images/5-Workshop/5.3-Backend-serverless/cognito-overview.jpg)

| Giá trị | Kết quả |
|---|---|
| User Pool ID | `us-east-1_eCU5t53Fl` |
| App Client ID | `5jbrrakldheal208d4kfi1iqci` |
| Region | us-east-1 |

## 2. DynamoDB — 6 bảng

Tạo bằng AWS CLI (`aws dynamodb create-table`), chế độ `PAY_PER_REQUEST` (tính phí theo lượt đọc/ghi thực tế, phù hợp traffic thấp và không đều của MVP).

![6 bảng DynamoDB](/AWS-HUTECHintership-report/images/5-Workshop/5.3-Backend-serverless/dynamodb-tables.jpg)

| Bảng | Partition Key | Sort Key | TTL |
|---|---|---|---|
| `inboxiq-users` | userId | — | ❌ |
| `inboxiq-user-settings` | userId | — | ❌ |
| `inboxiq-gmail-connections` | userId | — | ❌ |
| `inboxiq-email-summaries` | userId | emailId | ✅ 30 ngày |
| `inboxiq-processing-jobs` | jobId | — | ✅ 7 ngày |
| `inboxiq-ws-connections` | connectionId | — | ✅ 2 giờ |

3 bảng có TTL được bật riêng bằng `aws dynamodb update-time-to-live`, tự động xóa item hết hạn mà không tốn thao tác xóa thủ công.

## 3. Secrets Manager

Lưu OpenAI API key thay vì dùng SSM Parameter Store — vì Secrets Manager có audit log chi tiết và hỗ trợ auto-rotation.

![Secrets Manager](/AWS-HUTECHintership-report/images/5-Workshop/5.3-Backend-serverless/secrets-manager.jpg)

> **Sự cố gặp phải:** Lần đầu tạo secret trên PowerShell, dấu ngoặc kép trong JSON `{"apiKey":"..."}` bị nuốt mất, secret lưu sai thành `{apiKey:sk-proj-...}` không phải JSON hợp lệ — khiến Worker Lambda crash với `SyntaxError`. Khắc phục bằng cách ghi JSON ra file (`Out-File -Encoding ascii`) rồi trỏ `--secret-string file://...` thay vì gõ JSON trực tiếp trên dòng lệnh.

## 4. SQS — Main Queue + Dead-Letter Queue

DLQ tạo trước, Main Queue trỏ redrive policy về DLQ (`maxReceiveCount: 3`).

![SQS Dead-letter queue](/AWS-HUTECHintership-report/images/5-Workshop/5.3-backend-serverless/sqs-dlq-config.jpg)

`VisibilityTimeout = 330s` = Lambda Worker timeout (300s) + buffer 30s — bắt buộc lớn hơn thời gian Lambda xử lý để tránh SQS trả message về queue giữa lúc Lambda đang chạy dở.

## 5. IAM Role cho Lambda (Least Privilege)

Role `inboxiq-lambda-role` chỉ cấp đúng quyền cần dùng: DynamoDB (Get/Put/Update/Delete/Query trên đúng 6 bảng), SQS (Send/Receive/Delete trên đúng 2 queue), Secrets Manager (GetSecretValue trên đúng path secret), KMS Decrypt, WebSocket ManageConnections, CloudWatch Logs, X-Ray — không cấp quyền thừa.

![IAM Role permissions](/AWS-HUTECHintership-report/images/5-Workshop/5.3-Backend-serverless/iam-role-permissions.jpg)