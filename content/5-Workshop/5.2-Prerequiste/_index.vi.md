---
title : "Các bước chuẩn bị"
date : 2026-07-07 
weight : 2
chapter : false
pre : " <b> 5.2. </b> "
---
## Vì sao bước này quan trọng

Trước khi viết bất kỳ dòng code Lambda nào, cần dựng sẵn "lưới an toàn" chi phí —
vì OpenAI API là chi phí biến động duy nhất trong kiến trúc, và một lỗi loop
trong Lambda có thể vô tình gọi API liên tục, phát sinh phí ngoài ý muốn.

## 1. AWS Budgets Alert

Tạo 1 budget $20/tháng kèm 4 mức cảnh báo tại 10%, 25%, 50%, 100%.

![Budget alert](/AWS-HUTECHintership-report/images/5-Workshop/5.2-Prerequisite/budget-created.jpg)

## 2. OpenAI Spend Limit

Đặt project spend limit $5/tháng, kèm alert ở $2.50.

![OpenAI limit](/AWS-HUTECHintership-report/images/5-Workshop/5.2-Prerequisite/openai-limit.jpg)

## 3. Cài đặt công cụ local

| Công cụ | Phiên bản |
|---|---|
| AWS CLI | 2.35.16 |
| Node.js | v24.14.1 |
| AWS SAM CLI | 1.163.0 |
| Flutter | 3.38.2 |

## 4. Tạo IAM User riêng (không dùng root)

AWS khuyến nghị không tạo access key cho root account vì quyền không giới hạn
và không thể thu hẹp phạm vi. Tạo IAM user riêng `inboxiq-dev`, gắn các policy:
`AWSLambda_FullAccess`, `AmazonSQSFullAccess`, `AmazonDynamoDBFullAccess`,
`AmazonAPIGatewayAdministrator`, `SecretsManagerReadWrite`, `CloudWatchFullAccess`,
`IAMFullAccess`.

![IAM user](/AWS-HUTECHintership-report/images/5-Workshop/5.2-Prerequisite/iam-user-created.jpg)

## 5. Cấu hình AWS CLI

```bash
aws configure
aws sts get-caller-identity
```

Xác nhận `Arn` trả về đúng `user/inboxiq-dev`, không phải root — đảm bảo
terminal đang thao tác bằng IAM user vừa tạo.

![CLI configured](/AWS-HUTECHintership-report/images/5-Workshop/5.2-Prerequisite/cli-verify.jpg)