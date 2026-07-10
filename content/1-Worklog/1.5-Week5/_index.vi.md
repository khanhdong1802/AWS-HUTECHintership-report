---
title: "Worklog Tuần 5"
date: 2026-05-25
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

### Mục tiêu tuần 5:

- Tìm hiểu tổng quan về mô hình triển khai ứng dụng FCJ Management với Auto Scaling Group.
- Nắm được vai trò của Amazon EC2 Auto Scaling trong việc tự động điều chỉnh số lượng EC2 Instance theo tải hệ thống.
- Hiểu cách kết hợp Auto Scaling Group với Application Load Balancer để tăng tính sẵn sàng và khả năng chịu lỗi cho ứng dụng.
- Thực hành chuẩn bị hạ tầng gồm VPC, Public Subnet, Private Subnet, Security Group, EC2 Instance và Amazon RDS.
- Triển khai ứng dụng FCJ Management trên EC2, cấu hình kết nối đến RDS và chạy ứng dụng bằng Node.js, PM2.
- Tạo AMI từ EC2 đã cấu hình, sau đó tạo Launch Template để chuẩn hóa quá trình khởi tạo instance mới.
- Cấu hình Target Group và Application Load Balancer để phân phối lưu lượng truy cập đến các EC2 Instance.
- Tạo Auto Scaling Group, cấu hình Desired Capacity, Min Capacity, Max Capacity và tích hợp với Load Balancer.
- Thực hành kiểm thử Manual Scaling, Scheduled Scaling, Dynamic Scaling và Predictive Scaling.
- Theo dõi metric trên CloudWatch, đọc kết quả scaling và dọn dẹp tài nguyên sau khi hoàn thành lab.

---

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Đọc Introduction của workshop Deploying FCJ Management Application with Auto Scaling Group <br> - Tìm hiểu mục tiêu triển khai ứng dụng có khả năng mở rộng tự động <br> - Tạo VPC `AutoScaling-Lab`, cấu hình public subnet, private subnet và nhiều Availability Zone <br> - Tạo Security Group cho ứng dụng và database | 25/05/2026 | 25/05/2026 | https://000006.awsstudygroup.com/ |
| 3 | - Tạo EC2 Instance `FCJ-Management` dùng Amazon Linux 2023 <br> - Gán Key Pair, Public Subnet và Security Group cho EC2 <br> - Tạo DB Subnet Group và RDS MySQL Instance <br> - Kết nối EC2 đến RDS, tạo database, bảng `user` và thêm dữ liệu mẫu | 26/05/2026 | 26/05/2026 | https://000006.awsstudygroup.com/ |
| 4 | - Cài đặt Git, MariaDB client, Node.js và NPM trên EC2 <br> - Clone source code FCJ Management từ GitHub <br> - Cấu hình file `.env` để kết nối ứng dụng với RDS <br> - Cài PM2, chạy ứng dụng bằng `npm start` và cấu hình tự khởi động lại khi reboot <br> - Chuẩn bị metric giả lập cho Predictive Scaling và upload lên CloudWatch | 27/05/2026 | 27/05/2026 | https://000006.awsstudygroup.com/ |
| 5 | - Tạo AMI từ EC2 đã cài đặt ứng dụng <br> - Tạo Launch Template `FCJ-Management-template` từ AMI <br> - Cấu hình Instance Type, Key Pair, Subnet và Security Group trong Launch Template <br> - Tạo Target Group `FCJ-Management-TG` dùng giao thức HTTP và port `5000` | 28/05/2026 | 28/05/2026 | https://000006.awsstudygroup.com/ |
| 6 | - Tạo Application Load Balancer `FCJ-Management-LB` <br> - Chọn Internet-facing, IPv4 và public subnet ở nhiều Availability Zone <br> - Gắn Load Balancer với Target Group <br> - Kiểm tra DNS của Load Balancer, truy cập ứng dụng và test chức năng CRUD | 29/05/2026 | 29/05/2026 | https://000006.awsstudygroup.com/ |
| 7 | - Tạo Auto Scaling Group `FCJ-Management-ASG` từ Launch Template <br> - Cấu hình Desired Capacity, Minimum Capacity và Maximum Capacity <br> - Gắn ASG với Target Group của Load Balancer <br> - Bật Elastic Load Balancing Health Checks và CloudWatch group metrics <br> - Kiểm thử Manual Scaling và Scheduled Scaling | 30/05/2026 | 30/05/2026 | https://000006.awsstudygroup.com/ |
| CN | - Cấu hình Dynamic Scaling Policy dựa trên ALB Request Count Per Target <br> - Tạo Predictive Scaling Policy bằng custom metrics trên CloudWatch <br> - Đọc biểu đồ Load, Capacity và phân tích thời điểm ASG dự đoán cần scale out <br> - Dọn dẹp tài nguyên: ASG, Load Balancer, Target Group, Launch Template, AMI, EC2, RDS, Subnet Group và kiểm tra Billing | 31/05/2026 | 31/05/2026 | https://000006.awsstudygroup.com/ |

