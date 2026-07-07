---
title: "Worklog Tuần 2"
date: 2026-04-27
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

### Mục tiêu tuần 2:

- Tìm hiểu tổng quan về Amazon VPC và các thành phần cốt lõi trong hạ tầng mạng AWS (Subnet, Route Table, Internet Gateway, NAT Gateway).
- Thiết lập hệ thống mạng VPC phân bổ trên 2 Availability Zones (AZs) đáp ứng tiêu chuẩn Public và Private Subnets.
- Thực hành và so sánh 3 phương thức xác thực và kết nối an toàn vào EC2 Instance Private: SSH qua Bastion Host, EC2 Instance Connect (EIC) Endpoint, và AWS Systems Manager (SSM) Session Manager.
- Làm quen với công cụ VPC Reachability Analyzer để kiểm tra và debug logic đường đi của network traffic.
- Nâng cao tư duy quản trị chi phí (Cost Optimization) thông qua việc quản lý và dọn dẹp các tài nguyên mạng có phát sinh phí sau khi hoàn thành lab.

---

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Chuẩn bị tài khoản AWS, chọn Region `us-east-1` <br> - Đọc tài liệu tổng quan mạng Amazon VPC và AWS VPN Site-to-Site <br> - Chuẩn bị Key Pair và môi trường kết nối | 27/04/2026 | 27/04/2026 | https://000003.awsstudygroup.com/vi/ |
| 3 | - Thiết lập hạ tầng VPC core (CIDR `10.10.0.0/16`) <br> - Tạo 4 Subnets phân bổ trên 2 Availability Zones (AZ 1a, 1b) <br> - Khởi tạo và attach Internet Gateway (IGW) vào VPC | 28/04/2026 | 28/04/2026 | https://000003.awsstudygroup.com/vi/ |
| 4 | - Triển khai NAT Gateway tại Public Subnet 1 ở chế độ Zonal mode <br> - Cấp phát và gắn Elastic IP cho NAT Gateway <br> - Cấu hình Route Table cho Public Subnet và Private Subnet | 29/04/2026 | 29/04/2026 | https://000003.awsstudygroup.com/vi/ |
| 5 | - Khởi tạo EC2 Public và EC2 Private (t3.micro, Amazon Linux 2023) <br> - Thao tác kết nối truyền thống qua phương thức SSH Jump qua Bastion Host <br> - Kiểm tra hoạt động mạng của Private Subnet qua NAT Gateway bằng lệnh ping internet | 30/04/2026 | 30/04/2026 | https://000003.awsstudygroup.com/vi/ |
| 6 | - Sử dụng công cụ VPC Reachability Analyzer để kiểm tra liên thông mạng <br> - Phân tích logic đường đi của các packet dữ liệu giữa EC2 Public và EC2 Private | 01/05/2026 | 01/05/2026 | https://000003.awsstudygroup.com/vi/ |
| 7 | - Khởi tạo và cấu hình dịch vụ EC2 Instance Connect (EIC) Endpoint trong Private Subnet <br> - Cấu hình Security Group và kiểm tra kết nối trực tiếp vào EC2 Private không qua internet công cộng | 02/05/2026 | 02/05/2026 | https://000003.awsstudygroup.com/vi/ |
| CN | - Cấu hình IAM Role và thiết lập 3 VPC Interface Endpoints để chạy SSM Session Manager <br> - Thực hành kết nối quản trị Shell trực tiếp qua trình duyệt web <br> - Tiến hành dọn dẹp tài nguyên (Clean up) để tối ưu hóa chi phí hệ thống | 03/05/2026 | 03/05/2026 | https://000003.awsstudygroup.com/vi/ |

---

### Kết quả đạt được tuần 2:

#### Kiến thức

**Amazon VPC và Hạ tầng mạng Core**
- Hiểu VPC là mạng riêng ảo giúp cô lập hoàn toàn tài nguyên của bạn trên hạ tầng điện toán đám mây AWS.
- Phân biệt rõ ràng tính chất của Public Subnet (gắn liền với Internet Gateway) và Private Subnet (chỉ kết nối nội bộ hoặc ra ngoài internet thông qua cơ chế NAT).
- Hiểu Route Table hoạt động đóng vai trò như hệ thống "biển chỉ đường" điều hướng packet. Route mặc định `10.10.0.0/16 -> local` đảm bảo mọi traffic nội bộ luôn đi qua hạ tầng backbone an toàn của AWS.

**Internet Gateway và NAT Gateway**
- Internet Gateway (IGW) đóng vai trò là cánh cổng hai chiều (Inbound và Outbound) cho phép Public Subnet kết nối trực tiếp với Internet công cộng.
- NAT Gateway là cánh cổng một chiều (Outbound-only) giúp các máy chủ trong Private Subnet đi ra ngoài Internet (để cập nhật phần mềm, tải thư viện, gọi API) nhưng ngăn chặn hoàn toàn chiều ngược lại xâm nhập từ bên ngoài.
- Nắm vững tính năng cấu hình Availability mode (Regional vs Zonal) mới cập nhật của NAT Gateway và hiểu rõ bản chất chịu lỗi (HA) trong môi trường Production thực tế.

