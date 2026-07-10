---
title: "Worklog Tuần 4"
date: 2026-05-18
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

### Mục tiêu tuần 4:

- Tìm hiểu tổng quan về Amazon RDS và vai trò của RDS trong hệ thống cơ sở dữ liệu trên Cloud.
- Nắm được đặc điểm của RDS như managed service, backup tự động, scaling, Multi-AZ và Read Replica.
- Thực hành tạo VPC, Subnet, Security Group cho EC2 và RDS.
- Tạo DB Subnet Group để chuẩn bị môi trường triển khai database.
- Khởi tạo EC2 Instance làm máy chủ ứng dụng kết nối đến RDS.
- Tạo RDS Database Instance và kiểm tra Endpoint, Port, Username.
- Triển khai ứng dụng AWS FCJ Management kết nối với cơ sở dữ liệu RDS.
- Thực hành backup, snapshot và restore database.
- Dọn dẹp tài nguyên sau khi hoàn thành lab để tránh phát sinh chi phí.

---

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Đọc phần Introduction về Amazon RDS <br> - Tìm hiểu RDS là gì, OLTP, DB Instance, Endpoint <br> - Nắm các database engine được hỗ trợ như MySQL, PostgreSQL, MariaDB, SQL Server, Oracle, Aurora | 18/05/2026 | 18/05/2026 | https://000005.awsstudygroup.com/ |
| 3 | - Module 2.1: Create a VPC <br> - Tạo VPC, Public Subnet, Private Subnet <br> - Cấu hình nhiều Availability Zone để chuẩn bị cho RDS | 19/05/2026 | 19/05/2026 | https://000005.awsstudygroup.com/ |
| 4 | - Module 2.2: Create EC2 Security Group <br> - Module 2.3: Create RDS Security Group <br> - Mở các port cần thiết cho EC2 và giới hạn RDS chỉ nhận kết nối từ EC2 Security Group | 20/05/2026 | 20/05/2026 | https://000005.awsstudygroup.com/ |
| 5 | - Module 2.4: Create DB Subnet Group <br> - Module 3: Create EC2 Instance <br> - Launch Amazon Linux EC2, gán Key Pair và kết nối SSH bằng MobaXterm | 21/05/2026 | 21/05/2026 | https://000005.awsstudygroup.com/ |
| 6 | - Module 4: Create RDS Database Instance <br> - Tạo database instance, cấu hình engine, username, password, VPC, Security Group <br> - Kiểm tra trạng thái Available, Endpoint và Port của RDS | 22/05/2026 | 22/05/2026 | https://000005.awsstudygroup.com/ |
| 7 | - Module 5: Application Deployment <br> - Clone source code AWS FCJ Management <br> - Cài Node.js, npm package, MySQL client <br> - Tạo database, table user và chạy ứng dụng qua port 5000 | 23/05/2026 | 23/05/2026 | https://000005.awsstudygroup.com/ |
| CN | - Module 6: Backup and Restore <br> - Kiểm tra backup, snapshot và restore RDS <br> - Module 7: Clean up resources <br> - Xóa EC2, RDS, Snapshot, Subnet Group, Security Group, VPC để tránh phát sinh phí | 24/05/2026 | 24/05/2026 | https://000005.awsstudygroup.com/ |

---

### Kết quả đạt được tuần 4:

#### Kiến thức

**Amazon RDS là gì?**

- Hiểu Amazon RDS là dịch vụ cơ sở dữ liệu quan hệ được quản lý bởi AWS.
- RDS giúp người dùng tạo và vận hành database mà không cần tự quản lý server vật lý hoặc máy ảo database.
- RDS phù hợp với các hệ thống xử lý giao dịch như quản lý người dùng, đơn hàng, thanh toán hoặc dữ liệu có cấu trúc.
- RDS hỗ trợ nhiều database engine như Amazon Aurora, MySQL, MariaDB, Oracle, SQL Server và PostgreSQL.

**Managed Service trong RDS**