---

### Kết quả đạt được tuần 5:

#### Kiến thức

**Amazon EC2 Auto Scaling là gì?**

- Hiểu Amazon EC2 Auto Scaling là dịch vụ giúp tự động tăng hoặc giảm số lượng EC2 Instance dựa trên nhu cầu tải của hệ thống.
- Auto Scaling Group giúp duy trì số lượng instance mong muốn, thay thế instance lỗi và mở rộng tài nguyên khi traffic tăng.
- ASG thường được kết hợp với Application Load Balancer để phân phối request đến nhiều instance khác nhau.
- Mô hình này giúp ứng dụng có tính sẵn sàng cao hơn, chịu lỗi tốt hơn và tối ưu chi phí vận hành.

**Application Load Balancer**

- Hiểu Application Load Balancer hoạt động ở tầng ứng dụng, phù hợp với các ứng dụng HTTP/HTTPS.
- ALB nhận request từ người dùng rồi phân phối đến các EC2 Instance trong Target Group.
- ALB giúp tránh việc người dùng truy cập trực tiếp vào từng EC2 Instance riêng lẻ.
- Khi kết hợp với ASG, ALB tự động cập nhật danh sách target khi ASG tạo hoặc xóa instance.

**Target Group**

- Hiểu Target Group là nhóm các tài nguyên đích mà Load Balancer sẽ chuyển tiếp request đến.
- Trong lab, Target Group sử dụng target type là `Instances`, giao thức `HTTP` và port `5000`.
- Biết cách đăng ký EC2 Instance vào Target Group và kiểm tra trạng thái health check.
- Biết rằng chỉ những target healthy mới được Load Balancer chuyển tiếp traffic.

**AMI và Launch Template**

- Hiểu AMI là bản sao trạng thái của một EC2 Instance, bao gồm hệ điều hành, ứng dụng và các cấu hình đã cài đặt.
- Biết cách tạo AMI từ EC2 đã triển khai ứng dụng FCJ Management.
- Hiểu Launch Template dùng để chuẩn hóa cấu hình khi tạo EC2 Instance mới.
- Launch Template bao gồm AMI, Instance Type, Key Pair, Subnet, Security Group và các thông tin cấu hình cần thiết.
- ASG sử dụng Launch Template để tạo instance mới một cách nhất quán.

**VPC, Subnet và Security Group**

- Hiểu VPC là mạng riêng ảo dùng để cô lập tài nguyên AWS.
- Biết cách tạo VPC `AutoScaling-Lab` với CIDR `10.0.0.0/16`.
- Biết cấu hình nhiều Availability Zone để tăng khả năng chịu lỗi.
- Public Subnet được dùng cho EC2 và Load Balancer cần truy cập Internet.
- Private Subnet được dùng cho RDS nhằm hạn chế truy cập trực tiếp từ Internet.
- Biết tạo Security Group riêng cho ứng dụng và database.
- Application Security Group mở các port cần thiết như SSH `22`, HTTP `80`, HTTPS `443` và application port `5000`.
- Database Security Group chỉ cho phép MySQL/Aurora port `3306` từ Application Security Group.

**Amazon RDS trong mô hình nhiều tầng**

