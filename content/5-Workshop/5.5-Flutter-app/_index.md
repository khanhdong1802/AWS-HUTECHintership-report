---
title : "Flutter App"
date : 2026-07-09
weight : 5
chapter : false
pre : " <b> 5.5. </b> "
---

#### Vì sao cần một mục riêng cho Flutter App

Ba mục trước (Backend, OAuth Gmail) xây xong toàn bộ phần server, nhưng người dùng cuối không gọi trực tiếp API — họ cần một ứng dụng di động để đăng nhập, kết nối Gmail, và xem kết quả tóm tắt. Mục này ghi lại phần client Flutter và, quan trọng hơn, luồng **real-time push qua WebSocket** — phần khó nhất của cả dự án vì nó không lộ ra ngay trên diagram kiến trúc, nhưng lại là nơi phát sinh nhiều lỗi khó thấy nhất.

Luồng hoàn chỉnh từ lúc bấm nút đến lúc thấy kết quả:

```
User bấm "Check Gmail" trên app
  → App gửi connectionId (lấy qua route whoami) lên REST /check-gmail
  → Lambda producer đẩy job vào SQS
  → Lambda Worker nhận job từ SQS
  → Worker gọi Gmail API lấy email chưa đọc, gọi GPT-4o-mini tóm tắt (song song)
  → Worker lưu kết quả vào DynamoDB
  → Worker push kết quả ngược lại app qua API Gateway WebSocket (PostToConnection)
  → App nhận message qua WebSocket, hiển thị danh sách SummaryCard

```

Điểm mấu chốt của thiết kế: kết quả **không** được lấy bằng cách polling REST định kỳ — không có endpoint `GET /summaries` nào cả. Toàn bộ kết quả được đẩy một chiều từ server xuống client qua WebSocket ngay khi xử lý xong, giúp giảm số lượt gọi API và cho cảm giác "real-time" đúng nghĩa. Cái giá phải trả là độ phức tạp: client phải tự quản lý vòng đời kết nối WebSocket, và phải tự biết khi nào kết nối đã "chết" để nối lại — vấn đề chiếm phần lớn thời gian debug của mục này.

#### Kiến trúc client (instance-based)

Toàn bộ service của app (`AuthService`, `RestApiService`, `WebSocketService`, `GmailOAuthService`) được thiết kế theo kiểu **instance-based**, khởi tạo một lần ở tầng gốc của app và truyền xuống qua constructor, thay vì dùng static class hay singleton toàn cục. Cách này giúp việc test và quản lý vòng đời (dispose khi thoát màn hình) rõ ràng hơn, dù tốn thêm một chút boilerplate truyền tham số.

Cấu trúc thư mục chính:

```
lib/
├── config/app_config.dart          # Endpoint URLs, Cognito IDs
├── services/
│   ├── auth_service.dart           # Cognito login/signup
│   ├── rest_api_service.dart       # HTTP calls
│   ├── websocket_service.dart      # WebSocket connect + listen
│   └── gmail_oauth_service.dart    # OAuth init + deep link handler
├── models/email_summary.dart
├── screens/{login,home}_screen.dart
└── widgets/summary_card.dart

```

#### Vấn đề gặp phải và cách xử lý

**1. `$connect` không thể trả `connectionId` an toàn ngay lập tức.** Gửi message ngay trong Lambda `$connect` có rủi ro `GoneException` vì kết nối chưa "sống" hoàn toàn tại thời điểm đó. Giải pháp: thêm route WebSocket riêng tên `whoami` — client chủ động gửi request sau khi kết nối, Lambda `ws-whoami` dùng `ApiGatewayManagementApiClient` trả lại `connectionId` một cách an toàn.

**2. API Gateway không tự động re-deploy stage khi thêm route mới qua CloudFormation.** Route `whoami` được tạo thành công trong CloudFormation (`AWS::ApiGatewayV2::Route`), nhưng stage `prod` vẫn trỏ vào deployment cũ từ trước khi route đó tồn tại — dù đã khai `DependsOn` giữa `Deployment` và `Route`. Hệ quả: mọi request `whoami` bị API Gateway trả lỗi `Forbidden` ngay tại tầng routing, chưa từng chạm tới Lambda. Cách phát hiện: kiểm tra CloudWatch Logs của Lambda đích — log group còn chưa từng được tạo ra là dấu hiệu chắc chắn request chưa tới nơi. Cách sửa: chủ động **Deploy API** vào đúng stage sau mỗi lần thêm/sửa route, thay vì chỉ tin vào `sam deploy`.

