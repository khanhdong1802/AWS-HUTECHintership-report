---
title: "Các bài blogs đã dịch"
date: 2026-07-07
weight: 3
chapter: false
pre: " <b> 3. </b> "
---

Trong quá trình thực tập và học tập với AWS, em đã thực hiện dịch và tìm hiểu 3 bài blog liên quan đến các chủ đề quan trọng trong kiến trúc cloud hiện đại. Các bài blog tập trung vào cách thiết kế kết nối riêng tư đến Amazon S3, tối ưu phân phối nội dung bằng Amazon CloudFront và xây dựng data lake trong lĩnh vực y tế bằng kiến trúc microservices.

Việc dịch và phân tích các bài blog này giúp em hiểu rõ hơn cách các dịch vụ AWS được áp dụng vào những bài toán thực tế như bảo mật network, tăng tốc hiệu năng website, giảm tải hệ thống gốc, xử lý dữ liệu y tế và tổ chức kiến trúc phần mềm theo hướng linh hoạt, dễ mở rộng.

---

### [Blog 1 - Truy cập Amazon S3 riêng tư từ VPC bằng VPC Endpoint](3.1-Blog1/)

Blog này trình bày cách thiết kế mô hình truy cập **Amazon S3** từ một **EC2 Instance nằm trong private subnet** mà không cần đi qua Internet Gateway hoặc NAT Gateway. Nội dung chính tập trung vào việc sử dụng **Gateway VPC Endpoint for S3** để tạo đường truyền riêng tư giữa tài nguyên trong VPC và Amazon S3, từ đó tăng tính bảo mật, giảm phụ thuộc vào internet public và tối ưu chi phí vận hành.

Bài viết làm rõ bối cảnh khi một backend nội bộ cần upload log, export báo cáo hoặc backup dữ liệu lên S3 nhưng không nên được đặt trong public subnet. Thay vì mở đường ra internet thông qua NAT Gateway, hệ thống có thể cấu hình VPC Endpoint, route table, IAM Role, Endpoint Policy và Bucket Policy để kiểm soát đường đi của request cũng như quyền truy cập dữ liệu.

Qua blog này, em học được rằng VPC Endpoint chỉ giải quyết bài toán **network connectivity**, không tự động cấp quyền truy cập vào S3. Để hệ thống an toàn, cần kết hợp nhiều lớp bảo mật như IAM Role cho EC2, Endpoint Policy và Bucket Policy sử dụng điều kiện `aws:sourceVpce`. Đây là một ví dụ rõ ràng về tư duy **Defense in Depth** trong kiến trúc cloud.

Giá trị lớn nhất của bài blog là giúp em hiểu cách các tài nguyên trong private subnet vẫn có thể giao tiếp với dịch vụ AWS một cách an toàn, riêng tư và tối ưu nếu được thiết kế đúng. Kiến thức này có thể áp dụng cho các hệ thống backend, internal service, data processing hoặc batch job trong môi trường production.

---

### [Blog 2 - Amazon CloudFront: Tăng tốc phân phối nội dung từ Edge đến Origin](3.2-Blog2/)

Blog này giới thiệu **Amazon CloudFront**, dịch vụ CDN của AWS giúp phân phối nội dung nhanh hơn thông qua hệ thống edge location trên toàn cầu. Nội dung bài viết giải thích vì sao một website hoặc ứng dụng web có thể bị chậm khi mọi request đều đi trực tiếp về origin, đặc biệt khi người dùng truy cập từ nhiều khu vực địa lý khác nhau.

Bài viết mô tả cách CloudFront hoạt động theo luồng cơ bản: **User → CloudFront Edge Location → Origin**. Khi nội dung đã được cache tại edge location, CloudFront có thể phản hồi trực tiếp cho người dùng mà không cần gửi request về origin. Nếu xảy ra cache miss, CloudFront sẽ lấy dữ liệu từ origin, trả về cho người dùng và lưu lại bản sao để phục vụ các request sau.