- Hiểu RDS được dùng làm tầng cơ sở dữ liệu cho ứng dụng FCJ Management.
- Biết cách tạo DB Subnet Group để RDS hoạt động trong các private subnet.
- Biết tạo RDS MySQL Instance, cấu hình username, password, database name, Security Group và VPC.
- Biết lấy Endpoint và Port của RDS để cấu hình kết nối trong ứng dụng.
- Hiểu rằng RDS nên được đặt ở private subnet để giảm rủi ro bảo mật.

**Triển khai ứng dụng FCJ Management**

- Biết SSH vào EC2 bằng MobaXterm thông qua Public IPv4.
- Biết cài Git, MariaDB client, Node.js và NPM trên Amazon Linux 2023.
- Biết clone source code từ GitHub về EC2.
- Biết tạo file `.env` để lưu thông tin kết nối database:
  - `DB_HOST`
  - `DB_NAME`
  - `DB_USER`
  - `DB_PASS`
- Biết sử dụng PM2 để chạy ứng dụng Node.js ở chế độ background.
- Biết dùng `pm2 startup` và `pm2 save` để ứng dụng tự chạy lại sau khi EC2 reboot.

**CloudWatch Metrics**

- Hiểu CloudWatch được dùng để theo dõi hiệu năng của EC2, ASG và Load Balancer.
- Biết theo dõi các chỉ số như CPU Utilization, Network In, Network Out, request count và số lượng instance trong ASG.
- Biết upload custom metrics lên CloudWatch để phục vụ Predictive Scaling.
- Hiểu rằng CloudWatch metrics có thể có độ trễ, cần chờ một khoảng thời gian để dữ liệu cập nhật.

**Manual Scaling**

- Hiểu Manual Scaling là cách thay đổi trực tiếp Desired Capacity, Min Capacity hoặc Max Capacity của Auto Scaling Group.
- Khi Desired Capacity tăng, ASG sẽ tạo thêm EC2 Instance.
- Khi Desired Capacity giảm, ASG sẽ terminate bớt EC2 Instance.
- Manual Scaling dễ kiểm soát nhưng cần thao tác thủ công, không phù hợp khi traffic thay đổi bất ngờ.

**Scheduled Scaling**

- Hiểu Scheduled Scaling dùng để tự động thay đổi capacity theo thời gian định trước.
- Phù hợp với hệ thống có tải tăng theo khung giờ cố định, ví dụ giờ cao điểm, giờ làm việc hoặc mùa sale.
- Biết tạo Scheduled Action với tên `Rush hour`, cấu hình Desired Capacity, Min, Max, timezone và thời điểm chạy.
- Hiểu Scheduled Scaling chủ động hơn Manual Scaling nhưng vẫn phụ thuộc vào dự đoán lịch sử hoặc lịch cố định.

**Dynamic Scaling**

- Hiểu Dynamic Scaling tự động scale dựa trên metric thực tế từ CloudWatch.
- Trong lab, chính sách Dynamic Scaling sử dụng Target Tracking Scaling.
- Metric được dùng là Application Load Balancer Request Count Per Target.
- Target value được đặt là `500` request per target và Instance Warmup là `60` giây.
- Khi request tăng vượt ngưỡng, ASG tự tạo thêm instance; khi tải giảm, ASG sẽ scale in sau một khoảng thời gian.

**Predictive Scaling**

- Hiểu Predictive Scaling dùng dữ liệu lịch sử để dự đoán tải trong tương lai.
- ASG có thể dự đoán thời điểm cần tăng hoặc giảm số lượng instance.
- Predictive Scaling thường được kết hợp với Dynamic Scaling để vừa chủ động vừa phản ứng tốt với tải thực tế.
- Biết cấu hình Predictive Scaling Policy bằng custom metric pair gồm Load metric, Scaling metric và Capacity metric.
- Biết đọc biểu đồ Load và Capacity để xác định thời điểm ASG dự đoán cần launch instance mới.

**Dọn dẹp tài nguyên**

- Hiểu việc cleanup sau lab rất quan trọng để tránh phát sinh chi phí AWS.
- Biết xóa Auto Scaling Group, Load Balancer, Target Group, Launch Template, AMI, EC2 Instance, RDS Database và DB Subnet Group.
- Biết kiểm tra lại AWS Billing Dashboard sau khi xóa tài nguyên.
- Lưu ý cần kiểm tra thêm EBS Snapshot hoặc CloudWatch Logs vì các tài nguyên này có thể vẫn còn tồn tại sau khi xóa AMI hoặc EC2.

