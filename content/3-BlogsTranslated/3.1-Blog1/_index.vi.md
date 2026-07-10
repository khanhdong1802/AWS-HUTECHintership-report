---
title: "Blog 1"
date: 2026-07-02
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---
# Truy cập Amazon S3 riêng tư từ VPC bằng VPC Endpoint

_Bài chia sẻ được thực hiện bởi nhóm Otokoshi trong quá trình học và thực hành AWS._

Khi triển khai các mô hình demo trên AWS, cách tiếp cận đơn giản nhất để một EC2 Instance có thể upload/download dữ liệu từ Amazon S3 là đặt EC2 trong public subnet, gắn public IP, mở route ra Internet Gateway và sử dụng AWS CLI hoặc SDK.

Tuy nhiên, đối với các hệ thống production (backend, internal services, data processing) chạy trong **private subnet**, việc định tuyến dữ liệu qua public internet không phải là giải pháp tối ưu về mặt bảo mật và chi phí. Bài viết này sẽ hướng dẫn cách thiết kế mô hình truy cập Amazon S3 hoàn toàn riêng tư từ bên trong VPC sử dụng **Gateway VPC Endpoint**.

---

## Bối cảnh và Bài toán đặt ra

Giả sử hệ thống có một backend nội bộ chạy trên EC2 nằm trong private subnet, không được expose trực tiếp ra internet để đảm bảo an toàn. Backend này cần thực hiện các tác vụ:

- Lưu trữ file log hệ thống.
- Xuất (export) báo cáo định kỳ.
- Sao lưu (backup) cấu hình và dữ liệu batch lên Amazon S3.

Nếu không có đường internet, EC2 không thể kết nối tới S3 endpoint mặc định. Thay vì cấu hình cho private subnet đi ra ngoài qua NAT Gateway (gây tốn chi phí băng thông), chúng ta có thể sử dụng **Gateway VPC Endpoint cho S3** để tạo một đường truyền nội bộ, an toàn và tối ưu hơn.

---

## Kiến trúc tổng quan và Luồng xử lý

Mô hình MVP (Minimum Viable Product) bao gồm các thành phần cốt lõi sau:

- **EC2 Instance**: Nằm trong private subnet (không có public IP).
- **Amazon S3 Bucket**: Lưu trữ dữ liệu đích, kích hoạt tính năng _Block Public Access_.
- **Gateway VPC Endpoint**: Điểm cuối VPC dạng Gateway dành cho Amazon S3.
- **Route Table**: Bảng định tuyến của private subnet được cấu hình để điều hướng traffic đến S3 đi qua endpoint.
- **IAM & Policies**: Gồm IAM Role cho EC2, Endpoint Policy và Bucket Policy để kiểm soát quyền truy cập.

### Luồng xử lý dữ liệu (Workflow):

1. EC2 Instance trong private subnet gửi request (đọc/ghi) đến Amazon S3.
2. Route table của subnet nhận biết traffic có đích đến là S3 (thông qua Prefix List của AWS) và định tuyến request qua **Gateway VPC Endpoint**.
3. Amazon S3 tiếp nhận request và tiến hành xác thực đa lớp: kiểm tra IAM Role của EC2, Endpoint policy và Bucket policy.
4. Nếu tất cả các lớp bảo mật hợp lệ, EC2 có thể tương tác với bucket mà không cần đi qua Internet Gateway hoặc NAT Gateway.

   ![Ảnh minh họa](/AWS-HUTECHintership-report/images/3-BlogsTranslated/Blog1/blog1(0).jpg)
---

## Lựa chọn giải pháp: VPC Endpoint vs NAT Gateway

VPC Endpoint không thay thế hoàn toàn NAT Gateway. Dưới đây là bảng so sánh để làm rõ vai trò của từng công nghệ trong kiến trúc Network:

| Tiêu chí                 | Gateway VPC Endpoint (S3/DynamoDB)                                | NAT Gateway                                                       |
| ------------------------ | ----------------------------------------------------------------- | ----------------------------------------------------------------- |
| **Phạm vi kết nối**      | Chỉ kết nối riêng tư tới dịch vụ AWS được hỗ trợ (S3, DynamoDB).  | Kết nối ra toàn bộ internet (gọi API ngoài, cập nhật package...). |
| **Đường đi của traffic** | Hoàn toàn nằm trong mạng nội bộ của AWS (AWS backbone network).   | Đi qua môi trường internet public.                                |
| **Chi phí**              | **Miễn phí** giờ chạy và chi phí xử lý dữ liệu (Data processing). | Tính phí theo giờ chạy và phí xử lý dữ liệu trên mỗi GB.          |
| **Cấu hình Network**     | Cập nhật Route Table với target là Endpoint ID (`vpce-xxx`).      | Cập nhật Route Table với target là NAT Gateway ID (`nat-xxx`).    |

