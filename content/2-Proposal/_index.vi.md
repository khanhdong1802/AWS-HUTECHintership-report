---
title: "Bản đề xuất"
date: 2026-07-04
weight: 2
chapter: false
pre: "  2.  "
---

# InboxIQ

## AI-powered email triage on Serverless AWS

### 1. Tóm tắt điều hành

InboxIQ là một trợ lý ảo thông minh được thiết kế nhằm giải quyết tình trạng quá tải email cho người dùng (sinh viên, nhân viên văn phòng). Ứng dụng di động (Flutter) tự động kết nối với Gmail API, sử dụng mô hình trí tuệ nhân tạo OpenAI (GPT-4o-mini) để tóm tắt nội dung, tự động phân loại danh mục và đánh giá mức độ ưu tiên của email theo thời gian thực. Hệ thống tận dụng hoàn toàn kiến trúc Serverless và Event-Driven trên nền tảng AWS (vận hành tại Region us-east-1) nhằm tối ưu hiệu năng xử lý bất đồng bộ, đảm bảo tính bảo mật nghiêm ngặt và tiết kiệm chi phí vận hành ở mức tối đa trong khuôn khổ chương trình thực tập First Cloud AI Journey (FCAJ) 2026.

### 2. Tuyên bố vấn đề

*Vấn đề hiện tại* Người dùng hiện đại thường xuyên đối mặt với việc nhận từ 50–100 email mỗi ngày, dẫn đến tiêu tốn nhiều thời gian lọc thông tin và dễ bỏ lỡ các email quan trọng. Các giải pháp quản lý email hiện tại thường mang tính thủ công, thiếu khả năng phân tích ngữ nghĩa sâu, hoặc các hệ thống tích hợp AI bên thứ ba có chi phí bản quyền quá cao và không đảm bảo quyền riêng tư dữ liệu khi xử lý tập trung.

*Giải pháp* InboxIQ xây dựng một hệ thống xử lý email tự động phân tán bằng kiến trúc Serverless Event-Driven. Khi người dùng yêu cầu, ứng dụng Flutter tương tác an toàn qua API Gateway (REST & WebSocket) được bảo mật bởi Amazon Cognito. Yêu cầu được đẩy vào hàng đợi Amazon SQS để kích hoạt AWS Lambda Worker xử lý bất đồng bộ (giảm tải cho client và tối ưu tài nguyên backend). Lambda Worker sẽ truy vấn Gmail OAuth token lưu tại DynamoDB, lấy dữ liệu email chưa đọc từ Gmail API, gửi tới OpenAI API để xử lý sinh dữ liệu (tóm tắt, phân loại), sau đó lưu kết quả vào DynamoDB (với cơ chế tự hủy TTL) và push kết quả trực tiếp về ứng dụng thời gian thực qua WebSocket. Do tài khoản AWS Free Tier bị giới hạn quyền truy cập Amazon Bedrock, giải pháp lựa chọn OpenAI API nhưng được thiết kế theo mô hình AI Provider Abstraction để dễ dàng chuyển đổi sang Bedrock trong tương lai.

*Lợi ích và hoàn vốn đầu tư (ROI)* Hệ thống giúp tiết kiệm tới 80% thời gian đọc và phân loại email hàng ngày cho người dùng nhờ vào các bản tóm tắt súc tích và cơ chế gắn nhãn ưu tiên thông minh. Việc áp dụng 100% AWS Serverless giúp loại bỏ hoàn toàn chi phí duy trì hạ tầng phần cứng cố định. Chi phí vận hành ước tính cực kỳ thấp (nằm trọn trong Free Tier của AWS cho các tài nguyên tính toán và lưu trữ), chỉ phát sinh chi phí token rất nhỏ từ OpenAI API. Thời gian hoàn vốn đầu tư (ROI) về mặt thời gian và hiệu suất làm việc của người dùng đạt được ngay trong tháng đầu tiên đưa vào sử dụng.

### 3. Kiến trúc giải pháp

Hệ thống được tổ chức thành 5 tầng (Layer) chức năng tách biệt theo mô hình Serverless Event-Driven, đảm bảo khả năng mở rộng cô lập và tính chịu lỗi cao:

```
LAYER 1 · USER        → Users, Mobile App (Flutter)
LAYER 2 · BACKEND     → Cognito, API Gateway (REST + WebSocket), Lambda (Producer + Worker)
LAYER 3 · WORKFLOW     → SQS (Main Queue + Dead-Letter Queue), EventBridge Scheduler (optional)
LAYER 4 · STORAGE      → DynamoDB (6 bảng), Systems Manager Parameter Store
LAYER 5 · MONITORING   → CloudWatch, X-Ray, IAM Role
EXTERNAL SERVICES      → Gmail API, OpenAI API (ngoài AWS Cloud)

```