Các use case được đề cập bao gồm website tĩnh, web application hiện đại, e-commerce, hệ thống media, API service và lớp bảo mật phía trước origin. Ngoài việc tăng tốc độ tải trang, CloudFront còn giúp giảm tải cho origin, hỗ trợ HTTPS, tùy chỉnh cache behavior và có thể kết hợp với **AWS WAF** để bảo vệ ứng dụng khỏi các request bất thường hoặc tấn công web phổ biến.

Qua blog này, em hiểu rằng CloudFront không chỉ dùng để phân phối ảnh hoặc file tĩnh, mà còn là một thành phần quan trọng trong kiến trúc cloud hiện đại. Dịch vụ này giúp hệ thống cải thiện hiệu năng, tăng độ ổn định, giảm tải backend và nâng cao bảo mật khi được đặt làm lớp trung gian giữa người dùng và origin.

---

### [Blog 3 - Bắt đầu với healthcare data lakes: Sử dụng microservices](3.3-Blog3/)

Blog này giới thiệu cách xây dựng **data lake trong lĩnh vực y tế** bằng cách áp dụng kiến trúc **microservices**. Nội dung chính xoay quanh việc thiết kế một hệ thống có khả năng lưu trữ, xử lý và phân tích nhiều loại dữ liệu y tế khác nhau trong khi vẫn đảm bảo tính bảo mật, khả năng bảo trì và khả năng mở rộng.

Bài viết giải thích rằng data lake là một kho lưu trữ tập trung, có thể chứa dữ liệu ở cả dạng thô và dạng đã xử lý để phục vụ phân tích. Trong bối cảnh y tế, dữ liệu có thể đến từ nhiều nguồn khác nhau như hồ sơ bệnh án, dữ liệu xét nghiệm, thiết bị y tế hoặc các message chuẩn HL7v2. Vì vậy, việc tách hệ thống thành nhiều microservice nhỏ giúp từng phần xử lý dữ liệu được đóng gói độc lập, dễ thay đổi và dễ mở rộng hơn.

Một điểm nổi bật của blog là mô hình **pub/sub hub**, trong đó các microservice giao tiếp với nhau thông qua message bất đồng bộ thay vì gọi trực tiếp lẫn nhau. Kiến trúc này giúp giảm phụ thuộc giữa các service, hỗ trợ event-driven architecture và làm cho hệ thống linh hoạt hơn khi cần bổ sung hoặc thay đổi connector xử lý dữ liệu.

Các thành phần AWS được đề cập bao gồm **Amazon S3** để lưu trữ dữ liệu, **Amazon DynamoDB** làm catalog, **AWS Lambda** để xử lý logic, **Amazon SNS** làm pub/sub hub, **Amazon API Gateway** làm front door, **Amazon Cognito** cho xác thực và ủy quyền, cùng **AWS Step Functions** để điều phối workflow xử lý ER7 message.

Qua blog này, em học được rằng microservices không chỉ là việc chia nhỏ code, mà là cách thiết kế ranh giới trách nhiệm giữa các thành phần trong hệ thống. Đối với bài toán healthcare data lake, microservices giúp hệ thống dễ bảo trì, dễ mở rộng, giảm cognitive load cho nhóm phát triển và phù hợp với các pipeline dữ liệu có nhiều định dạng phức tạp.

---

### Tổng kết chung

Thông qua 3 bài blog đã dịch, em nhận thấy các chủ đề tuy khác nhau nhưng đều có điểm chung là hướng đến việc xây dựng hệ thống cloud **an toàn hơn, linh hoạt hơn và có khả năng mở rộng tốt hơn**.

- Blog 1 giúp em hiểu cách bảo vệ kết nối giữa private subnet và Amazon S3 bằng VPC Endpoint.
- Blog 2 giúp em hiểu cách tăng tốc phân phối nội dung và giảm tải origin bằng Amazon CloudFront.
- Blog 3 giúp em hiểu cách dùng microservices để xây dựng healthcare data lake có khả năng xử lý dữ liệu phức tạp.

Những kiến thức này hỗ trợ em rất nhiều trong việc hình thành tư duy thiết kế kiến trúc cloud. Em không chỉ học cách sử dụng từng dịch vụ AWS riêng lẻ, mà còn hiểu hơn về cách các dịch vụ phối hợp với nhau để giải quyết các bài toán thực tế về network, security, performance, scalability và data processing.
