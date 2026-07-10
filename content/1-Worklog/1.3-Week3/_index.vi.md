---
title: "Worklog Tuần 3"
date: 2026-05-11
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

### Mục tiêu tuần 3:

- Tìm hiểu tổng quan về Amazon EC2 và vai trò của EC2 trong hạ tầng Cloud.
- Thực hành tạo VPC, Security Group cho Linux và Windows Instance.
- Khởi tạo, kết nối và quản lý EC2 Instance trên AWS.
- Làm quen với các thao tác cơ bản: đổi Instance Type, tạo Snapshot, tạo AMI, khôi phục truy cập.
- Triển khai thử ứng dụng AWS User Management trên Linux và Windows Server.
- Thực hành quản trị chi phí và quyền sử dụng EC2 bằng IAM Policy.
- Dọn dẹp tài nguyên sau khi hoàn thành lab để tránh phát sinh chi phí.

---

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Đọc phần Introduction từ Amazon EC2 <br> - Nắm tổng quan workshop và kiến trúc thực hành <br> - Chuẩn bị tài khoản AWS, Region, Key Pair và môi trường kết nối | 11/05/2026 | 11/05/2026 | https://000004.awsstudygroup.com/ |
| 3 | - Module 2.1: Create a Linux VPC <br> - Module 2.2: Create VPC for Windows Instance <br> - Module 2.3: Create Security Group for Linux Instance <br> - Module 2.4: Create Security Group for Windows Instance | 12/05/2026 | 12/05/2026 | https://000004.awsstudygroup.com/ |
| 4 | - Module 3.1: Launch Microsoft Windows Server 2022 Instance <br> - Module 3.2: Connect from Computer to Windows Instance <br> - Kiểm tra Remote Desktop và key pair đăng nhập Windows | 13/05/2026 | 13/05/2026 | https://000004.awsstudygroup.com/ |
| 5 | - Module 4.1: Launch Amazon Linux Instance <br> - Module 4.2: Connect to Amazon Linux Instance <br> - Thực hành SSH vào Linux Instance bằng key pair | 14/05/2026 | 14/05/2026 | https://000004.awsstudygroup.com/ |
| 6 | - Module 5.1: Modify EC2 Instance Type <br> - Module 5.2: Create and Manage EBS Snapshots <br> - Module 5.3: Create Custom AMI <br> - Module 5.4: Launch Instance from Custom AMI <br> - Module 5.5 - 5.7: Recover access và Remote Desktop Ubuntu | 15/05/2026 | 15/05/2026 | https://000004.awsstudygroup.com/ |
| 7 | - Module 6: Deploy AWS User Management Application on Amazon Linux <br> - Cài LAMP Server, cấu hình database, phpMyAdmin, Node.js và deploy app | 16/05/2026 | 16/05/2026 | https://000004.awsstudygroup.com/ |
| CN | - Module 7: Deploy Node.js Application on Windows EC2 <br> - Module 8: Cost & Usage Governance with IAM <br> - Module 9: Clean up resources | 17/05/2026 | 17/05/2026 | https://000004.awsstudygroup.com/ |
---

### Kết quả đạt được tuần 3:

#### Kiến thức

**Amazon EC2 là gì?**

- Hiểu Amazon EC2 là dịch vụ cung cấp máy chủ ảo trên AWS.
- EC2 cho phép tạo, cấu hình, kết nối và quản lý server theo nhu cầu.
- Có thể chạy nhiều hệ điều hành khác nhau như Amazon Linux, Ubuntu và Windows Server.
- EC2 thường được dùng để triển khai web server, backend, database thử nghiệm, ứng dụng nội bộ hoặc môi trường lab.

**VPC và Security Group**