- Hiểu RDS là managed service, vì vậy người dùng không cần trực tiếp quản lý hệ điều hành bên dưới DB Instance.
- AWS hỗ trợ các tác vụ như backup, patching, software update, scaling, Multi-AZ và failover.
- Người dùng có thể tập trung nhiều hơn vào thiết kế database, bảo mật kết nối và vận hành ứng dụng.

**VPC, Subnet và DB Subnet Group**

- Hiểu VPC là mạng riêng ảo dùng để cô lập tài nguyên AWS.
- Biết cách tạo VPC với public subnet và private subnet.
- Public subnet phù hợp cho EC2 cần truy cập Internet.
- Private subnet phù hợp cho RDS để hạn chế truy cập trực tiếp từ Internet.
- Biết DB Subnet Group là tập hợp các subnet được chỉ định cho RDS.
- DB Subnet Group nên có subnet ở ít nhất hai Availability Zone để hỗ trợ tính sẵn sàng cao.

**Security Group cho EC2 và RDS**

- Hiểu Security Group hoạt động như firewall cho EC2 và RDS.
- EC2 Security Group cần mở các port cần thiết như:
  - SSH: `22`
  - HTTP: `80`
  - HTTPS: `443`
  - Application port: `5000`
- RDS Security Group cần mở port database, ví dụ MySQL/Aurora sử dụng port `3306`.
- Nên cấu hình RDS chỉ cho phép kết nối từ EC2 Security Group thay vì mở public IP rộng rãi.

**EC2 Instance dùng làm Application Server**

- Biết cách tạo Amazon Linux EC2 Instance.
- Biết chọn AMI Amazon Linux 2023.
- Biết chọn Instance Type phù hợp như `t2.micro` hoặc `t3.micro`.
- Biết gán Key Pair để SSH vào máy chủ.
- Biết kết nối EC2 bằng MobaXterm thông qua Public IP hoặc Public DNS.

**RDS Database Instance**

- Biết cách tạo RDS Database Instance bằng chế độ Standard create.
- Biết cấu hình engine, version, template, master username, password, VPC và Security Group.
- Biết kiểm tra trạng thái RDS từ `Creating` sang `Available`.
- Biết lấy Endpoint, Port và Username để ứng dụng hoặc EC2 kết nối đến database.

**Triển khai ứng dụng kết nối RDS**

- Biết clone source code từ GitHub về EC2.
- Biết cài Node.js, npm và các package cần thiết như Express, dotenv, MySQL.
- Biết tạo file `.env` để lưu thông tin kết nối database gồm Endpoint, Database Name, Username và Password.
- Biết tạo database `first_cloud_users`, tạo bảng `user` và thêm dữ liệu mẫu.
- Biết chạy ứng dụng bằng `npm start` và truy cập ứng dụng qua port `5000`.

**Backup và Restore trong RDS**

- Hiểu RDS hỗ trợ automated backup và manual snapshot.
- Biết kiểm tra backup trong tab Maintenance & backups.
- Biết tạo snapshot và restore snapshot thành một DB Instance mới.
- Hiểu rằng database được restore sẽ có Endpoint mới, vì vậy ứng dụng cần cập nhật lại chuỗi kết nối nếu sử dụng database mới.

**Dọn dẹp tài nguyên**

- Biết xóa RDS Database Instance, DB Snapshot và DB Subnet Group.
- Biết terminate EC2 Instance sau khi hoàn thành lab.
- Biết xóa Security Group, NAT Gateway, Elastic IP và VPC nếu không còn sử dụng.
- Hiểu tầm quan trọng của việc kiểm tra Billing Dashboard hoặc Cost Explorer sau khi cleanup để tránh phát sinh chi phí.

---

#### Thực hành

**Module 1 — Introduction**

- Đọc tổng quan về Amazon RDS.
- Tìm hiểu RDS là managed relational database service.
- Phân biệt RDS với database tự cài trên EC2.
- Tìm hiểu các khái niệm:
  - DB Instance
  - Endpoint
  - Database Engine
  - Maintenance Window
  - Backup
  - Snapshot
  - Multi-AZ
  - Read Replica
-  _Ảnh minh chứng: Trang Introduction của workshop Amazon RDS._

**Module 2.1 — Create a VPC**

