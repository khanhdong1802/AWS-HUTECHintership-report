---
title: "Event 4"
date: 2026-06-13
weight: 4
chapter: false
pre: " <b> 4.4. </b> "
---

# Bài thu hoạch “FCAJ Community Day - June 2026 Edition”

### Mục Đích Của Sự Kiện
- **Thiết kế hệ thống chịu tải cao (High-scale Architecture)**: Giới thiệu kiến trúc dịch vụ rút gọn liên kết (URL Shortener) có khả năng mở rộng linh hoạt trên hệ sinh thái AWS.
* **Định hướng phát triển năng lực dữ liệu**: Phân tích thực tế công việc, bộ kỹ năng cốt lõi và lộ trình tư duy dành cho một kỹ sư phân tích dữ liệu (Data Analytics Engineer) trong các tập đoàn đa quốc gia (MNCs).
- **Giải mã vai trò DevOps thực chiến**: Đánh giá bức tranh thị trường, mức thu nhập, nhu cầu tuyển dụng và các bộ kỹ năng nền tảng không thể thay đổi của một kỹ sư DevOps chuyên nghiệp.
- **Kết nối chuỗi giá trị và tư duy công dân**: Chia sẻ hành trình từ một sinh viên tò mò đến đối tác công nghệ của AWS, kết hợp triết lý "Đúng việc" đóng góp vào huyết mạch số quốc gia.

### Danh Sách Diễn Giả

- **Anh Đinh Trung Kiên** - Lead Developer tại Startup.
- **Bạn Nguyễn Minh Thọ** - Student & Cloud Contributor.
- **Anh Dat Pham** - Data Analytics Engineer tại Tập đoàn Đa quốc gia.
- **Anh Cường Nguyễn** - Process Engineer.
- **Anh Trong H.Truong** - DevOps Engineer tại Endava Vietnam.
- **Anh Danh Hoàng Hiếu Nghị** - AI Engineer, AWS Community Builder & SBG Leader.

---

### Nội Dung Nổi Bật

#### 1.Kiến trúc hệ thống rút gọn URL quy mô lớn (Scalable URL Shortener) trên AWS
- **Nỗi đau hệ thống truyền thống (Naive Flow)**: Quy trình tạo mã rút gọn On-demand truyền thống thường gặp các vấn đề lớn về độ trễ đọc dữ liệu (Read Latency), lỗ hổng bảo mật, điểm nghẽn đơn nhất (Single Point of Failure), và rất khó để mở rộng quy mô khi lưu lượng truy cập đột biến.
- **Kiến trúc Key Generation Service (KGS) tiên tiến**:
- *Tách biệt luồng xử lý (Separation of Concerns)*: Nhánh Đọc (Read path) và Nhánh Ghi (Write path) hoạt động hoàn toàn độc lập, tối ưu hóa riêng biệt theo từng đặc điểm traffic.
- *Tiền tính toán (Pre-computation over On-demand)*: Sử dụng các Container trên **Amazon ECS** để tính toán và sinh sẵn các mã rút gọn (Short codes) trước khi có yêu cầu từ người dùng. Các mã này sau đó được đẩy liên tục vào hàng đợi của **Amazon ElastiCache for Redis** thông qua lệnh `LPUSH key_queue`.
- *Quy trình khởi tạo siêu tốc*: Khi người dùng gửi yêu cầu rút gọn link, dịch vụ Backend (SpringBoot chạy trên ECS) chỉ cần thực hiện lệnh `RPOP` để lấy ngay mã đã sinh sẵn trong Redis và ghi thông tin vào **Amazon DynamoDB** làm khóa chính (Primary Key - PK). Quá trình này diễn ra ngay lập tức và loại bỏ hoàn toàn rủi ro xung đột mã (Collision-free).
- *Mô hình Cache-aside Pattern*: Luồng chuyển hướng (Forward flow) sẽ ưu tiên tra cứu trong bộ nhớ đệm Redis Server trước (Cache hit). Hệ thống chỉ truy vấn xuống cơ sở dữ liệu DynamoDB khi xảy ra hiện tượng Cache miss, giúp giảm thiểu tối đa áp lực tải và giữ độ trễ ở mức thấp nhất.
- *Phòng ngự tại vùng biên (Defense at the Edge)*: Đẩy toàn bộ lớp bảo mật **AWS WAF** và lớp bộ nhớ đệm phân phối tĩnh của **Amazon CloudFront** ra sát người dùng nhất có thể, chặn đứng các mối đe dọa trước khi chúng chạm vào vùng lõi hệ thống.

#### 2.Công việc thực tế và Tư duy phát triển của Data Analytics Engineer tại MNCs

