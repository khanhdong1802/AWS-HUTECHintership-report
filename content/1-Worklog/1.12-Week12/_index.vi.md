---
title: "Worklog Tuần 12"
date: 2026-07-06
weight: 12
chapter: false
pre: " <b> 1.12. </b> "
---

### Mục tiêu trọng tâm tuần 12:

- Hoàn thiện luồng **Tích hợp, Kiểm thử và Chỉnh sửa (Integration, Test and Fix)** cho dự án InboxIQ.
- Tích hợp giao diện Frontend (ứng dụng Flutter) với hệ thống Backend AWS (Cognito, API Gateway).
- Kết nối thành công với các dịch vụ bên ngoài: Gmail API (luồng OAuth 2.0) và OpenAI API (xử lý NLP).
- Thực hiện kiểm thử đầu cuối (End-to-End E2E Testing) để đánh giá độ trễ và khả năng chịu tải.
- Sử dụng AWS X-Ray để truy vết (tracing) luồng đi của dữ liệu và gỡ lỗi.
- Thu thập ảnh chụp minh chứng (Console Screenshots) và tổng hợp báo cáo nghiệm thu thực tập FCAJ 2026.

---

### Kế hoạch hành động chi tiết:

| Thứ | Nội dung công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu tham khảo |
| --- | --- | --- | --- | --- |
| 2 | - Code ứng dụng Flutter: Tích hợp thư viện Amplify/Cognito SDK <br> - Xây dựng luồng đăng nhập và lấy JWT Token | 06/07/2026 | 06/07/2026 | Flutter Docs / AWS Amplify |
| 3 | - Tích hợp Gmail API: Xử lý luồng OAuth callback lấy Access Token <br> - Lưu trữ an toàn Gmail token của user vào DynamoDB | 07/07/2026 | 07/07/2026 | Google Workspace API Docs |
| 4 | - Tích hợp OpenAI API: Cấu hình prompt cho GPT-4o-mini <br> - Lập trình luồng trích xuất, tóm tắt và dán nhãn ưu tiên email | 08/07/2026 | 08/07/2026 | OpenAI API Reference |
| 5 | - Tích hợp WebSocket trên Flutter (dùng package `web_socket_channel`) <br> - Kết nối luồng hoàn chỉnh: App -> REST API -> SQS -> Lambda -> OpenAI -> WebSocket -> App | 09/07/2026 | 09/07/2026 | Tài liệu nội bộ nhóm |
| 6 | - Tiến hành Test & Fix: Gửi thử nghiệm một batch 50 email <br> - Kích hoạt AWS X-Ray để trace log, tối ưu hóa các nút thắt cổ chai | 10/07/2026 | 10/07/2026 | AWS X-Ray Docs |
| 7 | - Hoàn thiện các góc cạnh của ứng dụng (UI/UX) <br> - Chụp ảnh màn hình các tài nguyên AWS, các log CloudWatch thành công để làm minh chứng | 11/07/2026 | 11/07/2026 | Ứng dụng InboxIQ |
| CN | - Dọn dẹp tài nguyên nháp không cần thiết <br> - Hoàn thiện toàn bộ báo cáo thực tập FCAJ 2026 <br> - Chuẩn bị nội dung thuyết trình nghiệm thu | 12/07/2026 | 12/07/2026 | Form báo cáo FCAJ |

---

### Tổng kết kết quả thu hoạch:

#### Về mặt kỹ thuật & Tích hợp
- Thành công "ghép nối" toàn bộ các mảnh ghép: Ứng dụng di động Flutter đã giao tiếp mượt mà với AWS Serverless thông qua mã thông báo JWT của Cognito.
- Giải quyết êm đẹp bài toán bất đồng bộ: Người dùng bấm "Tóm tắt Email", ứng dụng ngay lập tức hiển thị trạng thái "Đang xử lý..." (nhờ phản hồi 202 từ REST API). Sau vài giây, hệ thống tự động push kết quả phân loại từ OpenAI qua WebSocket khiến UI cập nhật theo thời gian thực mà không bị đơ app.
- Kịch bản tích hợp AI Provider Abstraction hoạt động tốt, mã nguồn Node.js tương tác trơn tru với GPT-4o-mini thông qua các biến môi trường và khóa lấy từ SSM.

#### Về mặt Kiểm thử & Đánh giá (Test & Fix)
- Luồng X-Ray Tracing giúp team nhìn rõ được thời gian tiêu tốn ở từng chặng (VD: SQS mất 50ms, gọi Gmail mất 400ms, gọi OpenAI mất 2.5s). Từ đó có cái nhìn thực tế về độ trễ hệ thống.
- Kiểm tra tính năng chịu lỗi (Fault tolerance): Khi chủ động truyền sai Gmail API Key, hệ thống đưa message vào DLQ (Dead-Letter Queue) chính xác sau 3 lần thử lại, chứng minh độ tin cậy của kiến trúc.
- Chi phí vận hành kiểm thử thực tế hoàn toàn nằm trong quỹ an toàn (chỉ tốn khoảng 0.8 USD cho tiền token OpenAI trong suốt quá trình test, AWS Cost = 0$).

---

### Các trở ngại và thách thức đối mặt:
- Việc duy trì kết nối WebSocket trên thiết bị di động đôi khi bị rớt mạng chập chờn khi thiết bị chuyển từ Wifi sang 4G.
- Mã thông báo Access Token của Gmail hết hạn (expire) khá nhanh, dẫn đến lỗi xác thực khi Lambda Worker cố gắng tải email.
- Parse (phân tích) kết quả trả về từ OpenAI gặp lỗi vì đôi khi AI sinh ra định dạng không chuẩn JSON như mong đợi.

### Phương án giải quyết và bài học rút ra:
- **WebSocket:** Code thêm cơ chế tự động kết nối lại (Auto-reconnect logic) trên ứng dụng Flutter khi phát hiện tín hiệu socket bị đóng ngắt quãng.
- **Gmail OAuth:** Viết bổ sung luồng tự động làm mới mã (Refresh Token flow) trên Lambda Worker. Khi Access Token báo lỗi 401, Lambda sẽ dùng Refresh Token lấy mã mới trước khi thử lại việc đọc email.
- **OpenAI:** Cấu hình tham số `response_format: { type: "json_object" }` trong payload gửi tới OpenAI và tinh chỉnh lại System Prompt để ép AI trả về cấu trúc JSON nghiêm ngặt, tránh lỗi parse.

### Đánh giá tổng kết dự án InboxIQ:
- Dự án đã hoàn thành xuất sắc các mục tiêu đề ra trong bản Đề xuất tuần 10.
- Giải quyết thành công bài toán quá tải email bằng AI với kiến trúc 100% Serverless, Event-Driven.
- Đảm bảo tuân thủ nguyên tắc bảo mật thông tin (Least Privilege, KMS Encryption, mã hóa Token).
- Hệ thống sẵn sàng cho buổi nghiệm thu đánh giá của chương trình First Cloud AI Journey (FCAJ) 2026 với bộ hồ sơ báo cáo, diagram và ảnh minh chứng hoàn chỉnh. Cảm ơn chặng đường 12 tuần đầy nỗ lực!