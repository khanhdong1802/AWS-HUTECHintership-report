---
title: "Worklog Tuần 10"
date: 2026-06-22
weight: 10
chapter: false
pre: " <b> 1.10. </b> "
---

### Mục tiêu trọng tâm tuần 10:

- Khởi động dự án cuối khóa: **InboxIQ - AI-powered email triage**.
- Đánh giá và chốt bản đề xuất dự án (Project Proposal), xác định rõ bài toán và giải pháp Serverless.
- Nghiên cứu chuyên sâu các dịch vụ AWS sẽ sử dụng trong kiến trúc: API Gateway (REST & WebSocket), Amazon SQS, AWS Lambda, DynamoDB, và Cognito.
- Thiết kế và vẽ sơ đồ kiến trúc hệ thống (Architecture Diagram) & Sơ đồ luồng dữ liệu (Dataflow Diagram) cho hệ thống 5 tầng.
- Lập bảng tính toán chi phí (Cost Estimation) dựa trên AWS Free Tier và thiết lập AWS Budgets để tạo rào cản ngân sách an toàn.
- Chuẩn bị tài khoản và môi trường để sẵn sàng cho việc triển khai hạ tầng ở tuần tiếp theo.

---

### Kế hoạch hành động chi tiết:

| Thứ | Nội dung công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu tham khảo |
| --- | --- | --- | --- | --- |
| 2 | - Họp nhóm chốt bản đề xuất dự án InboxIQ <br> - Phân tích bài toán quá tải email và định hình giải pháp Event-Driven | 22/06/2026 | 22/06/2026 | Bản đề xuất dự án InboxIQ |
| 3 | - Tìm hiểu cơ chế hoạt động của API Gateway WebSocket phục vụ real-time <br> - Nghiên cứu Amazon SQS (Main & Dead-Letter Queue) | 23/06/2026 | 23/06/2026 | AWS Documentation (API Gateway, SQS) |
| 4 | - Tìm hiểu Amazon Cognito cho xác thực <br> - Nghiên cứu cách Lambda kết nối an toàn với SSM Parameter Store | 24/06/2026 | 24/06/2026 | AWS Documentation (Cognito, SSM) |
| 5 | - Vẽ sơ đồ kiến trúc tổng thể (Architecture Diagram) <br> - Phác thảo luồng đi của dữ liệu (Dataflow Diagram) từ Client đến OpenAI | 25/06/2026 | 25/06/2026 | Draw.io / Lucidchart |
| 6 | - Phân rã dữ liệu, thiết kế cấu trúc 6 bảng DynamoDB <br> - Xác định các Partition Key và Sort Key cần thiết | 26/06/2026 | 26/06/2026 | AWS DynamoDB Best Practices |
| 7 | - Lập bảng tính toán chi phí hệ thống (Cost Estimation) <br> - Cấu hình AWS Budgets và Hard Limit trên OpenAI ($5.00) | 27/06/2026 | 27/06/2026 | AWS Pricing Calculator |
| CN | - Review lại toàn bộ thiết kế kiến trúc với Mentor <br> - Chốt phương án triển khai cho tuần 11 <br> - Viết báo cáo tuần 10 | 28/06/2026 | 28/06/2026 | Tài liệu nội bộ nhóm |

---

### Tổng kết kết quả thu hoạch:

#### Về mặt kiến thức & Thiết kế
- Hoàn thiện bản **Project Proposal** cho InboxIQ, xác định rõ ROI và các rủi ro hệ thống.
- Hiểu rõ lý do tại sao phải dùng **Amazon SQS** làm vùng đệm (buffer): giúp tách rời (decouple) tiến trình nhận request từ người dùng và tiến trình gọi API của OpenAI (vốn mất nhiều thời gian), tránh tình trạng timeout ở client.
- Nắm bắt cơ chế duy trì kết nối hai chiều của **API Gateway WebSocket** để push ngược kết quả từ AWS Lambda về ứng dụng di động mà không cần client phải "polling" liên tục.
- Thiết kế hoàn chỉnh sơ đồ kiến trúc 5 tầng (User, Backend, Workflow, Storage, Monitoring).
- Lên chiến lược lưu trữ bảo mật các khóa API nhạy cảm (OpenAI Key, Gmail Client Secret) vào **SSM Parameter Store** thay vì hard-code trong mã nguồn Lambda.

#### Về mặt thực hành
- Thiết kế xong lược đồ cơ sở dữ liệu NoSQL (Schema) cho 6 bảng DynamoDB chuyên biệt (User, Session, EmailMetadata, v.v.).
- Hoàn thành bản ước tính chi phí. Nhờ kiến trúc On-Demand 100%, dự kiến chi phí AWS bằng $0.00 (nằm hoàn toàn trong Free Tier).
- Kích hoạt thành công các bộ cảnh báo ngân sách AWS Budgets (ngưỡng $5, $10) để chống vượt rào chi phí.

---

### Các trở ngại và thách thức đối mặt:
- Việc thiết kế luồng kết nối WebSocket khá phức tạp về mặt logic, đặc biệt là cách lưu trữ `connectionId` vào DynamoDB để Lambda Worker biết đường trả kết quả về đúng thiết bị của user.
- Cần phải đắn đo nhiều khi thiết kế Partition Key cho DynamoDB để tránh tình trạng "Hot Partition" khi lượng truy vấn dồn vào một user cụ thể.
- Lúng túng trong việc xác định các tham số cho Dead-Letter Queue (DLQ) như số lần retry tối đa trước khi đưa message vào khu vực cách ly.

### Phương án giải quyết và bài học rút ra:
- Vẽ luồng Sequence Diagram (Biểu đồ tuần tự) chi tiết cho quá trình Handshake của WebSocket để team dễ hình dung cách `connectionId` được tạo và lưu.
- Đọc thêm tài liệu thiết kế NoSQL của AWS, quyết định sử dụng tổ hợp `userId` và `messageId` làm khóa phân vùng để dàn đều dữ liệu.
- Thiết lập số lần retry (`maxReceiveCount`) của SQS là 3; nếu Lambda Worker xử lý lỗi 3 lần liên tiếp, message sẽ tự động bị đẩy sang DLQ để tránh loop vô hạn gây tốn tiền.

### Định hướng tuần tiếp theo:
- Bắt tay vào "thực chiến": Triển khai cấu hình hạ tầng Backend trên AWS.
- Khởi tạo Cognito, DynamoDB, SSM Parameter Store.
- Viết code Node.js cho cụm Lambda (Producer & Worker) và cấu hình hàng đợi SQS.