- Hiểu VPC là mạng riêng ảo dùng để cô lập tài nguyên trên AWS.
- Biết cách tạo VPC riêng cho Linux Instance và Windows Instance.
- Hiểu Security Group hoạt động như firewall cấp instance.
- Biết mở port phù hợp:
  - SSH: `22` cho Linux.
  - RDP: `3389` cho Windows.
  - HTTP: `80` cho web server.
  - HTTPS: `443` cho web bảo mật.

**Key Pair và kết nối EC2**

- Key Pair dùng để xác thực khi truy cập EC2.
- Với Linux, sử dụng file `.pem` để SSH.
- Với Windows, sử dụng key pair để decrypt password rồi kết nối bằng Remote Desktop.
- Nếu mất key pair, cần dùng phương án khôi phục như Systems Manager hoặc gắn volume sang instance khác.

**EC2 Instance Type**

- Instance Type quyết định CPU, RAM, network và khả năng xử lý của máy chủ.
- Có thể stop instance rồi thay đổi Instance Type khi cần nâng hoặc giảm cấu hình.
- Việc chọn Instance Type cần dựa trên nhu cầu thực tế và chi phí.

**EBS Snapshot và Custom AMI**

- EBS Snapshot dùng để sao lưu volume.
- Custom AMI dùng để tạo image từ một instance đã cấu hình sẵn.
- Có thể launch instance mới từ AMI để tái sử dụng môi trường đã cài đặt.

**Triển khai ứng dụng trên EC2**

- Biết cách cài LAMP Server trên Amazon Linux.
- Biết cấu hình database server và phpMyAdmin.
- Biết cài Node.js để triển khai ứng dụng AWS User Management.
- Thực hành deploy ứng dụng trên cả Linux Instance và Windows Instance.

**Cost & Usage Governance với IAM**

- Biết dùng IAM Policy để giới hạn quyền sử dụng dịch vụ.
- Có thể hạn chế tạo EC2 theo Region, Instance Family hoặc Instance Type.
- Có thể kiểm soát quyền xóa tài nguyên theo IP hoặc thời gian.
- Hiểu tầm quan trọng của governance để tránh lạm dụng tài nguyên và phát sinh chi phí.

---

#### Thực hành

**Module 2 — Preparation**

- Tạo VPC cho Linux Instance.
- Tạo VPC cho Windows Instance.
- Tạo Security Group cho Linux:
  - Cho phép SSH từ IP cá nhân.
  - Cho phép HTTP nếu cần chạy web server.
- Tạo Security Group cho Windows:
  - Cho phép RDP từ IP cá nhân.
  - Không mở `0.0.0.0/0` nếu không cần thiết.
-  _Ảnh minh chứng: VPC và Security Group đã tạo thành công._

**Module 3 — Launch Microsoft Windows Server 2022 Instance**

- Chọn AMI Microsoft Windows Server 2022.
- Chọn Instance Type phù hợp với Free Tier hoặc lab.
- Gán Key Pair để có thể lấy password.
- Cấu hình Security Group cho phép RDP.
- Khởi tạo Windows EC2 Instance.
- Decrypt password bằng file key pair.
- Kết nối vào Windows Instance bằng Remote Desktop.
-  _Ảnh minh chứng: Windows EC2 ở trạng thái running._
-  _Ảnh minh chứng: Remote Desktop kết nối thành công._

**Module 4 — Launch Amazon Linux Instance**

- Chọn AMI Amazon Linux.
- Chọn Instance Type phù hợp.
- Gán Key Pair.
- Cấu hình Security Group cho phép SSH.
- Khởi tạo Linux EC2 Instance.
- Kết nối SSH vào instance bằng terminal.
-  _Ảnh minh chứng: Amazon Linux EC2 running._
-  _Ảnh minh chứng: SSH thành công vào Linux Instance._

**Module 5 — Amazon EC2 Basic**