---

#### Thực hành

**Module 1 — Introduction**

- Đọc tổng quan workshop Deploying FCJ Management Application with Auto Scaling Group.
- Nắm mục tiêu chính là triển khai ứng dụng có khả năng mở rộng tự động.
- Hiểu kiến trúc gồm EC2, RDS, Application Load Balancer, Launch Template, Auto Scaling Group và CloudWatch.
- Xác định luồng hoạt động: người dùng truy cập Load Balancer, Load Balancer chuyển request đến EC2, EC2 kết nối RDS để xử lý dữ liệu.
-  _Ảnh minh chứng: Trang Introduction của workshop 000006._

**Module 2.1 — Setup Network Infrastructure**

- Truy cập AWS Management Console.
- Tạo VPC mới với tên `AutoScaling-Lab`.
- Cấu hình IPv4 CIDR block là `10.0.0.0/16`.
- Chọn 3 Availability Zone để tăng khả năng chịu lỗi.
- Tạo 3 public subnet và 3 private subnet.
- Không tạo NAT Gateway để tiết kiệm chi phí lab.
- Bật auto-assign public IPv4 cho các public subnet.
- Tạo Application Security Group `FCJ-Management-SG`.
- Cấu hình inbound rules:
  - SSH port `22` từ My IP.
  - HTTP port `80` từ Anywhere-IPv4.
  - HTTPS port `443` từ Anywhere-IPv4.
  - Custom TCP port `5000` từ Anywhere-IPv4.
- Tạo Database Security Group `FCJ-Management-DB-SG`.
- Cấu hình inbound rule MySQL/Aurora port `3306`, source là `FCJ-Management-SG`.
-  _Ảnh minh chứng: VPC AutoScaling-Lab đã tạo._
-  _Ảnh minh chứng: Public subnet, private subnet và Security Group._

**Module 2.2 — Launch EC2 Instance**

- Truy cập EC2 Console.
- Tạo EC2 Instance mới tên `FCJ-Management`.
- Chọn Amazon Linux 2023 AMI.
- Chọn instance type `t2.micro`.
- Tạo Key Pair tên `fcj-key`, loại RSA, định dạng `.pem`.
- Chọn VPC `AutoScaling-Lab` và một public subnet.
- Bật Auto-assign public IP.
- Gán Security Group `FCJ-Management-SG`.
- Launch instance và chờ trạng thái `running`.
-  _Ảnh minh chứng: EC2 Instance FCJ-Management ở trạng thái running._

**Module 2.3 — Launch a Database Instance with RDS**

- Truy cập RDS Console.
- Tạo DB Subnet Group tên `FCJ-Management-Subnet-Group`.
- Chọn VPC `AutoScaling-Lab`.
- Chọn các private subnet ở nhiều Availability Zone.
- Tạo RDS Database Instance.
- Chọn Standard create.
- Chọn database engine MySQL.
- Chọn Production template và Multi-AZ DB instance.
- Đặt DB instance identifier là `fcj-management-db-instance`.
- Đặt master username là `admin`.
- Cấu hình master password cho lab.
- Chọn instance class, storage type và allocated storage phù hợp.
- Chọn VPC, DB Subnet Group và Security Group `FCJ-Management-DB-SG`.
- Đặt initial database name là `awsfcjuser`.
- Tạo database và ghi lại Endpoint, Port.
-  _Ảnh minh chứng: DB Subnet Group đã tạo._
-  _Ảnh minh chứng: RDS MySQL Instance ở trạng thái Available._
-  _Ảnh minh chứng: Endpoint và Port của RDS._

**Module 2.4 — Setup data for Database**

- Lấy Public IPv4 của EC2 Instance.
- Kết nối SSH vào EC2 bằng MobaXterm.
- Username sử dụng là `ec2-user`.
- Chọn private key tương ứng với Key Pair `fcj-key`.
- Cài Git bằng lệnh:

```bash
sudo yum install git
```

- Cài MariaDB client bằng lệnh:

```bash
sudo dnf install mariadb105
```

- Kiểm tra phiên bản MySQL client:

```bash
mysql --version
```

