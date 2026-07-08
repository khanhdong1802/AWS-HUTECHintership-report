---
title: "Blog 3"
date: 2026-07-07
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

# Hiện đại hóa ứng dụng và cơ sở dữ liệu với GenAI trên AWS

Trong quá trình phát triển phần mềm, nhiều doanh nghiệp vẫn đang vận hành các hệ thống cũ theo mô hình monolithic. Ban đầu, kiến trúc này có thể dễ triển khai và quản lý. Tuy nhiên, khi hệ thống ngày càng lớn, số lượng người dùng tăng lên và yêu cầu kinh doanh thay đổi liên tục, các ứng dụng monolithic thường bộc lộ những hạn chế lớn: thời gian phát hành (release) chậm, khó mở rộng độc lập, chi phí vận hành cao và rủi ro ảnh hưởng dây chuyền. Hiện đại hóa ứng dụng không chỉ đơn thuần là thay đổi công nghệ, mà còn là sự lột xác trong cách tư duy thiết kế hệ thống.

Bài đăng trên blog này (được tổng hợp từ nhóm Otokoshi) sẽ tập trung vào giải pháp GenAI-powered App & Database Modernization. Thay vì chỉ “nâng cấp code”, quá trình này là sự kết hợp nhuần nhuyễn giữa Domain-Driven Design (DDD), microservices, event-driven architecture, serverless computing và các công cụ AI (như Amazon Q Developer, AWS Transform) nhằm hỗ trợ phân tích, chuyển đổi và tối ưu hóa hệ thống từ gốc rễ.

---

## Hướng dẫn kiến trúc

Thay đổi cốt lõi trong tiến trình hiện đại hóa là việc dịch chuyển khỏi một khối monolithic khổng lồ sang một hệ sinh thái các dịch vụ nhỏ (microservices), được kết nối lỏng lẻo thông qua các luồng sự kiện (event-driven). Việc không cố gắng viết lại toàn bộ hệ thống từ đầu giúp giảm thiểu rủi ro kinh doanh đáng kể. Bằng cách định hình các ranh giới nghiệp vụ, chúng ta có thể bóc tách từng phần, chuyển dịch sang các mô hình serverless một cách an toàn.
**Kiến trúc giải pháp hiện đại hóa có thể được hình dung như sau:**
**Kiến trúc giải pháp hiện đại hóa có thể được hình dung như sau:**

![Hình 1. Sự dịch chuyển kiến trúc từ Monolithic sang Microservices kết hợp Event-driven và Serverless](/images/Blog3/blog3(0).jpg)

> *Hình 1. Sự dịch chuyển kiến trúc từ Monolithic sang Microservices kết hợp Event-driven và Serverless.*

---

Khi thiết lập ranh giới cho các thành phần trong hệ thống mới, hệ thống sẽ mang các đặc điểm chung:

* Bắt đầu từ business domain (nghiệp vụ kinh doanh) trước khi bàn về công nghệ.
* Mỗi chức năng quan trọng trở thành một service độc lập, tự chủ.
* Giao tiếp linh hoạt, bất đồng bộ qua các event thay vì gọi API trực tiếp.
* Chạy mã (code) mà không cần quản lý máy chủ vật lý hay máy ảo bên dưới.

Khi xác định chiến lược bóc tách monolithic, cần cân nhắc:

* **Nghiệp vụ**: Nhận diện các domain chính yếu (quản lý đơn hàng, thanh toán, khách hàng, kho hàng).
* **Kiến trúc**: Sử dụng Bounded Context để chia nhỏ, quyết định phần nào giữ lại, phần nào biến thành microservice.
* **Lộ trình**: Nâng cấp từng phần (modernize từng phần) thay vì thay đổi toàn diện trong một lần.

---

## Lựa chọn công nghệ và mô hình tính toán

| Khía cạnh hệ thống / Mức độ kiểm soát | Các dịch vụ AWS phù hợp / Mô hình tính toán |
| --- | --- |
| Cần kiểm soát chặt chẽ hệ điều hành, runtime và cấu hình | Amazon Elastic Compute Cloud (Amazon EC2) |
| Ứng dụng container, microservices và cần orchestration | Amazon Elastic Container Service (ECS), Amazon EKS |
| Chạy container nhưng không muốn quản lý máy chủ bên dưới | AWS Fargate |
| Event-driven, xử lý tác vụ ngắn, API nhỏ, automation | AWS Lambda |

