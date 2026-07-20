---
title: "Worklog Tuần 9"
date: 2026-06-15
weight: 9
chapter: false
pre: " <b> 1.9. </b> "
---

### Mục tiêu tuần 9:

- Tìm hiểu tổng quan về AWS Command Line Interface (AWS CLI).
- Hiểu vai trò của AWS CLI trong việc quản lý tài nguyên AWS.
- Cài đặt và cấu hình AWS CLI trên máy tính.
- Thực hành kiểm tra và quản lý tài nguyên bằng AWS CLI.
- Thực hành làm việc với Amazon S3 thông qua AWS CLI.
- Tìm hiểu và sử dụng Amazon SNS bằng AWS CLI.
- Thực hành quản lý IAM bằng AWS CLI.
- Tìm hiểu cách quản lý Amazon VPC bằng AWS CLI.
- Khởi tạo và quản lý Amazon EC2 bằng AWS CLI.
- Thực hiện dọn dẹp tài nguyên sau khi hoàn thành bài thực hành.

---

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Đọc tổng quan workshop AWS CLI <br> - Tìm hiểu vai trò của AWS CLI <br> - Chuẩn bị môi trường thực hành | 15/06/2026 | 15/06/2026 | https://000011.awsstudygroup.com/1-introduction/ |
| 3 | - Cài đặt AWS CLI <br> - Cấu hình Access Key và Region <br> - Kiểm tra kết nối AWS CLI | 16/06/2026 | 16/06/2026 | https://000011.awsstudygroup.com/2-prerequiste/ <br> https://000011.awsstudygroup.com/3-installation/ |
| 4 | - Kiểm tra thông tin tài nguyên AWS <br> - Thực hành các lệnh AWS CLI cơ bản | 17/06/2026 | 17/06/2026 | https://000011.awsstudygroup.com/4-check-resource/ |
| 5 | - Thực hành quản lý Amazon S3 bằng AWS CLI <br> - Tạo Bucket <br> - Upload và Download dữ liệu | 18/06/2026 | 18/06/2026 | https://000011.awsstudygroup.com/5-cli-with-s3/ |
| 6 | - Thực hành Amazon SNS bằng AWS CLI <br> - Tạo Topic <br> - Gửi và kiểm tra Notification | 19/06/2026 | 19/06/2026 | https://000011.awsstudygroup.com/6-cli-with-sns/ |
| 7 | - Quản lý IAM bằng AWS CLI <br> - Làm việc với Amazon VPC <br> - Khởi tạo EC2 bằng AWS CLI | 20/06/2026 | 20/06/2026 | https://000011.awsstudygroup.com/7-cli-with-iam/ <br> https://000011.awsstudygroup.com/8-cli-with-vpc/ <br> https://000011.awsstudygroup.com/9-create-ec2/ |
| CN | - Kiểm tra các tài nguyên đã tạo <br> - Thực hiện Cleanup Resources <br> - Tổng kết kiến thức đã học | 21/06/2026 | 21/06/2026 | https://000011.awsstudygroup.com/11-clean-up/ |

---

### Kết quả đạt được tuần 9:

#### Kiến thức

**AWS CLI là gì?**

- Hiểu AWS CLI là công cụ dòng lệnh giúp quản lý và tự động hóa các dịch vụ AWS.
- Hiểu lợi ích của việc sử dụng CLI thay cho AWS Management Console trong nhiều trường hợp.
- Biết cách cấu hình thông tin xác thực để kết nối đến tài khoản AWS.

**Cài đặt và cấu hình AWS CLI**

- Cài đặt AWS CLI trên hệ điều hành.
- Cấu hình Access Key, Secret Access Key, Region và Output Format.
- Kiểm tra kết nối tới tài khoản AWS bằng các lệnh CLI.

**Làm việc với Amazon S3**

- Tạo và quản lý S3 Bucket.
- Upload, Download và liệt kê các đối tượng trong Bucket.
- Xóa dữ liệu và Bucket bằng AWS CLI.

**Làm việc với Amazon SNS**

- Tạo SNS Topic.
- Đăng ký Subscription.
- Gửi Notification thông qua AWS CLI.