- Kết nối đến RDS bằng endpoint, port `3306`, username `admin`.
- Kiểm tra danh sách database bằng lệnh:

```sql
SHOW DATABASES;
```

- Chọn database đã tạo:

```sql
USE awsfcjuser;
```

- Tạo bảng `user` chứa thông tin người dùng.
- Thêm dữ liệu mẫu vào bảng.
- Kiểm tra dữ liệu bằng lệnh:

```sql
SELECT * FROM user;
```

- Thoát khỏi MySQL client sau khi hoàn thành.
-  _Ảnh minh chứng: SSH thành công vào EC2._
-  _Ảnh minh chứng: Kết nối thành công đến RDS._
-  _Ảnh minh chứng: Bảng user đã có dữ liệu mẫu._

**Module 2.5 — Deploy Web Server**

- Cài Node Version Manager bằng lệnh:

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.0/install.sh | bash
```

- Cài Node.js phiên bản 20:

```bash
nvm install 20
```

- Clone source code ứng dụng:

```bash
git clone https://github.com/First-Cloud-Journey/000004-EC2.git
```

- Truy cập thư mục source:

```bash
cd 000004-EC2
```

- Khởi tạo hoặc kiểm tra cấu hình NPM:

```bash
npm init
```

- Cài PM2 global:

```bash
npm install -g pm2
```

- Cấu hình script chạy ứng dụng bằng PM2.
- Tạo file `.env` và nhập thông tin kết nối database.
- Chạy ứng dụng:

```bash
npm start
```

- Kiểm tra trạng thái ứng dụng bằng PM2:

```bash
pm2 status
```

- Lấy Public DNS của EC2 để truy cập ứng dụng.
- Cấu hình PM2 tự khởi động lại ứng dụng khi EC2 reboot:

```bash
pm2 startup
pm2 save
```

-  _Ảnh minh chứng: Node.js đã cài đặt._
-  _Ảnh minh chứng: Source code đã clone thành công._
-  _Ảnh minh chứng: Ứng dụng chạy ổn định qua PM2._

**Module 2.6 — Prepare metric for Predictive Scaling**

- Tạo thư mục chuẩn bị metric:

```bash
mkdir metric-preparation && cd metric-preparation
```

- Tải script chuẩn bị dữ liệu metric:

```bash
curl -o prepare-metric-data.sh https://raw.githubusercontent.com/awslabs/ec2-spot-workshops/master/workshops/efficient-and-resilient-ec2-auto-scaling/prepare-metric-data.sh
```

- Chỉnh sửa script nếu cần.
- Tải dữ liệu CPU metric và instance metric.
- Chạy script để xử lý dữ liệu cho Auto Scaling Group `FCJ-Management-ASG`.
- Cấu hình AWS CLI bằng lệnh:

```bash
aws configure
```

- Upload custom metrics lên CloudWatch:

```bash
aws cloudwatch put-metric-data --namespace 'FCJ Management Custom Metrics' --metric-data file://metric-cpu.json
aws cloudwatch put-metric-data --namespace 'FCJ Management Custom Metrics' --metric-data file://metric-instances.json
```

- Truy cập CloudWatch Console để kiểm tra namespace `FCJ Management Custom Metrics`.
- Chọn dimension `AutoScalingGroupName` và kiểm tra các metric đã upload.
-  _Ảnh minh chứng: Custom metrics đã xuất hiện trên CloudWatch._

**Module 3 — Create Launch Template**

- Truy cập EC2 Console.
- Chọn EC2 Instance `FCJ-Management` đã cài ứng dụng.
- Tạo AMI từ instance này.
- Đặt AMI name là `FCJ-Management-AMI`.
- Chờ AMI chuyển sang trạng thái `Available`.
- Tạo Launch Template mới.
- Đặt Launch Template name là `FCJ-Management-template`.
- Chọn AMI `FCJ-Management-AMI`.
- Chọn instance type `t2.micro`.
- Chọn Key Pair `fcj-key`.
- Chọn public subnet và Security Group `FCJ-Management-SG`.
- Tạo Launch Template và kiểm tra thông tin cấu hình.
-  _Ảnh minh chứng: AMI FCJ-Management-AMI đã Available._
-  _Ảnh minh chứng: Launch Template đã tạo thành công._

**Module 4.1 — Create Target Group**

- Truy cập mục Target Groups trong EC2 Console.
- Tạo Target Group mới.
- Chọn target type là `Instances`.
- Đặt tên Target Group là `FCJ-Management-TG`.
- Chọn giao thức `HTTP`.
- Cấu hình port là `5000`.
- Chọn IP address type là IPv4.
- Chọn VPC `AutoScaling-Lab`.
- Register EC2 Instance hiện có vào Target Group.
- Kiểm tra target đã được thêm vào danh sách pending.
- Tạo Target Group và kiểm tra trạng thái health check.
-  _Ảnh minh chứng: Target Group FCJ-Management-TG đã tạo._
-  _Ảnh minh chứng: EC2 Instance đã được register vào Target Group._

**Module 4.2 — Create Load Balancer**

- Truy cập mục Load Balancers trong EC2 Console.
- Tạo Application Load Balancer mới.
- Đặt tên Load Balancer là `FCJ-Management-LB`.
- Chọn Scheme là Internet-facing.
- Chọn IP address type là IPv4.
- Chọn VPC `AutoScaling-Lab`.
- Chọn public subnet ở nhiều Availability Zone.
- Gán Security Group `FCJ-Management-SG`.
- Cấu hình listener chuyển tiếp request đến Target Group `FCJ-Management-TG`.
- Tạo Load Balancer và chờ trạng thái active.
- Sao chép DNS name của Load Balancer.
-  _Ảnh minh chứng: Application Load Balancer đã tạo._
-  _Ảnh minh chứng: Listener đã forward đến Target Group._

**Module 5 — Test**

- Truy cập Load Balancer DNS bằng trình duyệt.
- Kiểm tra giao diện FCJ Management hiển thị thành công.
- Thực hiện thao tác chỉnh sửa một bản ghi.
- Nhấn Submit để cập nhật dữ liệu.
- Kiểm tra thông báo cập nhật thành công.
- Quay lại trang chủ để xác nhận dữ liệu đã thay đổi.
- Nếu lỗi xảy ra, kiểm tra lại Security Group, Target Group health check, port `5000` và kết nối RDS.
-  _Ảnh minh chứng: Truy cập ứng dụng qua Load Balancer DNS._
-  _Ảnh minh chứng: CRUD hoạt động thành công._

**Module 6 — Create Auto Scaling Group**

- Truy cập Auto Scaling Groups trong EC2 Console.
- Tạo Auto Scaling Group mới.
- Đặt tên là `FCJ-Management-ASG`.
- Chọn Launch Template `FCJ-Management-template`.
- Chọn VPC `AutoScaling-Lab`.
- Chọn tất cả public subnet ở 3 Availability Zone.
- Gắn ASG với Target Group `FCJ-Management-TG`.
- Bật Elastic Load Balancing health checks.
- Bật CloudWatch group metrics collection.
- Cấu hình Group size:
  - Desired capacity: `1`
  - Minimum capacity: `1`
  - Maximum capacity: `3`
- Chưa cấu hình scaling policy ở bước này để phục vụ các phần test sau.
- Cấu hình SNS notification để nhận thông báo khi ASG có sự kiện scale.
- Tạo ASG và xác nhận email subscription nếu có.
- Kiểm tra Activity tab để xem instance được ASG khởi tạo.
-  _Ảnh minh chứng: Auto Scaling Group đã tạo._
-  _Ảnh minh chứng: Instance mới được ASG launch._

**Module 7.1 — Test Manual Scaling Solution**

- Mở Resource Map của Load Balancer để kiểm tra các target hiện tại.
- Cấu hình công cụ load testing với:
  - Test Type: `CLICKS`
  - Run until: `100000`
  - Number of Users: `1000`
  - Click Delay: `1 second`
- Nhập URL là DNS của Load Balancer.
- Bắt đầu load test.
- Quan sát metric của các EC2 Instance trong tab Monitoring.
- Theo dõi CPU Utilization, Network In, Network Out, Network Packets In và Network Packets Out.
- Thử scale down bằng cách chỉnh Desired Capacity và Min Desired Capacity về `0`.
- Quan sát ASG terminate instance trong Activity tab.
- Kiểm tra hiệu năng khi chỉ còn một target xử lý traffic.
-  _Ảnh minh chứng: Manual Scaling trước và sau khi scale down._
-  _Ảnh minh chứng: CloudWatch metrics thay đổi khi số lượng instance giảm._

**Module 7.2 — Test Scheduled Scaling Solution**

- Vào tab Automatic Scaling của Auto Scaling Group.
- Tạo Scheduled Action mới.
- Đặt tên là `Rush hour`.
- Cấu hình Desired Capacity là `1`, Min là `1`, Max là `3`.
- Chọn Recurrence là Once hoặc cấu hình theo lịch phù hợp.
- Chọn Time zone là `Asia/Ho_Chi_Minh`.
- Đặt thời điểm chạy gần thời gian hiện tại để dễ kiểm thử.
- Bắt đầu load test trước thời điểm scheduled action chạy khoảng 5 phút.
- Quan sát Activity tab để kiểm tra sự kiện `Executing scheduled action Rush hour`.
- Kiểm tra CPU metrics trước và sau khi instance mới được tạo.
-  _Ảnh minh chứng: Scheduled Action đã tạo._
-  _Ảnh minh chứng: ASG tự scale theo lịch._

**Module 7.3 — Test Dynamic Scaling Solution**

- Đưa ASG về trạng thái baseline để chuẩn bị kiểm thử.
- Vào tab Automatic Scaling của ASG.
- Tạo Dynamic Scaling Policy.
- Chọn Policy type là Target Tracking Scaling.
- Đặt tên policy là `Request Over 500 per target`.
- Chọn Metric type là Application Load Balancer Request Count Per Target.
- Chọn Target Group `FCJ-Management-TG`.
- Đặt Target value là `500`.
- Đặt Instance Warmup là `60` giây.
- Chạy lại load test để tạo traffic.
- Quan sát ASG Activity tab để xem quá trình scale out.
- Khi request tăng, ASG có thể tạo thêm instance đến giới hạn Max Capacity.
- Dừng load test để quan sát scale in.
- Ghi nhận rằng scale in có cooldown nên instance không bị xóa ngay lập tức.
-  _Ảnh minh chứng: Dynamic Scaling Policy đã tạo._
-  _Ảnh minh chứng: ASG scale out khi request tăng._
-  _Ảnh minh chứng: ASG scale in khi tải giảm._

**Module 7.4 — Read Metrics of Predictive Scaling Solution**

- Scale in thủ công để đưa môi trường về trạng thái phù hợp.
- Xóa dynamic scaling policy để tránh ảnh hưởng đến bài test Predictive Scaling.
- Tạo Predictive Scaling Policy mới.
- Đặt tên policy là `PredictCPUUtilizationAt15Percent`.
- Bật tùy chọn Scale based on forecast.
- Chọn Custom metric pair.
- Cấu hình Load metric bằng custom metric `WSCustomCPUUTILIZATION` trong namespace `FCJ Management Custom Metrics`.
- Cấu hình Scaling metric bằng giá trị Average CPU Utilization trong ASG.
- Cấu hình Capacity metric bằng custom metric `WSCustomGroupInstances`.
- Bật Add custom capacity metric.
- Cấu hình Pre-launch instances là `1 minute`.
- Tạo policy và quan sát biểu đồ Load, Capacity.
- Đọc biểu đồ theo UTC+0 và cộng thêm 7 giờ để quy đổi sang giờ Việt Nam.
- Phân tích thời điểm hệ thống dự đoán tải tăng và số lượng instance cần launch.
- Ghi nhận Predictive Scaling có thể kết hợp với Dynamic Scaling để tăng độ linh hoạt và độ tin cậy.
-  _Ảnh minh chứng: Predictive Scaling Policy đã tạo._
-  _Ảnh minh chứng: Biểu đồ Load và Capacity._
-  _Ảnh minh chứng: ASG launch instance trước thời điểm dự đoán tải tăng._

**Module 8 — Cleanup Resources**

- Xóa Auto Scaling Group `FCJ-Management-ASG`.
- Xóa Application Load Balancer `FCJ-Management-LB`.
- Xóa Target Group `FCJ-Management-TG`.
- Xóa Launch Template đã tạo.
- Deregister AMI `FCJ-Management-AMI`.
- Kiểm tra và xóa EBS Snapshot nếu không còn sử dụng.
- Terminate EC2 Instance `FCJ-Management`.
- Tắt deletion protection của RDS nếu đang bật.
- Xóa RDS Database Instance `fcj-management-db-instance`.
- Xóa DB Subnet Group.
- Kiểm tra lại Billing Dashboard để tránh phát sinh chi phí.
-  _Ảnh minh chứng: Các tài nguyên đã được xóa._
-  _Ảnh minh chứng: Billing Dashboard không phát sinh chi phí bất thường._

---

### Đánh giá kết quả tuần 5:

- Hoàn thành việc tìm hiểu và triển khai mô hình ứng dụng FCJ Management có khả năng mở rộng tự động.
- Nắm được cách xây dựng hệ thống nhiều tầng gồm Load Balancer, EC2 Application Server và RDS Database.
- Biết cách tạo AMI và Launch Template để chuẩn hóa quá trình tạo EC2 Instance mới.
- Biết cách tạo Application Load Balancer và Target Group để phân phối request đến nhiều instance.
- Tạo được Auto Scaling Group và kiểm tra cách ASG tự động launch hoặc terminate instance.
- Hiểu sự khác nhau giữa Manual Scaling, Scheduled Scaling, Dynamic Scaling và Predictive Scaling.
- Biết sử dụng CloudWatch để theo dõi hiệu năng và hỗ trợ quyết định scaling.
- Biết cleanup tài nguyên đúng thứ tự để tránh phát sinh chi phí.

---

### Khó khăn gặp phải:

- Cần kiểm tra kỹ Security Group vì ứng dụng sử dụng port `5000`, nếu chưa mở port thì Load Balancer không truy cập được ứng dụng.
- Khi tạo RDS cần chọn đúng VPC, DB Subnet Group và Database Security Group.
- Khi tạo Target Group, cần chọn đúng protocol, port và VPC để health check hoạt động.
- Khi tạo Launch Template, nếu AMI chưa ở trạng thái `Available` thì không nên tạo ASG ngay.
- CloudWatch metrics có độ trễ nên cần chờ trước khi kết luận scaling policy hoạt động hay chưa.
- Predictive Scaling cần dữ liệu lịch sử, vì vậy phải chuẩn bị custom metrics giả lập trước khi kiểm thử.
- Cleanup cần cẩn thận vì một số tài nguyên như AMI Snapshot, CloudWatch Logs hoặc RDS Snapshot có thể vẫn còn tồn tại.

---

### Hướng khắc phục:

- Kiểm tra lại inbound rules của Security Group trước khi test ứng dụng.
- Đảm bảo RDS chỉ cho phép kết nối từ Application Security Group thay vì mở rộng ra Internet.
- Kiểm tra Target Group health check trước khi truy cập Load Balancer DNS.
- Chờ AMI hoàn tất trạng thái `Available` trước khi tạo Launch Template hoặc Auto Scaling Group.
- Khi test Dynamic Scaling, cần tạo đủ traffic và theo dõi Activity tab của ASG thay vì chỉ nhìn biểu đồ ngay lập tức.
- Khi test Predictive Scaling, cần upload đúng namespace, metric name và dimension lên CloudWatch.
- Sau khi hoàn thành lab, nên kiểm tra Billing Dashboard và danh sách tài nguyên ở từng dịch vụ AWS.

---

### Định hướng tuần tiếp theo:

- Tiếp tục tìm hiểu các kiến trúc triển khai ứng dụng có tính sẵn sàng cao trên AWS.
- Nghiên cứu thêm về CloudWatch Alarm và cách kết hợp alarm với Auto Scaling Policy.
- Tìm hiểu cách dùng HTTPS Listener với AWS Certificate Manager cho Application Load Balancer.
- Tìm hiểu thêm về AWS WAF để bảo vệ ứng dụng web khi triển khai public qua Load Balancer.
- Thực hành tối ưu chi phí khi sử dụng EC2, RDS, Load Balancer và Auto Scaling Group.
- Chuẩn bị nội dung báo cáo, ảnh minh chứng và nhận xét kết quả triển khai cho tuần tiếp theo.
