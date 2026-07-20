---
title: "Worklog Tuần 4"
date: 2026-05-11
weight: 4
chapter: false
pre: "  1.4.  "
---

### Mục tiêu tuần 4:

* Khám phá bức tranh toàn cảnh về Amazon RDS cũng như tầm quan trọng của dịch vụ này trong kiến trúc cơ sở dữ liệu đám mây.
* Hiểu rõ các tính năng cốt lõi của RDS bao gồm: dịch vụ được quản lý (managed service), tự động sao lưu, khả năng mở rộng, kiến trúc Multi-AZ và Read Replica.
* Tiến hành thiết lập mạng VPC, Subnet và cấu hình Security Group phục vụ cho cả EC2 và RDS.
* Xây dựng DB Subnet Group nhằm tạo lập không gian mạng chuẩn cho việc triển khai cơ sở dữ liệu.
* Cài đặt và khởi chạy máy ảo EC2 đóng vai trò là server ứng dụng kết nối trực tiếp với RDS.
* Thiết lập RDS Database Instance, đồng thời thu thập các thông tin quan trọng như Endpoint, Port và Username.
* Đưa ứng dụng AWS FCJ Management lên môi trường và cấu hình để giao tiếp với database RDS.
* Làm quen với các thao tác sao lưu (backup), chụp bản ghi (snapshot) và phục hồi (restore) cơ sở dữ liệu.
* Thực hiện xóa bỏ các tài nguyên đã khởi tạo sau khi kết thúc bài thực hành nhằm tối ưu chi phí.

---

### Các công việc cần triển khai trong tuần này:

| Thứ | Hạng mục công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Nghiên cứu tài liệu giới thiệu về Amazon RDS. <br> - Làm rõ các khái niệm: RDS, hệ thống OLTP, DB Instance, Endpoint. <br> - Liệt kê các engine được hỗ trợ: MySQL, PostgreSQL, MariaDB, SQL Server, Oracle và Aurora. | 11/05/2026 | 11/05/2026 | [https://000005.awsstudygroup.com/](https://000005.awsstudygroup.com/) |
| 3 | - Module 2.1: Create a VPC. <br> - Thiết lập mạng VPC bao gồm cả Public và Private Subnet. <br> - Triển khai trên nhiều Availability Zone nhằm đảm bảo tính sẵn sàng cho RDS. | 12/05/2026 | 12/05/2026 | [https://000005.awsstudygroup.com/](https://000005.awsstudygroup.com/) |
| 4 | - Module 2.2: Create EC2 Security Group. <br> - Module 2.3: Create RDS Security Group. <br> - Thiết lập các quy tắc tường lửa (mở port) cho EC2 và cấu hình RDS sao cho chỉ cho phép traffic đi vào từ Security Group của EC2. | 13/05/2026 | 13/05/2026 | [https://000005.awsstudygroup.com/](https://000005.awsstudygroup.com/) |
| 5 | - Module 2.4: Create DB Subnet Group. <br> - Module 3: Create EC2 Instance. <br> - Khởi tạo máy ảo Amazon Linux, đính kèm Key Pair và thực hiện kết nối từ xa qua SSH bằng phần mềm MobaXterm. | 14/05/2026 | 14/05/2026 | [https://000005.awsstudygroup.com/](https://000005.awsstudygroup.com/) |
| 6 | - Module 4: Create RDS Database Instance. <br> - Tiến hành tạo database, lựa chọn engine, thiết lập tài khoản, mật khẩu, cũng như gắn VPC và Security Group tương ứng. <br> - Xác nhận trạng thái Available và lấy thông tin kết nối (Endpoint, Port). | 15/05/2026 | 15/05/2026 | [https://000005.awsstudygroup.com/](https://000005.awsstudygroup.com/) |
| 7 | - Module 5: Application Deployment. <br> - Tải source code AWS FCJ Management từ kho lưu trữ. <br> - Cài đặt môi trường Node.js, các thư viện npm và MySQL client. <br> - Khởi tạo database, bảng dữ liệu (user) và khởi chạy ứng dụng trên cổng 5000. | 16/05/2026 | 16/05/2026 | [https://000005.awsstudygroup.com/](https://000005.awsstudygroup.com/) |
| CN | - Module 6: Backup and Restore. <br> - Thử nghiệm các tính năng sao lưu, tạo snapshot và khôi phục RDS. <br> - Module 7: Clean up resources. <br> - Xóa toàn bộ tài nguyên (EC2, RDS, Snapshot, Security Group, VPC,...) để không bị tính thêm phí. | 17/05/2026 | 17/05/2026 | [https://000005.awsstudygroup.com/](https://000005.awsstudygroup.com/) |

---

### Kết quả đạt được tuần 4:

#### Kiến thức

**Amazon RDS là gì?**

* Nắm bắt được đây là một dịch vụ cơ sở dữ liệu quan hệ được quản lý hoàn toàn bởi hệ sinh thái AWS.
* Nhận thấy sự tiện lợi của RDS khi cho phép vận hành database mà không cần bận tâm đến việc bảo trì máy chủ vật lý hay hệ điều hành.
* Ứng dụng tốt cho các hệ thống yêu cầu xử lý giao dịch khắt khe (đơn hàng, người dùng, thanh toán) hay các kho dữ liệu có cấu trúc rõ ràng.
* Biết được danh sách các engine tương thích bao gồm: Amazon Aurora, Oracle, SQL Server, MySQL, PostgreSQL và MariaDB.

**Managed Service trong RDS**

* Hiểu rõ bản chất "managed service" giúp người dùng thoát khỏi gánh nặng quản trị hệ điều hành máy chủ.
* Các công việc bảo trì định kỳ như vá lỗi (patching), cập nhật phần mềm, sao lưu, mở rộng hay chuyển đổi dự phòng (failover) đều do AWS tự động xử lý.
* Tiết kiệm thời gian để đội ngũ kỹ thuật có thể tập trung vào việc tối ưu hóa cấu trúc dữ liệu và phát triển ứng dụng.

**VPC, Subnet và DB Subnet Group**

* Nắm được khái niệm VPC là môi trường mạng ảo độc lập giúp bảo vệ các tài nguyên trên AWS.
* Phân biệt và biết cách triển khai hai loại subnet: public và private.
* Public subnet được phân bổ cho máy ảo EC2 để có thể giao tiếp với Internet.
* Private subnet dùng để chứa RDS, giúp ẩn database khỏi các rủi ro từ mạng công cộng.
* Nắm được định nghĩa DB Subnet Group là một cụm các subnet dành riêng cho hệ thống RDS.
* Hiểu nguyên tắc thiết kế DB Subnet Group là phải trải dài trên ít nhất 2 Availability Zone để dự phòng sự cố.

**Security Group cho EC2 và RDS**

* Xem Security Group như một lớp tường lửa ảo bảo vệ ở cấp độ instance (cho cả EC2 và RDS).
* Đối với EC2, cần thiết lập mở các cổng giao tiếp cơ bản:
* `22` cho SSH
* `80` cho HTTP
* `443` cho HTTPS
* `5000` cho ứng dụng


* Đối với RDS (ví dụ dùng MySQL/Aurora), cần cho phép truy cập qua cổng `3306`.
* Áp dụng nguyên tắc bảo mật tối thiểu: Không mở cổng database ra Internet, mà chỉ cho phép luồng dữ liệu (inbound) xuất phát từ Security Group của máy ảo EC2.

**EC2 Instance dùng làm Application Server**

* Nắm vững các bước khởi tạo một máy ảo EC2 chạy Amazon Linux.
* Lựa chọn đúng image (AMI Amazon Linux 2023).
* Cấu hình loại instance tiết kiệm chi phí (`t2.micro` hoặc `t3.micro`).
* Quản lý Key Pair để xác thực bảo mật khi kết nối.
* Sử dụng MobaXterm để SSH vào hệ thống thông qua Public IP/DNS.

**RDS Database Instance**

* Thành thạo việc thiết lập một RDS Database Instance qua tùy chọn Standard create.
* Biết cách tùy chỉnh các thông số: loại engine, phiên bản, thông tin đăng nhập (master user/pass), cũng như gán mạng VPC và Security Group.
* Nhận biết quá trình chuyển đổi trạng thái của database từ lúc `Creating` cho đến khi `Available`.
* Biết cách trích xuất chuỗi kết nối (Endpoint) và thông tin port để phục vụ việc cấu hình trên app server.

**Triển khai ứng dụng kết nối RDS**

* Biết cách tải mã nguồn từ GitHub xuống môi trường máy chủ EC2.
* Hoàn thiện việc cài đặt Node.js, trình quản lý gói npm cùng các dependency (Express, dotenv, MySQL).
* Khai báo file môi trường `.env` nhằm bảo mật các thông tin nhạy cảm: Endpoint, tên database, tài khoản và mật khẩu.
* Thực hiện kết nối vào database mới tạo, lập cơ sở dữ liệu `first_cloud_users`, tạo bảng `user` và insert dữ liệu chạy thử.
* Khởi động ứng dụng thông qua lệnh `npm start` và kiểm tra hoạt động trên trình duyệt qua port `5000`.

**Backup và Restore trong RDS**

* Nắm được sự khác biệt giữa sao lưu tự động (automated backup) và tạo bản sao thủ công (manual snapshot).
* Theo dõi các bản backup trong khu vực Maintenance & backups trên console.
* Biết cách xuất snapshot cũng như dùng snapshot đó để dựng lại một DB Instance hoàn toàn mới.
* Hiểu được rằng sau khi phục hồi, hệ thống sẽ cấp một Endpoint khác, đòi hỏi phải cập nhật lại cấu hình kết nối trên phía ứng dụng.

**Dọn dẹp tài nguyên**

* Thành thạo quy trình gỡ bỏ RDS Database Instance, các Snapshot và DB Subnet Group liên quan.
* Chấm dứt hoạt động (terminate) của máy ảo EC2 ngay sau khi xong bài thực hành.
* Lần lượt xóa bỏ các thành phần mạng như Security Group, NAT Gateway, Elastic IP và VPC.
* Ý thức được việc phải kiểm tra chéo trên Cost Explorer hoặc Billing Dashboard để đảm bảo không bị tính phí "oan" do sót tài nguyên.

---

#### Thực hành

**Module 1 — Introduction**

* Nghiên cứu phần giới thiệu chung về giải pháp Amazon RDS.
* Làm rõ bản chất của một dịch vụ cơ sở dữ liệu quan hệ được quản lý.
* So sánh sự khác biệt giữa dùng RDS và tự dựng database trên máy ảo EC2.
* Làm quen với bộ thuật ngữ chuyên ngành:
* DB Instance
* Chuỗi kết nối (Endpoint)
* Database Engine
* Cửa sổ bảo trì (Maintenance Window)
* Backup & Snapshot
* Kiến trúc Multi-AZ
* Read Replica


**Module 2.1 — Create a VPC**

* Thiết lập không gian mạng ảo (VPC) phục vụ cho lab RDS.
* Khai báo dải mạng IPv4 CIDR.
* Cấu hình public subnet dành riêng cho EC2.
* Cấu hình private subnet dành riêng cho database RDS.
* Lựa chọn 2 vùng Availability Zone khác nhau để tăng tính chống chịu lỗi.
* Bật tính năng tự động cấp phát IP public (auto-assign public IPv4) cho subnet phù hợp.


**Module 2.2 — Create EC2 Security Group**

* Xây dựng Security Group đóng vai trò màng lọc cho máy chủ EC2.
* Bổ sung các quy tắc Inbound:
* Cổng `22` (Dùng cho SSH)
* Cổng `80` (Dùng cho HTTP)
* Cổng `443` (Dùng cho HTTPS)
* Cổng TCP `5000` (Cho ứng dụng custom)


* Siết chặt bảo mật bằng cách chỉ cho phép SSH từ địa chỉ IP của máy cá nhân.
* Lưu trữ lại ID của Security Group này để sử dụng ở các bước sau.

**Module 2.3 — Create RDS Security Group**

* Thiết lập một màng lọc mạng độc lập cho RDS.
* Tạo quy tắc Inbound cho phép cổng `3306` (dành cho MySQL/Aurora) hoạt động.
* Ở mục Source, chỉ định nhận traffic từ EC2 Security Group đã tạo ở trên, tuyệt đối không chọn Anywhere.
* Xác nhận RDS Security Group đã được đặt đúng vào mạng VPC của bài lab.


**Module 2.4 — Create DB Subnet Group**

* Đăng nhập vào giao diện quản trị Amazon RDS.
* Điều hướng đến phần khởi tạo DB Subnet Group.
* Ràng buộc vào mạng VPC vừa dựng.
* Lựa chọn các subnet thuộc tối thiểu 2 Availability Zone khác biệt.
* Đảm bảo chỉ add các private subnet để tăng cường an ninh cho database.
* Xác minh lại thông tin sau khi hệ thống báo tạo thành công.


**Module 3 — Create EC2 Instance**

* Tiến hành Launch một instance Amazon Linux mới.
* Chỉ định hệ điều hành là Amazon Linux 2023 AMI.
* Cấu hình cấu hình phần cứng cơ bản phù hợp với nhu cầu học tập.
* Chỉ định Key Pair để có thể truy cập SSH.
* Gắn EC2 Security Group đã chuẩn bị từ trước.
* Kích hoạt instance và chờ đến khi trạng thái báo `running`.
* Sử dụng MobaXterm để kết nối vào máy chủ.
* Kiểm tra dấu nhắc lệnh (terminal) xác nhận đăng nhập thành công.


**Module 4 — Create RDS Database Instance**

* Trở lại màn hình quản lý Amazon RDS.
* Nhấp chọn Create database.
* Bật tùy chọn Standard create để có thể tự do cấu hình.
* Lựa chọn loại engine cơ sở dữ liệu tương ứng theo hướng dẫn.
* Đặt tên định danh cho DB Instance (identifier).
* Cung cấp thông tin tài khoản quản trị (master username) và mật khẩu (password).
* Kết nối instance vào VPC, DB Subnet Group và RDS Security Group tương ứng.
* Bắt đầu quá trình tạo database.
* Đợi đến khi trạng thái chuyển từ `Creating` qua `Available`.
* Ghi lại các thông tin: Endpoint, Port và Username.


**Module 5 — Application Deployment**

* Kết nối SSH vào máy chủ EC2.
* Cài đặt công cụ Git lên nền tảng Amazon Linux.
* Clone dự án AWS FCJ Management về máy ảo.
* Thiết lập môi trường thực thi Node.js cùng bộ quản lý gói npm.
* Cài đặt đầy đủ các thư viện phụ thuộc của ứng dụng.
* Cài đặt gói MySQL client để giao tiếp với database từ command line.
* Khởi tạo tệp tin biến môi trường `.env` với các tham số:
* `DB_HOST`
* `DB_NAME`
* `DB_USER`
* `DB_PASS`


* Dùng thông tin Endpoint để test kết nối tới RDS.
* Khởi tạo database mang tên `first_cloud_users`.
* Xây dựng bảng dữ liệu `user`.
* Insert một số bản ghi mẫu để kiểm thử.
* Khởi động web app bằng câu lệnh:

```bash
npm start
