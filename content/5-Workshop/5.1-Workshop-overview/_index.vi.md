---
title : "Giới thiệu"
date : 2026-07-07 
weight : 1
chapter : false
pre : " <b> 5.1. </b> "
---
## Giới thiệu dự án

**InboxIQ** là trợ lý AI tóm tắt và phân loại email, giúp người dùng
(sinh viên, nhân viên văn phòng) nhận 50–100 email/ngày không có thời
gian đọc hết. Hệ thống tự động tóm tắt nội dung, phân loại mức độ ưu
tiên, và gợi ý hành động cho từng email.

- **Client:** Flutter (mobile app)
- **Kiến trúc:** Serverless, Event-Driven trên AWS
- **AI Provider:** OpenAI API (GPT-4o-mini)
- **AWS Region:** us-east-1

> **Vì sao dùng OpenAI thay vì Amazon Bedrock?** Tài khoản AWS Free Tier
> không được cấp quyền truy cập Bedrock. Hệ thống được thiết kế theo
> **AI Provider Abstraction** — tách riêng lớp gọi AI khỏi logic nghiệp
> vụ — để có thể chuyển sang Bedrock sau này mà không cần sửa lại kiến
> trúc tổng thể.

## Kiến trúc tổng quan (5 layer)

![Kiến trúc InboxIQ](/AWS-HUTECHintership-report/images/5-Workshop/5.1-Workshop-overview/architecture.png)

| Layer | Thành phần |
|---|---|
| **1. User** | Users, Web/Mobile App (Flutter) |
| **2. Backend** | Cognito, API Gateway (REST + WebSocket), Lambda (Producer + Worker) |
| **3. Workflow** | SQS (Main Queue + Dead-Letter Queue), EventBridge Scheduler (optional) |
| **4. Storage** | DynamoDB (6 bảng), Secrets Manager, Systems Manager Parameter Store |
| **5. Monitoring** | CloudWatch, X-Ray, IAM Role |
| **External** | Gmail API, OpenAI API (ngoài AWS Cloud) |

## Flow xử lý (14 bước)

| # | Từ → Đến | Mô tả |
|---|---|---|
| setup | App → API Gateway WebSocket | Connect kèm JWT → Lambda Authorizer verify → nhận `connectionId` |
| 1 | Users → App | Mở app, bấm "Check Gmail" |
| 2 | App → Cognito | Xác thực, nhận JWT |
| 3 | App → API Gateway REST | Gọi API kèm JWT + `connectionId` |
| 4 | API Gateway REST → Lambda Producer | Xác thực JWT, invoke Producer |
| 5 | Lambda Producer → SQS Main Queue | Đóng gói message (kèm connectionId), trả `202 Accepted` ngay |
| 6 | SQS Main Queue → Lambda Worker | Trigger xử lý bất đồng bộ (Partial Batch Response) |
| 7a | Lambda Worker → Secrets Manager | Lấy OpenAI API key (cache global scope) |
| 7b | Lambda Worker → DynamoDB | Query Gmail OAuth token theo UserID |
| 8 | Lambda Worker → Gmail API | Lấy danh sách email chưa đọc |
| 9 | Lambda Worker → OpenAI API | Tóm tắt + phân loại ưu tiên (timeout fail-fast) |
| 10 | Lambda Worker → DynamoDB | Ghi summary (bảng EmailSummaries, TTL 30 ngày) |
| 11 | Lambda Worker → WebSocket → App | Push kết quả real-time dùng connectionId |
| 13 | SQS Main → SQS DLQ | Khi retry > 3 lần |
| 14 | EventBridge Scheduler → SQS Main Queue | Trigger tự động mỗi 30 phút (optional) |

## Bảng dữ liệu (6 bảng DynamoDB)

| Bảng | Partition Key | Sort Key | TTL | Mục đích |
|---|---|---|---|---|
| `inboxiq-users` | `userId` | — | ❌ | Thông tin user cơ bản |
| `inboxiq-user-settings` | `userId` | — | ❌ | Cấu hình cá nhân hoá |
| `inboxiq-gmail-connections` | `userId` | — | ❌ | Gmail OAuth token |
| `inboxiq-email-summaries` | `userId` | `emailId` | ✅ 30 ngày | Bản tóm tắt email |
| `inboxiq-processing-jobs` | `jobId` | — | ✅ 7 ngày | Trạng thái xử lý (debug) |
| `inboxiq-ws-connections` | `connectionId` | — | ✅ 2 giờ | Mapping connectionId ↔ userId |

## Chi phí ước tính (Free Tier + OpenAI)

| Nguồn chi phí | /tháng ở quy mô demo |
|---|---|
| AWS (Cognito, API GW, Lambda, SQS, DynamoDB, SSM, CloudWatch, X-Ray, EventBridge) | ~$0 (trong free tier) |
| WebSocket connection-minute | ~$0.10 |
| OpenAI GPT-4o-mini | ~$0.30–1.00 |
| **Tổng** | **~$0.50–1.50/tháng** |

## Các bất cập cần lưu ý

1. Chưa có multi-region failover — chấp nhận cho MVP, nêu là hướng phát triển tiếp
2. Chưa có CI/CD pipeline — deploy bằng SAM CLI thủ công là đủ cho báo cáo
3. Rate limiting hiện chỉ có API Gateway throttle chung, chưa per-user usage plan