**VPC Reachability Analyzer**
- Biết cách sử dụng công cụ VPC Reachability Analyzer để phân tích tính liên thông mạng "logic" giữa các tài nguyên đám mây mà không cần thực tế gửi gói tin đi.
- Giúp cô lập và debug nhanh chóng các rào cản do thiết lập sai sót tại Route Table, Security Group hay Network ACL (NACL).

**3 Giải pháp kết nối và quản trị EC2 Private**
- **SSH Jump qua Bastion Host (Truyền thống):** Sử dụng một máy chủ EC2 Public làm trạm trung chuyển để kết nối. Giải pháp này yêu cầu người dùng phải tự quản lý file key pair thủ công, mở cổng port 22 ra ngoài internet và phải tuân thủ phân quyền tệp tin chặt chẽ (`chmod 400`).
- **EC2 Instance Connect (EIC) Endpoint (Hiện đại):** Một endpoint ảo do AWS quản lý trực tiếp trong VPC, giúp thiết lập phiên SSH qua đường mạng nội bộ backbone của AWS. Hoàn toàn miễn phí, không yêu cầu IP Public, NAT Gateway hay Bastion host, đồng thời tích hợp sẵn audit logging qua CloudTrail.
- **SSM Session Manager (Zero Trust):** Cho phép truy cập và mở terminal quản lý EC2 trực tiếp ngay trên trình duyệt web qua cơ chế SSM Agent và IAM Role (`AmazonSSMManagedInstanceCore`). Phương pháp này đạt độ an toàn tối đa khi không cần mở bất kỳ cổng inbound nào ra Internet công cộng, tuy nhiên cần đầu tư chi phí cố định cho các Interface Endpoints (`ssm`, `ssmmessages`, `ec2messages`).

**Quản lý chi phí (Cost Optimization)**
- Hiểu được tầm quan trọng của tư duy tối ưu hóa chi phí trên Cloud. Nhận diện các tài nguyên ngốn ngân sách chạy ngầm như NAT Gateway (~$32/tháng), các Interface Endpoints hay các Elastic IP bị treo không gắn với resource nào để thực hiện dọn dẹp kịp thời.

---

#### Thực hành

**Module 1 — Dựng hạ tầng mạng VPC Core**
- Thiết lập hệ thống mạng VPC mang dải CIDR `10.10.0.0/16` tại khu vực `us-east-1` (N. Virginia).
- Phân bổ thành công 4 Subnets chia đều trên 2 Availability Zones (1a, 1b) đáp ứng cấu hình Public và Private Subnets.
- Tạo và attach Internet Gateway (IGW) trực tiếp vào VPC.
- Khởi tạo thành công một NAT Gateway nằm tại Public Subnet 1 theo chế độ Zonal mode và liên kết với một Elastic IP.
- Cấu hình chính xác `Route table-Public` trỏ route mặc định `0.0.0.0/0` về IGW và `Route table-Private` trỏ route `0.0.0.0/0` về hệ thống NAT Gateway.
- 📸 _Ảnh minh chứng: Danh sách VPC, Subnets và các Route Tables định tuyến thành công._
- 📸 _Ảnh minh chứng: NAT Gateway ở trạng thái Available đi kèm với Elastic IP._

**Module 2 — Kiểm tra xác thực kết nối truyền thống qua Bastion Host**
- Khởi chạy thành công 2 EC2 Instance (`t3.micro`, Amazon Linux 2023) bao gồm một máy Public (`10.10.1.131`) và một máy Private (`10.10.3.142`) sử dụng chung key pair `aws-keypair-v2`.
- Thực hiện cấu hình SSH trung chuyển từ máy cá nhân thông qua Bastion Host để truy cập terminal vào EC2 Private. Thực hiện lệnh an toàn `chmod 400` cho file tệp tin key `.pem`.
- Chạy lệnh kiểm tra `ping -c 4 google.com` tại máy Private để xác nhận gói tin mạng đi qua NAT Gateway và nhận phản hồi thành công.
- 📸 _Ảnh minh chứng: Thông tin trạng thái running của 2 EC2 Instance trên bảng điều khiển._
- 📸 _Ảnh minh chứng: Terminal truy cập thành công vào EC2 Private qua Bastion Host và kết quả lệnh ping._

**Module 3 — Debug liên thông mạng với VPC Reachability Analyzer**
- Khởi tạo một phân tích kiểm tra luồng đi của dữ liệu từ máy EC2 Public tới máy EC2 Private.
- Kiểm tra các quy tắc của Security Group và nhận kết quả phân tích hệ thống báo trạng thái `Reachable`.
- 📸 _Ảnh minh chứng: Giao diện hiển thị kết quả Reachable trên VPC Reachability Analyzer._