---

## Các điểm lưu ý quan trọng về Bảo mật (Security Layers)

> **Mẹo kiến trúc:** VPC Endpoint chỉ giải quyết bài toán về đường đi của Network (Network Connectivity), không tự động cấp quyền truy cập dữ liệu. Để hệ thống hoạt động ổn định và an toàn, cần kết hợp chặt chẽ cơ chế **Phòng thủ chiều sâu (Defense in Depth)** qua 3 lớp Policy:

- **IAM Role (EC2)**: Xác định bản thân EC2 Instance được phép thực hiện những hành động nào trên S3 (Ví dụ: `s3:GetObject`, `s3:PutObject`).
- **Endpoint Policy**: Giới hạn những hành động hoặc những bucket nào được phép truyền tải traffic đi qua endpoint này.
- **Bucket Policy**: Kiểm soát các request đi vào bucket. Để thắt chặt bảo mật, nên cấu hình bucket policy chỉ chấp nhận request đến từ một VPC Endpoint cụ thể bằng điều kiện `aws:sourceVpce`.

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "Allow-Access-From-Specific-VPCE-Only",
      "Effect": "Deny",
      "Principal": "*",
      "Action": "s3:*",
      "Resource": [
        "arn:aws:s3:::your-bucket-name",
        "arn:aws:s3:::your-bucket-name/*"
      ],
      "Condition": {
        "StringNotEquals": {
          "aws:sourceVpce": "vpce-1a2b3c4d"
        }
      }
    }
  ]
}
```

### Kinh nghiệm Debug khi gặp lỗi kết nối:

Khi EC2 không thể kết nối hoặc bị từ chối truy cập S3, không nên chỉ kiểm tra mã nguồn. Hãy rà soát tuần tự theo các checklist sau:

- [ ] **IAM Role**: EC2 đã được gắn đúng IAM Role với quyền hạn tối thiểu (Least Privilege) chưa?
- [ ] **Route Table**: Private subnet đã có route chỉ định traffic S3 qua `vpce-xxx` chưa?
- [ ] **DNS & Region**: Tên bucket và region trong code đã chính xác chưa?
- [ ] **Policies**: Có cấu hình `Deny` nhầm lẫn nào trong Bucket Policy hoặc Endpoint Policy không?

---

## Bài học rút ra

Qua quá trình thực hành triển khai mô hình truy cập Amazon S3 riêng tư, nhóm rút ra 3 bài học kinh nghiệm quan trọng:

1. **Private Subnet không cô lập hoàn toàn tài nguyên**: Nếu được thiết kế network đúng cách với VPC Endpoint, các tài nguyên bên trong private subnet vẫn dễ dàng giao tiếp với các dịch vụ AWS cần thiết một cách an toàn.
2. **Tuy duy bảo mật tích hợp**: VPC Endpoint không đơn thuần là một cấu hình network, nó giúp thu hẹp bề mặt tấn công (attack surface) bằng cách loại bỏ hoàn toàn nhu cầu mở kết nối public internet khi không cần thiết.
3. **Sự phối hợp của nhiều dịch vụ**: Một hệ thống Cloud chuẩn chỉnh là sự giao thoa kiến thức giữa Network (Route table, Endpoint) và Security (IAM, Bucket Policy, Logging). Hiểu rõ request đi qua đâu và quyền được kiểm soát ở lớp nào chính là chìa khóa để vận hành hệ thống an toàn, tối ưu.

## Ảnh chụp màn hình bài blog:
![Ảnh chụp màn hình](/AWS-HUTECHintership-report/images/3-BlogsTranslated/Blog1/blog1(1).jpg)

## LinkBlog: [Link blog](https://www.facebook.com/groups/awsstudygroupfcj/posts/2201893240575636/?notif_id=1782978457405305&notif_t=tagged_with_story&ref=notif)