- **Sự khác biệt về domain công việc**:
- Tại các công ty Tech/E-commerce (như Kamereo): Tập trung xây dựng báo cáo định kỳ để theo dõi hiệu suất vận hành, thiết kế Dashboard quản lý xu hướng dữ liệu để phát hiện bất thường và phối hợp đa phòng ban tìm nguyên nhân gốc rễ (Root Cause) để tối ưu hóa GMV.
- Tại các tập đoàn sản xuất (như Colgate-Palmolive): Tham gia vào các dự án dữ liệu máy móc, thiết bị IoT trong nhà máy để tìm cơ hội tối ưu chi phí sản xuất và nâng cao hiệu suất thiết bị.
- **4 kỹ năng bắt buộc**: Tư duy phản biện (Critical thinking), Kỹ năng giao tiếp điều phối, Kể chuyện với dữ liệu (Data storytelling), và Năng lực giải quyết bài toán thực tế.
- **Lộ trình 5 bước định hình sự nghiệp**: Tư duy không đặt nặng chức danh (Title) mà tập trung nâng cấp năng lực qua từng nấc thang: `Follower` (Người thực thi checklist) $\rightarrow$ `Learner` (Người học chủ động) $\rightarrow$ `Problem Solver` (Người cam kết giải quyết bài toán) $\rightarrow$ `System Thinker` (Người tư duy hệ thống toàn cảnh) $\rightarrow$ `Super Star` (Người dẫn dắt và xây dựng tầm nhìn).
- **Văn hóa không đổ lỗi (No-Blame Post-Mortem)**: Khắc họa sâu nét văn hóa công nghệ tại các tập đoàn đa quốc gia. Khi xảy ra sự cố sập hệ thống nghiêm trọng, các kỹ sư cùng ngồi lại rà soát mã nguồn, tìm ra kẽ hở kiến trúc hạ tầng để sửa đổi tận gốc thay vì chỉ trích hay quy trách nhiệm cho cá nhân.

#### 3.Bức tranh toàn cảnh về DevOps Engineer trong thực tế
- **Thị trường và thu nhập tại Việt Nam (2025 - 2026)**: Theo các báo cáo khảo sát từ ITviec và JT1 Salary Guide, vị trí DevOps Engineer và Cloud Engineer luôn nằm trong nhóm có chỉ số nhu cầu tuyển dụng và mức lương cao nhất thị trường. Thu nhập dao động từ **16 - 28 triệu VND/tháng** đối với mức Junior và có thể đạt tới **65 - 100 triệu VND/tháng** đối với cấp bậc Lead/Expert.
- **Định kiến vs Thực tế**: DevOps không chỉ đơn thuần là người viết vài đường ống CI/CD, cấu hình Docker/Kubernetes hay ngồi trực hệ thống lúc nửa đêm. Phạm vi công việc (Scope) phụ thuộc rất lớn vào quy mô công ty, cấu trúc phòng ban và độ trưởng thành của hạ tầng doanh nghiệp.
- **"Fundamentals Stay"**: Công cụ công nghệ (Tools) có thể thay đổi liên tục theo thời gian, nhưng các kiến thức nền tảng sẽ luôn ở lại. DevOps giỏi cần làm chủ các kỹ năng gốc: Hệ điều hành Linux, Kiến thức mạng (Networking basics), Ngôn ngữ lập trình (Python/Golang), Tư duy hệ thống (System Thinking) và năng lực đặt câu hỏi *"Tại sao"* trước khi tìm hiểu *"Làm thế nào"*.

### Những Gì Học Được

#### Tư Duy Thiết Kế

- **Tư duy tiền tính toán (Pre-computation)**: Học được cách chuyển đổi các tác vụ nặng, dễ gây nghẽn mạch thời gian thực thành các tác vụ chạy ngầm bất đồng bộ, chuẩn bị sẵn tài nguyên để phục vụ người dùng cuối với độ trễ thấp nhất.
- **Tư duy No-Blame**: Hiểu rằng hệ thống lỗi là do lỗ hổng trong quy trình hoặc kiến trúc, việc xây dựng văn hóa thảo luận cởi mở sau sự cố là chìa khóa để nâng cấp độ ổn định của toàn bộ tổ chức.

#### Kiến Trúc Kỹ Thuật

- Nắm vững mô hình triển khai **KGS (Key Generation Service)** kết hợp giữa Redis (in-memory) và DynamoDB để xử lý bài toán chịu tải cao, chống trùng lặp dữ liệu.
- Hiểu cách thức phân phối các chỉ số đo lường hiệu suất vận hành (Fulfillment, Last Mile Cost, Fill Rate) thông qua các Dashboard phân tích dữ liệu trực quan.
- Phân biệt rõ ràng các lớp phòng ngự bảo mật tại vùng biên đám mây thông qua sự kết hợp của AWS WAF, CloudFront và AWS KMS.

