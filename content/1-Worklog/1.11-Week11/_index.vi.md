---
title: "Worklog Tuần 11"
date: 2026-06-29
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
---

### Mục tiêu trọng tâm tuần 11:

- Chuyển từ giai đoạn thiết kế sang giai đoạn **Xây dựng hệ thống (Build Project)**.
- Triển khai toàn bộ hạ tầng lưu trữ và cấu hình (Storage & Config Layer).
- Thiết lập hàng đợi tin nhắn bất đồng bộ với Amazon SQS.
- Lập trình và triển khai các hàm AWS Lambda (Node.js) đóng vai trò Producer và Worker.
- Cấu hình mạng lưới API Gateway (REST và WebSocket) để giao tiếp với Client.
- Tích hợp bảo mật Amazon Cognito và phân quyền IAM chặt chẽ.

---

### Kế hoạch hành động chi tiết:

| Thứ | Nội dung công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu tham khảo |
| --- | --- | --- | --- | --- |
| 2 | - Khởi tạo Amazon Cognito User Pool <br> - Cấu hình 6 bảng Amazon DynamoDB theo thiết kế <br> - Lưu API Keys vào SSM Parameter Store | 29/06/2026 | 29/06/2026 | AWS Console |
| 3 | - Khởi tạo Amazon SQS (`inboxiq-main` và `inboxiq-dlq`) <br> - Thiết lập IAM Role (Least Privilege) cho các Lambda function | 30/06/2026 | 30/06/2026 | AWS IAM & SQS Docs |
| 4 | - Viết mã nguồn (Node.js) cho Lambda Authorizer <br> - Viết mã nguồn Lambda Producer (nhận request, ném vào SQS) | 01/07/2026 | 01/07/2026 | AWS SDK for JavaScript v3 |
| 5 | - Viết mã nguồn Lambda Worker (xử lý logic chính) <br> - Cấu hình SQS trigger kích hoạt Lambda Worker | 02/07/2026 | 02/07/2026 | Node.js / AWS SDK |
| 6 | - Cấu hình Amazon API Gateway (REST) tích hợp với Lambda Producer <br> - Cấu hình API Gateway (WebSocket) tích hợp luồng trả kết quả | 03/07/2026 | 03/07/2026 | AWS API Gateway Docs |
| 7 | - Thực hiện test kết nối nội bộ (Unit test) giữa API Gateway -> SQS -> Lambda <br> - Áp dụng cơ chế Global Caching trong Lambda | 04/07/2026 | 04/07/2026 | Postman / wscat |
| CN | - Review lại log trên CloudWatch để kiểm tra lỗi <br> - Tối ưu hóa thời gian timeout của Lambda <br> - Viết báo cáo tuần 11 | 05/07/2026 | 05/07/2026 | AWS CloudWatch |

---

### Tổng kết kết quả thu hoạch:

#### Về mặt kiến thức & Vận hành
- Nắm vững quy trình kết nối các dịch vụ AWS với nhau thông qua cơ chế Event-Driven (Sự kiện kích hoạt Sự kiện).
- Hiểu được tầm quan trọng của việc gán IAM Role chính xác: Lambda Producer chỉ được quyền `sqs:SendMessage`, trong khi Lambda Worker cần quyền `sqs:ReceiveMessage` và `dynamodb:PutItem`.
- Nắm được kỹ thuật tối ưu hóa Lambda: Lưu trữ các tham số từ SSM vào biến global ngoài hàm `handler()` để tận dụng tính năng tái sử dụng execution context (Warm start), giúp giảm số lần gọi API SSM gây nghẽn (throttling).

#### Về mặt thực hành
- Tạo thành công cụm Cognito User Pool phục vụ xác thực.
- Deploy thành công 6 bảng DynamoDB với cơ chế tự hủy dữ liệu TTL (Time to Live) để tự động xóa log cũ sau 30 ngày, tiết kiệm dung lượng.
- Code và deploy thành công cụm Lambda bằng Node.js.
- Cấu hình thành công cơ chế `ReportBatchItemFailures` của SQS, giúp Lambda Worker chỉ xử lý lại đúng email bị lỗi thay vì phải chạy lại cả một mẻ (batch) tin nhắn.
- Dùng công cụ `wscat` (trên terminal) để test lập kênh kết nối WebSocket qua API Gateway thành công.

---

### Các trở ngại và thách thức đối mặt:
- Lỗi CORS (Cross-Origin Resource Sharing) liên tục xuất hiện khi test REST API Gateway qua trình duyệt.
- Quên không cấp quyền `execute-api:ManageConnections` cho Lambda Worker nên hàm này không thể push data ngược về API Gateway WebSocket, dẫn đến lỗi 403 Forbidden.
- Lambda Worker bị Timeout do mặc định của AWS chỉ là 3 giây, trong khi việc gọi API OpenAI và Gmail tốn nhiều thời gian hơn.

### Phương án giải quyết và bài học rút ra:
- Kích hoạt tính năng Enable CORS trên API Gateway và tinh chỉnh lại header trả về từ Lambda Producer (`Access-Control-Allow-Origin: '*'`).
- Bổ sung ngay quyền IAM `execute-api:ManageConnections` vào Role của Lambda Worker.
- Tăng cấu hình Timeout của Lambda Worker lên 5 phút (300 giây) để đảm bảo có đủ thời gian chờ OpenAI sinh dữ liệu tóm tắt email.

### Định hướng tuần tiếp theo:
- Tích hợp ứng dụng Mobile (Flutter) với Backend AWS.
- Xử lý luồng kết nối với External API: Gmail API (OAuth) và OpenAI API (GPT-4o-mini).
- Kiểm thử toàn hệ thống (End-to-End Testing), gỡ lỗi (Fixing) và chuẩn bị dữ liệu báo cáo nghiệm thu thực tập FCAJ.