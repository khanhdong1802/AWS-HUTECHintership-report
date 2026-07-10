---
title: "Worklog Tuần 6"
date: 2026-06-01
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

### Mục tiêu tuần 6:

- Tìm hiểu tổng quan về quản lý chi phí trên AWS bằng dịch vụ AWS Budgets.
- Hiểu vai trò của AWS Budgets trong việc theo dõi, kiểm soát và cảnh báo chi phí sử dụng dịch vụ AWS.
- Thực hành tạo Budget bằng template có sẵn để thiết lập ngân sách nhanh.
- Tạo Cost Budget để theo dõi chi phí sử dụng AWS theo tháng.
- Tạo Usage Budget để theo dõi mức sử dụng tài nguyên AWS, đặc biệt là các dịch vụ tính phí theo thời gian sử dụng.
- Tìm hiểu Reservation Budget để giám sát mức sử dụng Reserved Instances.
- Tìm hiểu Savings Plans Budget để giám sát mức sử dụng Savings Plans.
- Cấu hình alert threshold và email notification để nhận cảnh báo khi chi phí hoặc mức sử dụng vượt ngưỡng.
- Biết cách kiểm tra Budget history, Budget health và trạng thái cảnh báo.
- Thực hành xóa các Budget đã tạo sau khi hoàn thành lab để tránh nhận thông báo không cần thiết.

---

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Đọc tổng quan workshop Cost Management with AWS Budgets <br> - Tìm hiểu vai trò của AWS Budgets trong quản lý chi phí AWS <br> - Truy cập AWS Billing and Cost Management <br> - Làm quen với giao diện Budgets trong AWS Console | 01/06/2026 | 01/06/2026 | https://000007.awsstudygroup.com/1-create-budget/ |
| 3 | - Tạo Budget bằng template có sẵn <br> - Chọn template Monthly cost budget <br> - Nhập tên Budget, số tiền ngân sách hàng tháng và ngưỡng cảnh báo <br> - Kiểm tra Budget đã tạo thành công và xem Budget history | 02/06/2026 | 02/06/2026 | https://000007.awsstudygroup.com/1-create-budget/ |
| 4 | - Tạo Cost Budget bằng chế độ Customize <br> - Chọn loại ngân sách Cost budget <br> - Cấu hình Period, Budget effective dates, Budgeting method và Budgeted amount <br> - Cấu hình Budget scope áp dụng cho All AWS services và Unblended costs | 03/06/2026 | 03/06/2026 | https://000007.awsstudygroup.com/2-cost-budgets/ |
| 5 | - Cấu hình alert threshold cho Cost Budget <br> - Thêm email nhận cảnh báo khi chi phí vượt ngưỡng <br> - Review lại toàn bộ cấu hình Cost Budget <br> - Tạo Budget và xác nhận trạng thái hoạt động | 04/06/2026 | 04/06/2026 | https://000007.awsstudygroup.com/2-cost-budgets/ |
| 6 | - Tạo Usage Budget để theo dõi mức sử dụng tài nguyên <br> - Chọn Usage budget trong Budget types <br> - Cấu hình Usage type groups, ví dụ EC2: ELB - Running Hours <br> - Thiết lập giới hạn usage hours và alert threshold | 05/06/2026 | 05/06/2026 | https://000007.awsstudygroup.com/3-usage-budget/ |
| 7 | - Tìm hiểu Reservation Budget cho Reserved Instances <br> - Tìm hiểu Savings Plans Budget cho Savings Plans <br> - Thực hành hoặc xem hướng dẫn tạo RI Budget và Savings Plans Budget <br> - Cấu hình coverage threshold, utilization threshold và email alert | 06/06/2026 | 06/06/2026 | https://000007.awsstudygroup.com/4-reservation-budget/ <br> https://000007.awsstudygroup.com/5-saving-plans-budget/ |
| CN | - Kiểm tra danh sách Budget đã tạo <br> - Xem Budget health và Budget history <br> - Xóa các Budget đã tạo trong quá trình thực hành <br> - Ghi nhận kết quả, khó khăn, hướng khắc phục và bài học rút ra | 07/06/2026 | 07/06/2026 | https://000007.awsstudygroup.com/6-clean-up/ |

---

### Kết quả đạt được tuần 6:

#### Kiến thức

**AWS Budgets là gì?**