- Thực hành thay đổi Instance Type.
- Tạo và quản lý EBS Snapshot.
- Tạo Custom AMI từ EC2 Instance đã cấu hình.
- Launch EC2 Instance mới từ Custom AMI.
- Tìm hiểu cách khôi phục truy cập Windows Instance.
- Tìm hiểu cách khôi phục truy cập Linux Instance.
- Thực hành Remote Desktop vào EC2 Ubuntu.
-  _Ảnh minh chứng: Snapshot đã tạo._
-  _Ảnh minh chứng: Custom AMI đã tạo._
-  _Ảnh minh chứng: Instance launch từ AMI thành công._

**Module 6 — Deploy AWS User Management Application on Amazon Linux**

- Cài đặt LAMP Web Server.
- Kiểm tra Apache/PHP hoạt động.
- Cấu hình database server.
- Cài đặt phpMyAdmin.
- Cài Node.js trên Amazon Linux.
- Deploy ứng dụng AWS User Management.
- Kiểm tra chức năng CRUD cơ bản của ứng dụng.
-  _Ảnh minh chứng: Apache test page._
-  _Ảnh minh chứng: phpMyAdmin hoạt động._
-  _Ảnh minh chứng: ứng dụng AWS User Management chạy thành công._

**Module 7 — Deploy Node.js Application on EC2 Windows**

- Cài XAMPP trên Windows Instance.
- Cài Node.js trên Windows Instance.
- Deploy AWS User Management Application trên Windows Server.
- Kiểm tra ứng dụng qua trình duyệt.
-  _Ảnh minh chứng: XAMPP chạy trên Windows._
-  _Ảnh minh chứng: Node.js application hoạt động._

**Module 8 — Cost & Usage Governance with IAM**

- Tạo IAM Policy giới hạn sử dụng dịch vụ theo Region.
- Tạo Policy giới hạn EC2 theo Instance Family.
- Tạo Policy giới hạn EC2 theo Instance Type.
- Tạo Policy quản lý loại EBS Volume được phép dùng.
- Tạo Policy giới hạn quyền xóa tài nguyên theo IP công ty.
- Tạo Policy giới hạn quyền xóa tài nguyên theo thời gian.
-  _Ảnh minh chứng: IAM Policy đã tạo._
-  _Ảnh minh chứng: thao tác bị chặn khi không đúng điều kiện policy._

**Module 9 — Clean up resources**

- Terminate các EC2 Instance không còn sử dụng.
- Xóa EBS Volume không cần thiết.
- Xóa Snapshot nếu không dùng nữa.
- Xóa AMI đã tạo trong lab.
- Xóa Security Group, VPC hoặc tài nguyên phụ nếu không còn cần.
- Kiểm tra Billing Dashboard sau khi dọn dẹp.
-  _Ảnh minh chứng: không còn instance running._
-  _Ảnh minh chứng: Billing không phát sinh bất thường._

---

#### Khó khăn và cách giải quyết

**Khó khăn:**

- Ban đầu dễ nhầm giữa VPC, Subnet và Security Group.
- Khi kết nối Windows Instance, cần decrypt password bằng đúng key pair.
- Khi SSH vào Linux, dễ lỗi do sai permission file `.pem`.
- Nếu mở sai port hoặc sai source IP trong Security Group thì không thể kết nối.
- Một số tài nguyên như EBS Snapshot, AMI, Elastic IP có thể phát sinh phí nếu quên xóa.
- Khi deploy app, có thể gặp lỗi package, firewall, port hoặc database connection.

**Cách giải quyết:**

- Kiểm tra lại kiến trúc mạng trước khi launch instance.
- Đặt tên tài nguyên rõ ràng theo format:
  - `fcj-linux-vpc`
  - `fcj-windows-vpc`
  - `fcj-linux-sg`
  - `fcj-windows-sg`
- Chỉ mở SSH/RDP theo IP cá nhân, hạn chế mở toàn bộ Internet.
- Với Linux, chạy lệnh phân quyền key pair trước khi SSH:

```bash
chmod 400 first-kp.pem
