---
title: "Workshop"
date: 2026-07-07
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Xây dựng ứng dụng InboxIQ trên nền tảng AWS Serverless

#### Tổng quan

**InboxIQ** là một ứng dụng quản lý email thông minh được phát triển trên nền tảng **AWS Serverless**, kết hợp với các dịch vụ trí tuệ nhân tạo nhằm hỗ trợ người dùng đọc, phân tích và tóm tắt nội dung email một cách nhanh chóng. Thay vì sử dụng mô hình máy chủ truyền thống, hệ thống tận dụng các dịch vụ được quản lý hoàn toàn của AWS để giảm chi phí vận hành, tăng khả năng mở rộng và đơn giản hóa quá trình triển khai.

Trong workshop này, chúng ta sẽ từng bước xây dựng và triển khai Backend Serverless bằng **AWS Serverless Application Model (AWS SAM)**. Đồng thời, chúng ta sẽ cấu hình và tích hợp các dịch vụ quan trọng của AWS như **Amazon API Gateway**, **AWS Lambda**, **Amazon Cognito**, **Amazon DynamoDB**, **Amazon SQS**, **AWS Secrets Manager** và **Amazon CloudWatch** nhằm tạo nên một hệ thống hoàn chỉnh có khả năng xác thực người dùng, xử lý dữ liệu, lưu trữ thông tin và giám sát hoạt động của ứng dụng.

Bên cạnh các dịch vụ của AWS, workshop còn hướng dẫn cách tích hợp **Gmail OAuth 2.0** để người dùng cấp quyền truy cập hộp thư Gmail một cách an toàn. Sau khi được cấp quyền, hệ thống sẽ sử dụng **Gmail API** để lấy nội dung email, kết hợp với **OpenAI API** để thực hiện tóm tắt, phân loại và phân tích thông tin, từ đó giúp người dùng dễ dàng theo dõi các email quan trọng mà không cần đọc toàn bộ nội dung.

Thông qua workshop này, người học sẽ hiểu được quy trình triển khai một ứng dụng Serverless thực tế trên AWS, từ khâu chuẩn bị môi trường phát triển, triển khai hạ tầng bằng Infrastructure as Code, xây dựng các API xử lý nghiệp vụ, tích hợp dịch vụ bên thứ ba cho đến kiểm thử và vận hành hệ thống. Đây cũng là cơ hội để làm quen với kiến trúc Event-Driven và những phương pháp phát triển ứng dụng hiện đại trên nền tảng điện toán đám mây AWS.

#### Nội dung

1. [Tổng quan về Workshop](5.1-Workshop-overview/)
2. [Chuẩn bị môi trường](5.2-Prerequiste/)
3. [Triển khai Backend Serverless trên AWS](5.3-Backend/)
4. [Tích hợp Gmail OAuth](5.4-Gmail-OAuth/)
5. [VPC Endpoint Policies](5.5-Flutter-app/)
6. [Dọn dẹp tài nguyên](5.6-Cleanup/)