*Dịch vụ AWS sử dụng* - *Amazon Cognito*: Quản lý định danh, xác thực người dùng và cấp phát mã thông báo JWT bảo mật.

* *Amazon API Gateway*: Cung cấp HTTP REST Endpoint cho các tác vụ đồng bộ và WebSocket Endpoint để duy trì kết nối hai chiều thời gian thực.
* *AWS Lambda*: Triển khai dưới dạng các hàm xử lý tính toán độc lập (Lambda Authorizer, Lambda Producer điều phối và Lambda Worker xử lý chính).
* *Amazon SQS*: Quản lý hàng đợi tin nhắn bất đồng bộ bao gồm `inboxiq-main` (xử lý chính với tính năng Partial Batch Response) và `inboxiq-dlq` (cơ chế dọn dẹp và cô lập tin nhắn lỗi khi retry > 3 lần).
* *Amazon DynamoDB*: Cơ sở dữ liệu NoSQL lưu trữ cấu hình người dùng, phiên kết nối WebSocket và lưu trữ bộ nhớ đệm dữ liệu tóm tắt email.
* *AWS Systems Manager Parameter Store*: Lưu trữ bảo mật (SecureString) các khóa cấu hình hệ thống và API Key của OpenAI.
* *Amazon EventBridge Scheduler*: Lên lịch tự động kích hoạt tiến trình kiểm tra email định kỳ sau mỗi 30 phút.
* *AWS CloudWatch & AWS X-Ray*: Thu thập log, giám sát chỉ số vận hành và theo dõi (tracing) luồng xử lý end-to-end của các hàm Lambda.

*Thiết kế thành phần* - *Ứng dụng Mobile (Flutter)*: Giao diện người dùng nhận diện và kết nối WebSocket, gửi yêu cầu phân tích và hiển thị kết quả real-time.

* *Tầng điều phối (Producer)*: Nhận request từ API Gateway, kiểm tra tính hợp lệ và đóng gói dữ liệu kèm theo `connectionId` vào SQS, phản hồi ngay lập tức trạng thái `202 Accepted` cho Client.
* *Tầng xử lý dữ liệu (Worker)*: Tiêu thụ tin nhắn từ SQS, thực hiện gọi API tích hợp ngoài (Gmail API & OpenAI API), lưu trữ dữ liệu vào database và đẩy trạng thái trực tiếp về client qua kênh kết nối WebSocket tương ứng.
* *Tầng lưu trữ*: Phân rã dữ liệu thành 6 bảng DynamoDB chuyên biệt giúp tối ưu hóa cấu trúc truy vấn NoSQL và quản lý vòng đời dữ liệu.

### 4. Triển khai kỹ thuật

*Các giai đoạn triển khai* Dự án được chia thành 4 giai đoạn chính, bám sát tiến độ báo cáo thực tập FCAJ 2026:

1. *Nghiên cứu bối cảnh & Phác thảo kiến trúc*: Đánh giá bài toán quá tải email, xây dựng tài liệu thiết kế hệ thống 5 tầng, lựa chọn giải pháp thay thế OpenAI và thực hiện các vòng review kiến trúc với Mentor hệ thống (Tháng 1).
2. *Tính toán chi phí & Thiết lập rào cản ngân sách*: Khởi tạo tài khoản AWS, thiết lập AWS Budgets chống vượt chi phí Free Tier, đặt hạn mức cứng (Hard Limit) trên OpenAI Platform (Tháng 1).
3. *Cấu hình hạ tầng & Phát triển Core Backend*: Tạo lập các tài nguyên lưu trữ (DynamoDB, SSM, SQS), cấu hình các endpoint API Gateway, thiết lập IAM Role theo nguyên tắc đặc quyền tối thiểu (Least Privilege), viết mã nguồn cho các hàm Lambda và xử lý bộ nhớ đệm (Global Scope caching) để tránh throttling (Tháng 2).
4. *Tích hợp Flutter, Kiểm thử & Đưa vào vận hành*: Hiện thực hóa luồng OAuth callback lấy Gmail token, xây dựng giao diện Flutter, kiểm thử end-to-end luồng đi của dữ liệu qua X-Ray và hoàn thiện báo cáo thực tập (Tháng 3).

*Yêu cầu kỹ thuật* - *Ứng dụng Client*: Phát triển bằng Flutter Mobile SDK, tích hợp Amplify/Cognito SDK phục vụ xác thực người dùng và sử dụng gói thư viện `web_socket_channel` để duy trì cổng giao tiếp thời gian thực.

* *Hạ tầng Backend*: Toàn bộ mã nguồn Lambda được viết bằng Node.js. Hàm Worker yêu cầu cấu hình thời gian xử lý (Timeout) tối đa là 5 phút và bắt buộc kích hoạt tính năng `ReportBatchItemFailures` của SQS để xử lý lỗi cục bộ trong một mẻ dữ liệu (Batch). Mã hóa toàn bộ dữ liệu nhạy cảm lưu trữ bằng khóa KMS mặc định của AWS.