**Làm việc với IAM**

- Liệt kê User, Group và Policy.
- Quản lý thông tin IAM bằng các câu lệnh CLI.
- Hiểu cách sử dụng CLI để kiểm tra quyền truy cập.

**Làm việc với Amazon VPC**

- Kiểm tra thông tin VPC.
- Liệt kê Subnet và Security Group.
- Quản lý tài nguyên mạng thông qua AWS CLI.

**Làm việc với Amazon EC2**

- Khởi tạo EC2 Instance bằng AWS CLI.
- Kiểm tra trạng thái Instance.
- Thực hiện các thao tác quản lý EC2 từ dòng lệnh.

---

#### Thực hành

##### Module 1 — Introduction

- Đọc tài liệu giới thiệu AWS CLI.
- Tìm hiểu kiến trúc và nguyên lý hoạt động.
- Chuẩn bị môi trường thực hành.

##### Module 2 — Installation

- Cài đặt AWS CLI.
- Cấu hình thông tin tài khoản AWS.
- Kiểm tra phiên bản và kết nối.

##### Module 3 — Check Resource

- Kiểm tra thông tin tài nguyên AWS.
- Thực hành các lệnh AWS CLI cơ bản.
- Làm quen với cú pháp CLI.

##### Module 4 — AWS CLI với Amazon S3

- Tạo Bucket.
- Upload và Download tệp.
- Xóa dữ liệu trong Bucket.

##### Module 5 — AWS CLI với Amazon SNS

- Tạo Topic.
- Tạo Subscription.
- Gửi thông báo thử nghiệm.

##### Module 6 — AWS CLI với IAM

- Kiểm tra User.
- Liệt kê Group và Policy.
- Thực hành các lệnh IAM.

##### Module 7 — AWS CLI với Amazon VPC

- Kiểm tra VPC.
- Xem Subnet.
- Kiểm tra Security Group.

##### Module 8 — Create EC2

- Khởi tạo EC2 Instance.
- Kiểm tra trạng thái Instance.
- Thực hiện quản lý EC2 bằng AWS CLI.

##### Module 9 — Cleanup Resources

- Kiểm tra toàn bộ tài nguyên đã tạo.
- Xóa tài nguyên không còn sử dụng.
- Xác nhận hoàn tất Cleanup.

---

### Đánh giá kết quả tuần 9:

- Hiểu vai trò của AWS CLI trong việc quản lý dịch vụ AWS.
- Thành thạo cài đặt và cấu hình AWS CLI.
- Thực hành thành công quản lý Amazon S3 bằng CLI.
- Biết sử dụng AWS CLI với Amazon SNS.
- Quản lý được IAM và VPC thông qua dòng lệnh.
- Khởi tạo và quản lý EC2 bằng AWS CLI.
- Nâng cao kỹ năng sử dụng AWS CLI để tự động hóa các thao tác quản trị trên AWS.

---

### Khó khăn gặp phải:

- Ban đầu chưa quen với cú pháp và tham số của AWS CLI.
- Dễ nhầm lẫn giữa các lệnh của từng dịch vụ AWS.
- Một số thao tác yêu cầu cấu hình IAM Permission phù hợp.
- Việc ghi nhớ tên tài nguyên và ID khi thao tác bằng CLI còn mất thời gian.

---

### Hướng khắc phục:

- Thực hành thường xuyên các lệnh AWS CLI.
- Tham khảo AWS CLI Command Reference khi cần.
- Kiểm tra IAM Policy trước khi thực hiện các thao tác.
- Ghi chú các câu lệnh thường dùng để thuận tiện trong quá trình thực hành.

---

### Định hướng tuần tiếp theo:

- Tìm hiểu về AWS CloudFormation.
- Thực hành tạo hạ tầng dưới dạng mã (Infrastructure as Code).
- Triển khai tài nguyên AWS bằng Template.
- Tìm hiểu quản lý Stack và cập nhật hạ tầng.
- Chuẩn bị hình ảnh minh chứng và hoàn thiện báo cáo thực hành tuần tiếp theo.