- Hiểu AWS Budgets là dịch vụ dùng để theo dõi chi phí, mức sử dụng tài nguyên và các cam kết tiết kiệm chi phí trên AWS.
- AWS Budgets giúp người dùng đặt ngân sách theo ngày, tháng, quý hoặc năm.
- Khi chi phí hoặc mức sử dụng vượt ngưỡng đã cấu hình, AWS Budgets có thể gửi cảnh báo qua email.
- AWS Budgets phù hợp với người mới học AWS vì giúp kiểm soát chi phí trong quá trình thực hành lab.
- Hiểu rằng AWS Budgets chỉ có chức năng giám sát và cảnh báo, không tự động dừng tài nguyên khi vượt ngân sách.

**AWS Billing and Cost Management**

- Biết truy cập AWS Billing and Cost Management từ AWS Management Console.
- Hiểu đây là khu vực quản lý thông tin chi phí, hóa đơn, ngân sách và cảnh báo chi phí.
- Biết sử dụng menu Budgets để tạo, xem, chỉnh sửa hoặc xóa các Budget.
- Biết kiểm tra trạng thái Budget sau khi tạo thành công.

**Create Budget using Template**

- Hiểu template giúp tạo Budget nhanh bằng các cấu hình có sẵn.
- Biết chọn Use a template để sử dụng mẫu đơn giản.
- Biết chọn Monthly cost budget để tạo ngân sách chi phí hàng tháng.
- Biết nhập Budget name, monthly budget amount và alert threshold.
- Biết kiểm tra Budget history sau khi tạo Budget thành công.
- Hiểu rằng template phù hợp khi cần thiết lập nhanh cảnh báo chi phí cơ bản.

**Cost Budget**

- Hiểu Cost Budget dùng để theo dõi chi phí sử dụng AWS.
- Biết tạo Cost Budget bằng chế độ Customize.
- Biết chọn Budget type là Cost budget.
- Biết cấu hình Period gồm Daily, Monthly, Quarterly hoặc Annually.
- Biết phân biệt Recurring Budget và Expiring Budget:
  - Recurring Budget: ngân sách lặp lại theo chu kỳ.
  - Expiring Budget: ngân sách chỉ áp dụng trong một khoảng thời gian nhất định.
- Biết chọn Budgeting method:
  - Fixed: ngân sách cố định cho mỗi kỳ.
  - Planned: ngân sách có thể thay đổi theo từng tháng.
- Biết chọn Budget scope là All AWS services để theo dõi toàn bộ dịch vụ AWS.
- Biết chọn Aggregate costs by là Unblended costs.
- Biết thêm alert threshold để nhận cảnh báo khi chi phí đạt một tỷ lệ nhất định của ngân sách.

**Usage Budget**

- Hiểu Usage Budget dùng để theo dõi mức sử dụng tài nguyên thay vì số tiền chi phí.
- Biết điểm khác nhau giữa Cost Budget và Usage Budget:
  - Cost Budget theo dõi chi phí.
  - Usage Budget theo dõi mức sử dụng tài nguyên.
- Biết chọn Budget type là Usage budget.
- Biết chọn Usage type groups để xác định loại tài nguyên cần theo dõi.
- Trong lab, Usage Budget có thể dùng để theo dõi EC2: ELB - Running Hours.
- Biết thiết lập giới hạn usage hours cho tài nguyên.
- Biết cấu hình email alert khi mức sử dụng vượt ngưỡng.
- Hiểu Usage Budget hữu ích với các dịch vụ tính phí theo giờ như EC2, Load Balancer hoặc các tài nguyên chạy liên tục.

**Reservation Budget**

- Hiểu Reservation Budget dùng để theo dõi Reserved Instances.
- Biết Reserved Instances thường yêu cầu cam kết hoặc thanh toán trước nên trong phạm vi lab chỉ nên xem hướng dẫn hoặc thực hành ở mức demo.
- Biết Reservation Budget giúp theo dõi coverage hoặc utilization của Reserved Instances.
- Biết cấu hình Coverage threshold để theo dõi mức bao phủ của Reserved Instances.
- Hiểu Reservation Budget phù hợp với môi trường doanh nghiệp đã sử dụng Reserved Instances để tối ưu chi phí dài hạn.

**Savings Plans Budget**

- Hiểu Savings Plans Budget dùng để theo dõi mức sử dụng Savings Plans.
- Biết Savings Plans là hình thức cam kết sử dụng tài nguyên tính toán trong một khoảng thời gian nhất định để đổi lấy mức giá thấp hơn so với On-Demand.
- Biết Savings Plans Budget giúp theo dõi utilization threshold.
- Biết cấu hình email alert để nhận thông báo khi mức sử dụng Savings Plans chưa đạt kỳ vọng.
- Hiểu Savings Plans Budget phù hợp khi hệ thống có nhu cầu sử dụng compute ổn định trong thời gian dài.