### 5. Lộ trình & Mốc triển khai

* *Tháng 1 (Khởi động)*: Thiết kế kiến trúc, hoàn thiện sơ đồ luồng dữ liệu (Dataflow Diagram), đăng ký tài khoản AWS/OpenAI và hoàn thành thiết lập cấu hình an toàn ngân sách (AWS Budgets).
* *Tháng 2 (Xây dựng Core)*: Hoàn thành cấu hình 6 bảng DynamoDB, cấu hình API Gateway (REST & WebSocket kèm Authorizer). Triển khai mã nguồn Lambda Producer và Lambda Worker. Tích hợp thành công SQS và thiết lập cơ chế DLQ.
* *Tháng 3 (Tích hợp & Hoàn thiện)*: Xây dựng luồng đăng ký OAuth kết nối Gmail từ ứng dụng Flutter. Triển khai kết nối gọi API OpenAI. Thực hiện kiểm thử toàn diện, chụp lại toàn bộ minh chứng cấu hình trên AWS Console và tổng hợp tài liệu báo cáo thực tập FCAJ.

### 6. Ước tính ngân sách

Hạ tầng được thiết kế tối ưu hóa theo mô hình On-Demand để tận dụng triệt để gói AWS Free Tier, giảm thiểu rủi ro phát sinh chi phí ngoài ý muốn trong quá trình thực tập.

*Chi phí hạ tầng AWS (Ước tính hàng tháng)* - AWS Lambda: 0,00 USD (Nằm trong hạn mức miễn phí 1 triệu request/tháng).

* Amazon SQS & API Gateway: ~0,00 USD (Lượng request thử nghiệm MVP nhỏ hơn rất nhiều so với hạn mức Free Tier).
* Amazon DynamoDB: 0,00 USD (Lựa chọn chế độ On-Demand cấp phát tự động).
* AWS Systems Manager & AWS X-Ray: 0,00 USD.

*Chi phí dịch vụ tích hợp bên ngoài* - OpenAI API (GPT-4o-mini): ~1,00 - 2,00 USD/tháng (Dựa trên khối lượng dữ liệu test khoảng 50-100 email/ngày). Hạn mức chi tiêu tối đa (Hard Limit) được cấu hình khóa chặt ở mức **5,00 USD/tháng** để kiểm soát hoàn toàn rủi ro tài chính.

### 7. Đánh giá rủi ro

*Ma trận rủi ro* - Lỗi kết nối hoặc mất hiệu lực mã OAuth Token của Gmail: Ảnh hưởng Cao, Xác suất Trung bình.

* Vượt ngưỡng hạn mức gọi API (Throttling) từ phía OpenAI hoặc AWS SSM: Ảnh hưởng Trung bình, Xác suất Thấp.
* Phát sinh chi phí AWS ngoài tầm kiểm soát: Ảnh hưởng Cao, Xác suất Thấp.

*Chiến lược giảm thiểu* - Khóa Token: Xây dựng bảng lưu trữ trạng thái token cụ thể, thực hiện kiểm tra hiệu lực trước khi gọi và viết luồng tự động làm mới mã (Refresh Token flow).

* Throttling: Áp dụng cơ chế lưu trữ đệm (Caching) các khóa API bảo mật tại phạm vi biến toàn cục (Global Scope) của hàm Lambda để tránh việc truy vấn liên tục vào Parameter Store trên mỗi request. Sử dụng tính năng SQS Partial Batch Response để chỉ retry lại các email bị lỗi thay vì xử lý lại toàn bộ mẻ.
* Chi phí: Đặt bộ cảnh báo AWS Budgets tự động gửi email thông báo khi chi phí chạm mốc $5, $10, $20 và cấu hình Budget Action tự động thu hồi quyền của IAM User nếu chi phí chạm ngưỡng $50.

### 8. Kết quả kỳ vọng

*Cải tiến kỹ thuật*: Xây dựng thành công một hệ thống xử lý bất đồng bộ hoàn chỉnh, xử lý mượt mà luồng dữ liệu lớn mà không gây nghẽn kết nối của ứng dụng di động nhờ mô hình WebSocket phản hồi thời gian thực.
*Giá trị dài hạn*: Cung cấp một giải pháp nền tảng có thể tái sử dụng cho các bài toán xử lý ngôn ngữ tự nhiên (NLP) dựa trên kiến trúc Serverless, đồng thời phục vụ làm minh chứng thực tế, trực quan bằng hình ảnh (Console Screenshots) cho tập báo cáo thực tập FCAJ đạt chất lượng xuất sắc.