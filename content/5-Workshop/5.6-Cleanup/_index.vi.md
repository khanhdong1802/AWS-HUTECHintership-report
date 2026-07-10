---
title: "Dọn dẹp tài nguyên"
date: 2026-07-09
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

Sau khi hoàn tất demo và nộp báo cáo, toàn bộ tài nguyên AWS phục vụ InboxIQ nên được dọn dẹp để tránh phát sinh chi phí ngoài dự kiến. Mục này hướng dẫn từng bước xoá đúng thứ tự, tránh xoá nhầm resource đang được resource khác phụ thuộc.

#### 5.6.1 Xoá stack CloudFormation chính (Lambda, API Gateway, SQS, DynamoDB)

Toàn bộ backend được quản lý bởi một stack SAM duy nhất, nên xoá gọn bằng một lệnh:

```powershell
cd D:\AWS\Project\AWSF\inboxiq-backend
sam delete
```

Lệnh này hiện ra xác nhận trước khi xoá — kiểm tra đúng tên stack `inboxiq-backend` rồi mới đồng ý:

```
Are you sure you want to delete the stack inboxiq-backend in the region us-east-1 ? [y/N]:
Are you sure you want to delete the folder inboxiq-backend in S3 which contains the artifacts? [y/N]:
```

`sam delete` sẽ tự động xoá toàn bộ resource nằm trong `template.yaml`: 8 Lambda function, REST API, WebSocket API, 4 bảng DynamoDB, SQS queue (kèm dead-letter queue), Lambda Layer (mọi version), và các Lambda permission liên quan.


![Terminal hiện xác nhận sam delete](/AWS-HUTECHintership-report/images/5-Workshop/5.6-Cleanup/cleanup-sam-delete-confirm.jpg)

#### 5.6.2 Kiểm tra CloudFormation Console sau khi xoá

Vào **CloudFormation Console** → tìm stack `inboxiq-backend` → xác nhận trạng thái chuyển thành `DELETE_COMPLETE` (stack sẽ biến mất khỏi danh sách mặc định sau khi xoá xong, cần bật "Show nested" hoặc lọc theo trạng thái để thấy).


![CloudFormation stack inboxiq-backend trước khi xoá](/AWS-HUTECHintership-report/images/5-Workshop/5.6-Cleanup/cleanup-cfn-stack-before.jpg)

#### 5.6.3 Xoá Cognito User Pool

User Pool được tạo thủ công ở bước đầu (file 02), không nằm trong SAM stack, nên cần xoá riêng:

1. Vào **Cognito Console** → **User pools** → chọn user pool của InboxIQ.
2. **Delete** (góc trên bên phải trang tổng quan) → nhập tên user pool để xác nhận → **Delete**.


![Cognito User Pool trước khi xoá](/AWS-HUTECHintership-report/images/5-Workshop/5.6-Cleanup/cleanup-cognito-pool.jpg)

#### 5.6.4 Xoá các Secret trong Secrets Manager

InboxIQ lưu hai bộ thông tin nhạy cảm trong Secrets Manager: API key của OpenAI và Client Secret của Google OAuth. Cả hai cần được xoá:

```powershell
aws secretsmanager delete-secret `
  --secret-id inboxiq/openai-api-key `
  --region us-east-1

aws secretsmanager delete-secret `
  --secret-id inboxiq/google-oauth-client `
  --region us-east-1
```

{{% notice tip %}}
Mặc định, Secrets Manager giữ secret ở trạng thái "chờ xoá" 7-30 ngày trước khi xoá vĩnh viễn (để có thể khôi phục nếu xoá nhầm). Nếu muốn xoá ngay lập tức, thêm cờ `--force-delete-without-recovery` cho từng lệnh — cân nhắc kỹ vì thao tác này không thể hoàn tác.
{{% /notice %}}

![Secrets Manager - các secret trước khi xoá](/AWS-HUTECHintership-report/images/5-Workshop/5.6-Cleanup/cleanup-secrets-manager.jpg)

#### 5.6.5 Xoá S3 bucket chứa artifact deploy của SAM

`sam delete` ở bước 5.6.1 thường tự hỏi có xoá kèm bucket artifact không (`aws-sam-cli-managed-default-samclisourcebucket-...`). Nếu bỏ qua lúc đó, xoá thủ công:

1. Vào **S3 Console** → tìm bucket có tiền tố `aws-sam-cli-managed-default-samclisourcebucket-`.
2. **Empty** bucket trước (S3 không cho xoá bucket còn object) → sau đó **Delete** bucket.

![Danh sách S3 bucket artifact SAM trước khi xoá](/AWS-HUTECHintership-report/images/5-Workshop/5.6-Cleanup/cleanup-s3-bucket.jpg)

#### 5.6.6 Kiểm tra lại CloudWatch Logs

Log group thường tự xoá cùng Lambda function khi qua `sam delete`. Nếu còn sót (do được tạo thủ công hoặc retention chưa hết hạn), lọc theo tiền tố để xoá gọn:

1. Vào **CloudWatch Console** → **Log groups** → lọc theo `/aws/lambda/inboxiq-`.
2. Chọn tất cả → **Actions** → **Delete log group(s)**.


![Danh sách CloudWatch Log groups còn sót lại](/AWS-HUTECHintership-report/images/5-Workshop/5.6-Cleanup/cleanup-cloudwatch-logs.jpg)

#### 5.6.7 Bảng tổng hợp tài nguyên cần dọn

| # | Tài nguyên | Cách xoá | Mục |
|---|---|---|---|
| 1 | Lambda, API Gateway, DynamoDB, SQS, Layer | `sam delete` | 5.6.1 |
| 2 | Cognito User Pool | Xoá thủ công qua Console | 5.6.3 |
| 3 | Secret OpenAI API key | `aws secretsmanager delete-secret` | 5.6.4 |
| 4 | Secret Google OAuth Client | `aws secretsmanager delete-secret` | 5.6.4 |
| 5 | S3 bucket artifact SAM | Empty + Delete qua Console | 5.6.5 |
| 6 | CloudWatch Log groups còn sót | Xoá thủ công nếu cần | 5.6.6 |