**Alert Threshold và Email Notification**

- Hiểu alert threshold là ngưỡng cảnh báo được đặt theo phần trăm hoặc giá trị cụ thể.
- Biết cấu hình nhiều mức cảnh báo, ví dụ 50%, 80% và 100%.
- Biết thêm email để nhận cảnh báo ngân sách.
- Hiểu rằng người nhận email nên là người có trách nhiệm theo dõi chi phí hoặc quản lý tài khoản AWS.
- Biết kiểm tra email xác nhận hoặc thông báo từ AWS Budgets sau khi cấu hình.

**Budget Health và Budget History**

- Hiểu Budget health cho biết trạng thái hiện tại của ngân sách so với mức đã đặt.
- Biết dùng Budget history để xem xu hướng chi phí hoặc mức sử dụng theo thời gian.
- Biết quan sát trạng thái ngân sách để đánh giá tài khoản AWS có đang sử dụng vượt mức dự kiến hay không.
- Hiểu rằng dữ liệu chi phí trên AWS có thể có độ trễ, nên cần chờ một khoảng thời gian để số liệu cập nhật đầy đủ.

**Cleanup AWS Budgets**

- Hiểu cleanup là bước cần thiết sau khi hoàn thành lab.
- Biết xóa các Budget đã tạo nếu chỉ dùng để thực hành.
- Biết rằng xóa AWS Budgets không xóa tài nguyên đang chạy trong AWS account.
- Hiểu AWS Budgets chỉ là công cụ theo dõi và cảnh báo, không phải tài nguyên xử lý ứng dụng.
- Biết kiểm tra lại danh sách Budget sau khi xóa để đảm bảo không còn cảnh báo không cần thiết.

---

#### Thực hành

**Module 1 — Create Budget**

- Truy cập AWS Management Console.
- Tìm kiếm và mở dịch vụ AWS Billing and Cost Management.
- Chọn mục Budgets ở menu bên trái.
- Nhấn Create a budget để bắt đầu tạo ngân sách.
- Chọn Use a template (simplified).
- Chọn template Monthly cost budget.
- Nhập tên Budget theo yêu cầu lab.
- Nhập số tiền ngân sách hàng tháng.
- Cấu hình alert threshold để nhận cảnh báo khi chi phí đạt ngưỡng.
- Nhấn Create budget để hoàn tất.
- Kiểm tra Budget đã được tạo thành công.
- Mở Budget history để xem lịch sử ngân sách.
- Kiểm tra các loại alert có sẵn trong template.
-  _Ảnh minh chứng: Giao diện AWS Billing and Cost Management._
-  _Ảnh minh chứng: Chọn Budgets và Create a budget._
-  _Ảnh minh chứng: Chọn Monthly cost budget template._
-  _Ảnh minh chứng: Budget được tạo thành công._

**Module 2 — Create Cost Budget**

- Truy cập AWS Billing and Cost Management.
- Chọn Budgets ở menu bên trái.
- Nhấn Create budget.
- Trong phần Budget setup, chọn Customize.
- Trong Budget types, chọn Cost budget.
- Nhấn Next để tiếp tục.
- Ở phần Set your budget, nhập Budget name là `Monthly`.
- Chọn Period phù hợp, ví dụ Monthly.
- Chọn Budget effective dates:
  - Recurring Budget nếu muốn ngân sách lặp lại.
  - Expiring Budget nếu chỉ muốn áp dụng một lần.
- Cấu hình Budgeting method:
  - Fixed nếu muốn ngân sách mỗi kỳ giống nhau.
  - Planned nếu muốn đặt ngân sách khác nhau theo từng tháng.
- Nhập Budgeted amount theo mức chi phí mong muốn.
- Trong Budget scope, chọn All AWS services.
- Trong Aggregate costs by, chọn Unblended costs.
- Nhấn Next để chuyển sang bước cấu hình cảnh báo.
- Chọn Add an alert threshold.
- Nhập tỷ lệ cảnh báo, ví dụ 80% hoặc 100%.
- Nhập email nhận cảnh báo.
- Kiểm tra lại toàn bộ cấu hình.
- Nhấn Create budget để hoàn tất.
- Xác nhận Budget đã xuất hiện trong danh sách Budgets.
-  _Ảnh minh chứng: Chọn Customize và Cost budget._
-  _Ảnh minh chứng: Cấu hình Period, Budget amount và Budget scope._
-  _Ảnh minh chứng: Cấu hình alert threshold._
-  _Ảnh minh chứng: Cost Budget tạo thành công._