**3. `WebSocketChannel.connect()` trong Flutter không chờ handshake hoàn tất.** Hàm trả về ngay lập tức dù kết nối thật sự còn đang xử lý phía sau (bao gồm cả Lambda Authorizer xác thực JWT). Dùng `Future.delayed` với thời gian cố định là một phỏng đoán không đáng tin — thay bằng `await channel.ready`, một Future chỉ hoàn thành đúng lúc handshake xong hoặc ném lỗi nếu thất bại.

**4. Xử lý email tuần tự khiến thời gian phản hồi tăng tuyến tính và dễ mất cả batch vì một email lỗi.** Ban đầu Worker gọi GPT-4o-mini lần lượt cho từng email trong vòng lặp `for`. Với 10 email chưa đọc, mỗi lệnh gọi OpenAI có thể mất tới 15 giây (timeout tối đa) — nếu chạy tuần tự, tổng thời gian dễ vượt ngưỡng an toàn và tăng nguy cơ WebSocket bị đóng trước khi kịp trả kết quả. Đổi sang `Promise.allSettled` để xử lý song song, đồng thời đảm bảo một email lỗi (timeout, rate limit OpenAI) không làm mất kết quả của các email khác trong cùng batch — thay bằng `Promise.all`, vốn sẽ reject toàn bộ nếu chỉ một phần tử thất bại.

**5. `connectionId` bị cache "cứng" ở tầng UI, không đồng bộ với trạng thái thật của kết nối.** Đây là nguyên nhân sâu xa nhất và khó phát hiện nhất trong toàn bộ quá trình debug: màn hình Home lưu `connectionId` vào state chỉ một lần lúc khởi tạo. Khi WebSocket rớt kết nối (do mất mạng, đổi mạng, hay hệ điều hành tạm ngưng kết nối nền), service tầng dưới tự dọn giá trị về `null`, nhưng state của màn hình không được cập nhật theo — dẫn đến việc gửi một `connectionId` đã chết lên server. Backend trả về lỗi `410 Gone` khi cố push kết quả, nhưng vì đây là lỗi xảy ra ở phía server sau khi REST call đã trả về thành công, người dùng không thấy bất kỳ thông báo lỗi nào — chỉ thấy màn hình xoay mãi không có kết quả.

Bằng chứng xác nhận nguyên nhân: log của Lambda `ws-disconnect` cho thấy kết nối đã đóng *trước* thời điểm người dùng bấm nút Check Gmail, không phải trong lúc xử lý. Cách sửa: đọc `connectionId` trực tiếp từ service (nguồn dữ liệu sống) ngay tại thời điểm bấm nút thay vì tin vào giá trị đã cache trong state, và tự động kết nối lại nếu phát hiện giá trị đó là `null`.

#### Nội dung

{{% children /%}}

#### Kết quả đạt được sau mục này

* Ứng dụng Flutter hoàn chỉnh chạy trên thiết bị Android thật (Samsung S23 Ultra), kiến trúc instance-based cho toàn bộ service tầng client
* Luồng đăng nhập Cognito, deep link OAuth Gmail, và kết nối WebSocket real-time đều chạy ổn định end-to-end
* Route `whoami` bổ sung cho WebSocket API để lấy `connectionId` an toàn, không phụ thuộc vào `$connect`
* Worker xử lý email song song, chịu lỗi từng phần tử (partial failure tolerant) thay vì mất cả batch vì một email lỗi
* Xác nhận và khắc phục một lớp lỗi tinh vi: trạng thái kết nối cache ở tầng UI không đồng bộ với trạng thái thật của kết nối mạng — một bài học chung áp dụng được cho bất kỳ ứng dụng real-time nào, không riêng gì WebSocket