---

## Domain-Driven Design (DDD)

![Quy trình hiện đại hóa ứng dụng 5 bước](/AWS-HUTECHintership-report/images/3-BlogsTranslated/Blog3/blog3(1).jpg)

Một sai lầm phổ biến là bắt đầu bằng câu hỏi: “Nên dùng Lambda, ECS hay Kubernetes?”. Thực tế, câu hỏi đầu tiên phải là: “Hệ thống đang phục vụ nghiệp vụ gì?”. DDD giúp nhóm kỹ thuật và nghiệp vụ sử dụng một ngôn ngữ chung. Thông qua các buổi *event storming*, bạn có thể xác định: - Những sự kiện nghiệp vụ quan trọng và các actor tham gia.

* Timeline các bước xử lý chính.
* Những bounded context (giới hạn ngữ cảnh) cần được bóc tách.

Nhược điểm/Thách thức: Đòi hỏi sự tham gia và trao đổi liên tục giữa *domain expert* (chuyên gia nghiệp vụ) và *software expert* (chuyên gia phần mềm) để khám phá sự phức tạp trước khi bắt tay vào code.

---

## Event-Driven Architecture

Giải quyết bài toán giao tiếp giữa các thành phần. Nếu các microservices gọi nhau bằng API đồng bộ, hệ thống sẽ nhanh chóng bị phụ thuộc chéo. Khi một dịch vụ sập, toàn bộ hệ thống có thể tê liệt.

* Một service (ví dụ: Order) chỉ cần phát ra sự kiện (OrderCreated).
* Các service khác (Payment, Inventory, Notification) độc lập lắng nghe sự kiện đó thông qua các router như **Amazon EventBridge** hoặc **Amazon SNS/SQS** để xử lý phần việc của mình.

> Hệ thống linh hoạt hơn, dễ dàng scale hơn và loại bỏ được sự phụ thuộc trực tiếp giữa các thành phần, giúp kiến trúc trở nên "loosely-coupled".

---

## Compute Evolution: Chuyển dịch sang Serverless

* Trút bỏ gánh nặng hạ tầng: Công việc duy trì, scaling, patching và quản lý capacity được giao lại cho AWS.
* Khai thác sức mạnh của **AWS Lambda**:
1. Rất phù hợp với các workload event-driven.
2. Tối ưu chi phí nhờ cơ chế tự động mở rộng và tính phí theo mức độ sử dụng thực tế (pay-as-you-go).
3. Lập trình viên chỉ cần tập trung vào viết logic nghiệp vụ.

---

## Vai trò của GenAI trong giải pháp

### 1. Amazon Q Developer và AWS Transform

Dù không thể thay thế hoàn toàn Kiến trúc sư phần mềm, GenAI đóng vai trò như một trợ lý siêu tốc giúp giải quyết các bài toán di chuyển dữ liệu và mã nguồn phức tạp.

![Sự hỗ trợ của GenAI thông qua Amazon Q Developer và AWS Transform](/AWS-HUTECHintership-report/images/3-BlogsTranslated/Blog3/blog3(2).jpg)

* **Amazon Q Developer**: Phân tích codebase cũ, gợi ý cách bóc tách module, tạo tài liệu kỹ thuật, đề xuất test case và hỗ trợ refactor (ví dụ: nâng cấp phiên bản Java, giải thích dependency).
* **AWS Transform**: Dịch vụ agentic AI hỗ trợ hiện đại hóa ở quy mô lớn đối với các workload nặng như Windows, mainframe hay VMware.

Ví dụ về một bản kế hoạch chuyển đổi (diff) do AI đề xuất để thay thế lời gọi API đồng bộ sang mô hình phát sự kiện:

```diff
- // Legacy Monolithic Synchronous Call
- paymentService.processPayment(order.getId(), order.getTotal());
- inventoryService.deductItems(order.getItems());

+ // Modern Event-Driven Architecture with EventBridge
+ PutEventsRequestEntry eventEntry = PutEventsRequestEntry.builder()
+    .source("com.ecommerce.orders")
+    .detailType("OrderCreated")
+    .detail(orderJson)
+    .eventBusName("EcommerceEventBus")
+    .build();
+ eventBridgeClient.putEvents(PutEventsRequest.builder().entries(eventEntry).build());

```