**Module 3 — Create Usage Budget**

- Truy cập lại mục Budgets trong AWS Billing and Cost Management.
- Nhấn Create budget.
- Chọn Customize.
- Trong Budget types, chọn Usage budget.
- Nhấn Next để tiếp tục.
- Nhập tên cho Usage Budget.
- Trong phần Budget against, chọn Usage type groups.
- Chọn loại usage cần theo dõi, ví dụ EC2: ELB - Running Hours.
- Cấu hình Set budget amount:
  - Chọn Period là Daily, Monthly, Quarterly hoặc Annually.
  - Chọn Budget renewal type là Recurring hoặc Expiring.
  - Chọn Budgeting method là Fixed hoặc Planned.
  - Nhập số giờ sử dụng tối đa muốn theo dõi.
- Giữ cấu hình mặc định ở Budget scope nếu phù hợp với lab.
- Nhấn Next để cấu hình cảnh báo.
- Chọn Add an alert threshold.
- Nhập phần trăm cảnh báo so với ngân sách sử dụng.
- Nhập email nhận thông báo.
- Review lại cấu hình.
- Nhấn Create budget để hoàn tất.
- Kiểm tra Budget health để xem mức sử dụng hiện tại so với giới hạn.
- Mở Budget history để theo dõi xu hướng sử dụng.
-  _Ảnh minh chứng: Chọn Usage budget._
-  _Ảnh minh chứng: Chọn Usage type groups._
-  _Ảnh minh chứng: Cấu hình usage hours và alert._
-  _Ảnh minh chứng: Usage Budget hiển thị trong danh sách._

**Module 4 — Create RI Budget**

- Truy cập AWS Billing and Cost Management.
- Chọn Budgets.
- Nhấn Create budget.
- Trong Budget setup, chọn Customize.
- Chọn Reservation budget.
- Nhấn Next.
- Nhập Budget name cho Reservation Budget.
- Cấu hình Coverage threshold để theo dõi mức bao phủ của Reserved Instances.
- Cấu hình Budget scope theo hướng dẫn lab.
- Ở phần Alert setting, nhập email nhận cảnh báo.
- Review lại thông tin.
- Nhấn Create budget.
- Kiểm tra Reservation Budget đã được tạo.
- Ghi nhận rằng trong phạm vi lab không bắt buộc phải mua Reserved Instances vì có thể phát sinh chi phí hoặc yêu cầu cam kết thanh toán.
-  _Ảnh minh chứng: Chọn Reservation budget._
-  _Ảnh minh chứng: Cấu hình Coverage threshold._
-  _Ảnh minh chứng: Reservation Budget được tạo hoặc xem ở chế độ demo._

**Module 5 — Create Savings Plans Budget**

- Truy cập AWS Billing and Cost Management.
- Chọn Budgets.
- Nhấn Create budget.
- Chọn Customize.
- Trong Budget types, chọn Savings Plans budget.
- Nhấn Next.
- Nhập Budget name cho Savings Plans Budget.
- Cấu hình Utilization threshold để theo dõi mức sử dụng Savings Plans.
- Giữ Budget scope theo mặc định nếu phù hợp với lab.
- Ở phần Alert setting, nhập email nhận cảnh báo.
- Review lại toàn bộ cấu hình.
- Nhấn Create budget để hoàn tất.
- Kiểm tra Budget vừa tạo trong danh sách.
- Ghi nhận rằng Savings Plans thường liên quan đến cam kết sử dụng dài hạn nên trong môi trường học tập chỉ nên thao tác theo hướng dẫn hoặc xem demo.
-  _Ảnh minh chứng: Chọn Savings Plans budget._
-  _Ảnh minh chứng: Cấu hình Utilization threshold._
-  _Ảnh minh chứng: Savings Plans Budget được tạo hoặc xem ở chế độ demo._

**Module 6 — Clean Up Resources**

- Truy cập AWS Billing and Cost Management.
- Chọn Budgets ở menu bên trái.
- Kiểm tra danh sách các Budget đã tạo trong lab.
- Chọn Budget cần xóa.
- Chọn Action.
- Nhấn Delete.
- Ở hộp thoại xác nhận, nhấn Delete để hoàn tất.
- Lặp lại thao tác với các Budget còn lại đã tạo trong quá trình thực hành.
- Kiểm tra lại danh sách Budgets sau khi xóa.
- Ghi nhận rằng việc xóa Budget chỉ xóa cấu hình theo dõi và cảnh báo, không ảnh hưởng đến các tài nguyên AWS đang chạy.
-  _Ảnh minh chứng: Danh sách Budget trước khi xóa._
-  _Ảnh minh chứng: Thao tác Delete Budget._
-  _Ảnh minh chứng: Danh sách Budget sau khi cleanup._