**Module 4 — Triển khai giải pháp kết nối bằng EC2 Instance Connect (EIC) Endpoint**
- Tạo một EIC Endpoint (`eice-0f7c...`) đặt tại dải mạng Private Subnet.
- Tiến hành cập nhật Inbound rule trên Security Group của EC2 Private, chỉ chấp nhận truy cập cổng port 22 đi từ Security Group của chính EIC Endpoint.
- Thực hiện kết nối SSH trực tiếp vào hệ điều hành của EC2 Private từ Console AWS mà không cần thông qua môi trường internet công cộng hay Bastion Host.
- 📸 _Ảnh minh chứng: Thông tin EIC Endpoint đã tạo thành công ở trạng thái Available._
- 📸 _Ảnh minh chứng: Cửa sổ terminal SSH kết nối vào Private Instance qua giải pháp EIC Endpoint._

**Module 5 — Thiết lập giải pháp Zero Trust với SSM Session Manager**
- Tạo một IAM Role cấp quyền quản lý hệ thống đám mây chứa managed policy `AmazonSSMManagedInstanceCore` và attach trực tiếp vào cả hai máy chủ EC2.
- Tạo lập 3 VPC Interface Endpoints (`ssm`, `ssmmessages`, `ec2messages`) sử dụng AWS PrivateLink đặt tại Private Subnet, kích hoạt tùy chọn Enable DNS name và mở port 443 inbound cho toàn dải mạng `10.10.0.0/16`.
- Đăng nhập điều khiển máy chủ EC2 Private thông qua tính năng Session Manager trực tiếp ngay trên giao diện trình duyệt web mà không cần key pair hay mở cổng SSH.
- 📸 _Ảnh minh chứng: Trạng thái Available của 3 VPC Interface Endpoints._
- 📸 _Ảnh minh chứng: Giao diện terminal Session Manager chạy trực tiếp trên Web Browser._

**Module 6 — Thực hiện dọn dẹp tài nguyên (Clean up)**
- Thực hiện xóa bỏ (Delete) NAT Gateway để dừng tính phí giờ chạy hệ thống.
- Thực hiện giải phóng hoàn toàn (Release) Elastic IP trống để tránh phát sinh chi phí IP treo tài nguyên.
- Xóa bỏ hoàn toàn 3 SSM VPC Interface Endpoints đã thiết lập cho lab.
- Giữ lại hệ thống core VPC và EIC Endpoint (hoàn toàn miễn phí) nhằm tái sử dụng cho các bài lab tuần tiếp theo.
- 📸 _Ảnh minh chứng: Các tài nguyên NAT Gateway và VPC Endpoints đã được xóa sạch trên hệ thống._

---

#### Khó khăn và cách giải quyết

**Khó khăn:**
- Dễ bị nhầm lẫn về bản chất hoạt động và cách cấu hình bảng định tuyến giữa Internet Gateway (hai chiều) và NAT Gateway (một chiều).
- Gặp lỗi từ chối SSH kết nối `WARNING: UNPROTECTED PRIVATE KEY FILE!` khi copy file key pair `.pem` lên máy trung chuyển Bastion Host mà quên cấu hình lại quyền hạn tệp tin.
- Hệ thống SSM Agent trên máy EC2 Private không thể đăng ký thành công lên dịch vụ Systems Manager do người dùng quên tích chọn mục "Enable DNS name" khi tạo lập các Interface Endpoints.
- Nguy cơ cao bị phát sinh hóa đơn chi phí (Billing) lớn chạy ngầm từ các tài nguyên tính phí theo giờ như NAT Gateway (~$32/tháng) và Interface Endpoints nếu sơ suất quên tắt/xóa sau khi hoàn thành lab.

**Cách giải quyết:**
- Hệ thống hóa lại kiến thức bằng cách tự vẽ sơ đồ tư duy luồng đi của traffic từ môi trường internet qua từng gateway và subnet để nắm sâu sắc cấu trúc hạ tầng.
- Thực hiện tuân thủ quy chuẩn bảo mật an toàn hệ thống Linux, chạy ngay lệnh `chmod 400 aws-keypair-v2.pem` ngay sau khi tải tệp tin key pair lên server.
- Đọc kỹ hướng dẫn thực hành lab, kiểm tra chi tiết các thuộc tính phân giải DNS của VPC và cấu hình chính xác inbound port 443 cho Security Group của các endpoint.
- Thiết lập danh sách checklist dọn dẹp tài nguyên bài bản sau mỗi buổi học, kiểm tra kỹ lường Billing Dashboard định kỳ để phát hiện và ngăn chặn sớm các tài nguyên chưa tắt.

```
