---

title: "Các bài blog đã dịch"
date: 2026-07-07
weight: 3
chapter: false
pre: " <b> 3. </b> "
--------------------

Trong quá trình thực tập và tìm hiểu về AWS, em đã dịch và nghiên cứu ba bài blog liên quan đến những chủ đề quan trọng trong kiến trúc cloud hiện đại: kết nối riêng tư đến Amazon S3, phân phối nội dung bằng Amazon CloudFront và xây dựng data lake y tế theo kiến trúc microservices.

Quá trình này giúp em hiểu rõ hơn cách các dịch vụ AWS được kết hợp để giải quyết những bài toán thực tế về bảo mật mạng, hiệu năng hệ thống, khả năng mở rộng và xử lý dữ liệu.

---

### [Blog 1 - Truy cập Amazon S3 riêng tư từ VPC bằng VPC Endpoint](3.1-Blog1/)

Bài blog trình bày cách một **EC2 Instance trong private subnet** có thể truy cập **Amazon S3** mà không cần sử dụng Internet Gateway hoặc NAT Gateway.

Giải pháp chính là sử dụng **Gateway VPC Endpoint for S3** để tạo đường truyền riêng tư giữa tài nguyên trong VPC và Amazon S3. Mô hình này phù hợp với các backend nội bộ cần tải log, lưu bản sao dự phòng hoặc xuất báo cáo lên S3 nhưng không nên kết nối trực tiếp với internet công cộng.

Để triển khai an toàn, hệ thống cần kết hợp:

* Gateway VPC Endpoint.
* Route Table.
* IAM Role gắn với EC2.
* Endpoint Policy.
* Bucket Policy.

Qua bài blog, em hiểu rằng VPC Endpoint chỉ cung cấp đường kết nối mạng riêng tư, không tự động cấp quyền truy cập dữ liệu. Quyền truy cập vẫn phải được kiểm soát bằng IAM và Bucket Policy, có thể kết hợp điều kiện `aws:sourceVpce` để chỉ cho phép request đi qua endpoint đã chỉ định.

Bài viết giúp em hiểu rõ hơn về mô hình **Defense in Depth**, trong đó hệ thống được bảo vệ bằng nhiều lớp thay vì phụ thuộc vào một cơ chế duy nhất.

---

### [Blog 2 - Amazon CloudFront: Tăng tốc phân phối nội dung từ Edge đến Origin](3.2-Blog2/)

Bài blog giới thiệu **Amazon CloudFront**, dịch vụ Content Delivery Network của AWS giúp phân phối nội dung đến người dùng thông qua hệ thống edge location trên toàn cầu.

Luồng hoạt động cơ bản của CloudFront gồm:

**User → CloudFront Edge Location → Origin**

Khi nội dung đã được lưu tại edge location, CloudFront có thể phản hồi trực tiếp cho người dùng mà không cần gửi request về origin. Nếu nội dung chưa được cache, CloudFront sẽ lấy dữ liệu từ origin, trả về cho người dùng và lưu lại để phục vụ các request tiếp theo.

CloudFront có thể được áp dụng cho nhiều loại hệ thống như:

* Website tĩnh.
* Web application.
* Hệ thống thương mại điện tử.
* Dịch vụ media.
* API.
* Các ứng dụng có người dùng ở nhiều khu vực địa lý.

Ngoài việc cải thiện tốc độ tải nội dung, CloudFront còn giúp giảm tải cho origin, hỗ trợ HTTPS, tùy chỉnh cache behavior và tích hợp với **AWS WAF** để tăng cường bảo mật.

Qua bài blog, em nhận thấy CloudFront không chỉ là công cụ phân phối file tĩnh mà còn là một lớp quan trọng giữa người dùng và hệ thống phía sau, giúp cải thiện hiệu năng, khả năng chịu tải và độ an toàn của ứng dụng.

---

### [Blog 3 - Bắt đầu với Healthcare Data Lakes bằng kiến trúc Microservices](3.3-Blog3/)

Bài blog trình bày cách xây dựng **healthcare data lake** bằng kiến trúc **microservices**, nhằm lưu trữ, xử lý và phân tích nhiều loại dữ liệu y tế khác nhau.

Dữ liệu y tế có thể đến từ nhiều nguồn như:

* Hồ sơ bệnh án.
* Kết quả xét nghiệm.
* Thiết bị y tế.
* Dữ liệu lâm sàng.
* Message theo chuẩn HL7v2.

Việc chia hệ thống thành các microservice độc lập giúp mỗi thành phần tập trung vào một nhiệm vụ cụ thể, dễ bảo trì và có thể mở rộng riêng khi khối lượng dữ liệu tăng lên.

Một điểm quan trọng của kiến trúc là mô hình **publish/subscribe**, trong đó các service trao đổi dữ liệu thông qua message bất đồng bộ thay vì gọi trực tiếp lẫn nhau. Cách tiếp cận này giúp giảm phụ thuộc giữa các thành phần và phù hợp với các hệ thống event-driven.

Các dịch vụ AWS được sử dụng trong kiến trúc gồm:

* **Amazon S3** để lưu trữ dữ liệu.
* **Amazon DynamoDB** để quản lý catalog.
* **AWS Lambda** để xử lý logic.
* **Amazon SNS** làm hệ thống publish/subscribe.
* **Amazon API Gateway** làm cổng truy cập API.
* **Amazon Cognito** để xác thực và phân quyền.
* **AWS Step Functions** để điều phối quy trình xử lý dữ liệu.

Qua bài blog, em hiểu rằng microservices không chỉ đơn thuần là chia nhỏ mã nguồn mà còn là quá trình xác định rõ trách nhiệm, phạm vi và cách giao tiếp của từng thành phần trong hệ thống.

---

### Tổng kết chung

Ba bài blog đề cập đến những bài toán khác nhau nhưng đều hướng đến mục tiêu xây dựng hệ thống cloud an toàn, linh hoạt và có khả năng mở rộng.

* Blog 1 giúp em hiểu cách tạo kết nối riêng tư giữa VPC và Amazon S3.
* Blog 2 giúp em hiểu cách CloudFront tăng tốc phân phối nội dung và giảm tải cho origin.
* Blog 3 giúp em hiểu cách áp dụng microservices và event-driven architecture vào hệ thống xử lý dữ liệu y tế.

Thông qua quá trình dịch và phân tích, em không chỉ tìm hiểu cách sử dụng từng dịch vụ AWS riêng lẻ mà còn hiểu rõ hơn cách chúng phối hợp với nhau để giải quyết các yêu cầu về **network, security, performance, scalability và data processing** trong một kiến trúc cloud thực tế.