---

### Đánh giá kết quả tuần 6:

- Hoàn thành việc tìm hiểu dịch vụ AWS Budgets trong quản lý chi phí AWS.
- Biết cách tạo ngân sách bằng template có sẵn để thiết lập cảnh báo nhanh.
- Tạo được Cost Budget để theo dõi chi phí sử dụng AWS theo tháng.
- Tạo được Usage Budget để theo dõi mức sử dụng tài nguyên AWS.
- Hiểu được sự khác nhau giữa Cost Budget và Usage Budget.
- Nắm được mục đích của Reservation Budget trong việc theo dõi Reserved Instances.
- Nắm được mục đích của Savings Plans Budget trong việc theo dõi Savings Plans.
- Biết cấu hình alert threshold và email notification cho Budget.
- Biết kiểm tra Budget health và Budget history để đánh giá tình trạng ngân sách.
- Biết cleanup các Budget đã tạo trong môi trường lab.
- Nhận thức rõ hơn về tầm quan trọng của kiểm soát chi phí khi thực hành trên AWS.

---

### Khó khăn gặp phải:

- Giao diện Billing and Cost Management có nhiều mục nên ban đầu dễ nhầm giữa Bills, Cost Explorer và Budgets.
- Cần phân biệt rõ Cost Budget và Usage Budget vì hai loại Budget này theo dõi hai nhóm thông tin khác nhau.
- Khi cấu hình Usage Budget, cần chọn đúng Usage type group để theo dõi đúng loại tài nguyên.
- Một số tài khoản AWS mới có thể chưa hiển thị đầy đủ các loại Budget ngoài Cost Budget.
- Dữ liệu chi phí và usage trên AWS có thể cập nhật chậm nên sau khi tạo Budget chưa thể thấy ngay số liệu đầy đủ.
- Phần RI Budget và Savings Plans Budget dễ gây nhầm lẫn vì liên quan đến các hình thức cam kết sử dụng dài hạn.
- Nếu cấu hình nhiều alert hoặc nhập sai email, người dùng có thể không nhận được cảnh báo như mong muốn.
- Cần chú ý rằng AWS Budgets chỉ cảnh báo, không tự động dừng dịch vụ khi vượt ngân sách.

---

### Hướng khắc phục:

- Đọc kỹ từng bước trong workshop trước khi thao tác trên AWS Console.
- Ghi chú lại mục đích của từng loại Budget:
  - Cost Budget dùng để theo dõi chi phí.
  - Usage Budget dùng để theo dõi mức sử dụng tài nguyên.
  - Reservation Budget dùng để theo dõi Reserved Instances.
  - Savings Plans Budget dùng để theo dõi Savings Plans.
- Khi cấu hình cảnh báo, nên nhập đúng email và kiểm tra hộp thư để xác nhận nhận thông báo.
- Nên đặt nhiều mức cảnh báo như 50%, 80% và 100% để chủ động kiểm soát chi phí.
- Không nên mua Reserved Instances hoặc Savings Plans trong tài khoản học tập nếu không có yêu cầu chính thức.
- Sau khi hoàn thành lab, cần xóa các Budget thực hành để tránh nhận thông báo không cần thiết.
- Kiểm tra lại Billing Dashboard sau mỗi buổi lab để phát hiện chi phí bất thường.
- Kết hợp AWS Budgets với thói quen cleanup tài nguyên để hạn chế phát sinh chi phí ngoài ý muốn.

---

### Định hướng tuần tiếp theo:

- Tiếp tục tìm hiểu sâu hơn về AWS Billing and Cost Management.
- Nghiên cứu thêm AWS Cost Explorer để phân tích chi phí theo dịch vụ, thời gian và tài khoản.
- Tìm hiểu cách sử dụng AWS Free Tier Dashboard để theo dõi giới hạn miễn phí.
- Tìm hiểu AWS Cost Anomaly Detection để phát hiện chi phí bất thường.
- Nghiên cứu thêm về AWS Organizations và cách quản lý chi phí cho nhiều tài khoản AWS.
- Thực hành xây dựng thói quen kiểm tra Billing Dashboard sau mỗi bài lab.
- Chuẩn bị ảnh minh chứng, ghi chú thao tác và nhận xét kết quả để hoàn thiện báo cáo tuần tiếp theo.