#### Chiến Lược Hiện Đại Hóa

- Thấu hiểu chuỗi dịch chuyển từ "Làm được" sang "Làm đúng chuẩn" toàn cầu. Đối với chuỗi cung ứng vật lý là tuân thủ GMP, GSP, GDP; còn đối với chuỗi cung ứng số và hạ tầng đám mây là bắt buộc phải đạt các chứng chỉ khắt khe như ISO 27001, SOC 2, và GDPR.

### Ứng Dụng Vào Công Việc

- **Ứng dụng Cache-aside trong đồ án**: Thiết lập thêm một lớp Redis Cache đứng trước Database chính của các ứng dụng Backend tự làm để tối ưu tốc độ phản hồi API.
- **Xây dựng tư duy System Thinker**: Khi viết code hoặc cấu hình hệ thống, tập thói quen đánh giá xem thay đổi này có ảnh hưởng chéo đến các module khác hoặc chi phí tài nguyên đám mây (FinOps) hay không.
- **Tập trung vào Fundamentals**: Dành thêm thời gian học sâu về nhân Linux, các câu lệnh quản trị mạng nâng cao và Git luồng phối hợp (Git branching strategies) thay vì chỉ học vẹt các cú pháp của các công cụ automation.
- **Thực hành triết lý "Đúng việc"**: Rèn luyện nội tâm tự quản trị vững vàng, làm nghề với tinh thần phụng sự giải quyết các bài toán thực tế của xã hội, hướng tới việc gánh vác các hệ thống số lớn sau khi ra trường.

### Trải nghiệm trong event

- Tham gia sự kiện **“FCAJ Community Day - June 2026 Edition”** vào ngày 13/06/2026 mang lại chuỗi trải nghiệm công nghệ vô cùng bùng nổ và thực tế.

#### Học hỏi từ các diễn giả có chuyên môn cao

- Các bài chia sẻ cực kỳ lôi cuốn từ những đàn anh đi trước đầy kinh nghiệm thực chiến. Lời khuyên về việc *"Dùng AI để nâng tầm kỹ năng chứ không phải để tắt đi bộ não của mình"* của các diễn giả DevOps khiến tôi thức tỉnh về phương pháp tự học trong kỷ nguyên công nghệ mới.

#### Trải nghiệm kỹ thuật thực tế

- Được phân tích chi tiết sơ đồ kiến trúc hạ tầng AWS chịu tải quy mô lớn. Việc bóc tách từng luồng request từ Route 53, đi qua ALB rồi chia tải xuống các dịch vụ Fargate và Redis giúp tôi hiểu sâu sắc thế nào là một hệ thống có tính sẵn sàng cao (High Availability).

#### Ứng dụng công cụ hiện đại

- Được tiếp cận các mẫu Dashboard vận hành thực tế tại doanh nghiệp, từ đó hiểu được cách biến những dòng dữ liệu thô, khô khan thành những câu chuyện có ý nghĩa, hỗ trợ ban giám đốc đưa ra các quyết định kinh doanh chính xác.

#### Kết nối và trao đổi

- Sự kiện diễn ra vô cùng ấm cúng với không gian thảo luận trực tiếp và cả các phiên kết nối trực tuyến qua Google Meet với hơn 30 thành viên đồng hành. Việc được trao đổi và lắng nghe những trăn trở, định hướng phát triển từ các AWS Community Builders đã mở rộng đáng kể mạng lưới kết nối chuyên môn của tôi.

#### Bài học rút ra

- Mọi công cụ, công nghệ thời thượng đều sẽ lỗi thời theo năm tháng, duy chỉ có kiến thức nền tảng và tư duy hệ thống vững vàng là chìa khóa giúp người kỹ sư thích nghi với mọi biến động thị trường.
- Hãy luôn chủ động học hỏi bằng cách thực hành (Hands-on Labs), đóng góp ngược lại cho cộng đồng (Share Back) để không ngừng nâng cao giá trị bản thân trên bản đồ công nghệ.

#### Một số hình ảnh khi tham gia sự kiện

![Ảnh minh chứng: tham gia event](</AWS-HUTECHintership-report/images/4-EventParticipated/4.4-Event4/event4(0).jpg>)

> Tóm lại, sự kiện đã mang đến một lăng kính công nghệ vô cùng sắc bén và toàn diện. Đây không chỉ là những kiến thức bổ trợ cho cuốn báo cáo thực tập, mà còn là kim chỉ nam giúp tôi định hình rõ ràng con đường trở thành một kỹ sư giải pháp đám mây tử tế và chuẩn mực quốc tế.