- Tạo VPC cho môi trường triển khai RDS.
- Cấu hình IPv4 CIDR block.
- Tạo public subnet cho EC2.
- Tạo private subnet cho RDS.
- Chọn ít nhất hai Availability Zone để tăng tính sẵn sàng.
- Kiểm tra auto-assign public IPv4 cho subnet phù hợp.
-  _Ảnh minh chứng: VPC đã tạo thành công._
-  _Ảnh minh chứng: Public subnet và private subnet._

**Module 2.2 — Create EC2 Security Group**

- Tạo Security Group dành cho EC2 Instance.
- Cấu hình inbound rules:
  - SSH port `22`
  - HTTP port `80`
  - HTTPS port `443`
  - Custom TCP port `5000`
- Giới hạn SSH theo IP cá nhân để tăng bảo mật.
- Ghi lại Security Group ID để dùng khi tạo EC2 và cấu hình RDS.
-  _Ảnh minh chứng: EC2 Security Group đã tạo._
-  _Ảnh minh chứng: Inbound rules của EC2 Security Group._

**Module 2.3 — Create RDS Security Group**

- Tạo Security Group riêng cho RDS.
- Cấu hình inbound rule cho MySQL/Aurora port `3306`.
- Chọn source là EC2 Security Group thay vì mở public Internet.
- Kiểm tra RDS Security Group đã nằm trong đúng VPC.
-  _Ảnh minh chứng: RDS Security Group đã tạo._
-  _Ảnh minh chứng: Rule cho phép EC2 kết nối đến RDS._

**Module 2.4 — Create DB Subnet Group**

- Truy cập Amazon RDS Console.
- Tạo DB Subnet Group.
- Chọn VPC đã tạo ở bước trước.
- Chọn subnet ở ít nhất hai Availability Zone.
- Ưu tiên chọn private subnet cho database.
- Kiểm tra DB Subnet Group sau khi tạo thành công.
-  _Ảnh minh chứng: DB Subnet Group đã tạo._
-  _Ảnh minh chứng: Các subnet được thêm vào DB Subnet Group._

**Module 3 — Create EC2 Instance**

- Tạo Amazon Linux EC2 Instance.
- Chọn AMI Amazon Linux 2023.
- Chọn Instance Type phù hợp với lab.
- Gán Key Pair để đăng nhập SSH.
- Gán EC2 Security Group đã tạo.
- Launch instance và chờ trạng thái `running`.
- Kết nối EC2 bằng MobaXterm.
- Kiểm tra terminal sau khi SSH thành công.
-  _Ảnh minh chứng: EC2 Instance ở trạng thái running._
-  _Ảnh minh chứng: SSH thành công vào EC2._

**Module 4 — Create RDS Database Instance**

- Truy cập Amazon RDS Console.
- Chọn Create database.
- Chọn Standard create.
- Chọn database engine phù hợp với workshop.
- Cấu hình DB Instance identifier.
- Thiết lập master username và master password.
- Chọn VPC, DB Subnet Group và RDS Security Group.
- Tạo database instance.
- Chờ trạng thái RDS chuyển sang `Available`.
- Kiểm tra Endpoint, Port và Username.
-  _Ảnh minh chứng: RDS Database Instance đang tạo._
-  _Ảnh minh chứng: RDS ở trạng thái Available._
-  _Ảnh minh chứng: Endpoint và Port của RDS._

**Module 5 — Application Deployment**

- SSH vào EC2 Instance.
- Cài đặt Git trên Amazon Linux.
- Clone repository AWS FCJ Management.
- Cài Node.js và npm.
- Cài các package cần thiết cho ứng dụng.
- Cài MySQL client để kết nối đến RDS.
- Tạo file `.env` chứa thông tin:
  - `DB_HOST`
  - `DB_NAME`
  - `DB_USER`
  - `DB_PASS`
- Kết nối đến RDS bằng Endpoint.
- Tạo database `first_cloud_users`.
- Tạo bảng `user`.
- Thêm dữ liệu mẫu vào bảng.
- Chạy ứng dụng bằng lệnh:

```bash
chmod 400 first-kp.pem
