---
title: "Kiểm thử end-to-end và xử lý sự cố WebSocket"
date: 2026-07-09
weight: 3
chapter: false
pre: " <b> 5.5.3. </b> "
---

#### 1. Sự cố: route WebSocket mới không được API Gateway nhận diện

**Hiện tượng:** sau khi thêm route `whoami` (Lambda + Route + Integration) vào `template.yaml` và `sam deploy` báo thành công, app Flutter gửi request `{"action": "whoami"}` nhưng luôn nhận lại:

```json
{"message": "Forbidden", "connectionId": "...", "requestId": "..."}
```

Kiểm tra CloudWatch Logs của Lambda `inboxiq-ws-whoami` thấy log group **chưa từng tồn tại** — bằng chứng chắc chắn request chưa từng chạm tới Lambda, lỗi nằm ở tầng routing của API Gateway, không phải ở code.

![Log group does not exist](/AWS-HUTECHintership-report/images/5-Workshop/5.5-Flutter-app/whoami-log-group-not-exist.jpg)

**Nguyên nhân:** kiểm tra trong Console, resource `WsWhoamiFunction`/`WhoamiRoute`/`WhoamiIntegration` đều có trạng thái `CREATE_COMPLETE` trong CloudFormation stack — route thật sự đã được tạo. Nhưng vào **API Gateway → WebSocket API → Stages → prod → Deployment history**, stage vẫn đang active ở deployment **cũ hơn ngày tạo route** — nghĩa là CloudFormation tạo resource `AWS::ApiGatewayV2::Route` thành công, nhưng **không tự tạo deployment mới cho stage `prod`**, dù đã khai `DependsOn` giữa `AWS::ApiGatewayV2::Deployment` và route đó. `DependsOn` chỉ đảm bảo thứ tự tạo resource, không ép CloudFormation coi đó là "thay đổi" cần re-deploy.

![Deployment history chỉ có 1 bản deploy cũ](/AWS-HUTECHintership-report/images/5-Workshop/5.5-Flutter-app/deployment-history-stale.jpg)

**Cách xử lý:** vào API Gateway Console → chọn API WebSocket → **Deploy API** → chọn stage `prod` → xác nhận. Sau khi có deployment mới, route hoạt động ngay mà không cần sửa code hay deploy lại CloudFormation.

![Deployment mới sau khi Deploy API thủ công](/AWS-HUTECHintership-report/images/5-Workshop/5.5-Flutter-app/deployment-history-fixed.jpg)

**Bài học:** với `AWS::ApiGatewayV2` quản lý bằng SAM/CloudFormation, thêm route mới không tự động đồng nghĩa route đó "sống" trên stage đang chạy. Cần xác minh qua Deployment history sau mỗi lần thay đổi route, hoặc cân nhắc thiết kế template để `Deployment` logical ID đổi theo nội dung route (ví dụ gắn hash) nhằm buộc CloudFormation tạo deployment mới mỗi lần.

#### 2. Sự cố: WebSocket "coi như đã kết nối" nhưng thực chất còn đang handshake

**Hiện tượng:** ngay sau khi sửa xong route `whoami`, app vẫn báo "WebSocket chưa sẵn sàng" ở một số lần chạy, không ổn định.

**Nguyên nhân:** code gốc dùng:
```dart
_channel = WebSocketChannel.connect(uri);
await Future.delayed(const Duration(milliseconds: 300));
```
`WebSocketChannel.connect()` trả về ngay lập tức, không chờ handshake thật sự hoàn tất (bao gồm cả thời gian Lambda Authorizer xác thực JWT — có thể chậm hơn 300ms nếu bị cold start). Gửi message trước khi handshake xong khiến message bị rớt âm thầm, không có lỗi nào được ném ra.

**Cách xử lý:** thay khoảng chờ cố định bằng tín hiệu thật của thư viện:
```dart
_channel = WebSocketChannel.connect(uri);
try {
  await _channel!.ready;
} catch (e) {
  _channel = null;
  rethrow;
}
```
`channel.ready` là một `Future` chỉ hoàn thành đúng lúc handshake xong, hoặc ném lỗi nếu thất bại — loại bỏ hoàn toàn việc phỏng đoán thời gian chờ.

#### 3. Sự cố: xử lý email tuần tự dễ vỡ khi số lượng lớn

**Hiện tượng:** thời gian phản hồi tăng tuyến tính theo số email; một email lỗi (timeout OpenAI, rate limit) làm mất kết quả của toàn bộ các email khác trong cùng batch.

**Nguyên nhân:** code gốc gọi GPT-4o-mini tuần tự trong vòng lặp `for`, mỗi lệnh gọi có timeout tối đa 15 giây — với nhiều email chưa đọc, tổng thời gian dễ tiến sát ngưỡng timeout của Lambda Worker (300 giây) và tăng nguy cơ kết nối WebSocket phía client bị đóng trước khi kịp nhận kết quả.

**Cách xử lý:** đổi sang xử lý song song, chịu lỗi từng phần tử:
```js
const summaries = (await Promise.allSettled(
  emails.map(async (email) => {
    const summary = await summarizeWithOpenAI(email, apiKey);
    await saveSummary(userId, email.id, summary);
    return { emailId: email.id, ...summary };
  })
)).filter(r => r.status === 'fulfilled').map(r => r.value);
```
`Promise.allSettled` (thay vì `Promise.all`) đảm bảo một phần tử reject không kéo sập toàn bộ batch — email lỗi bị bỏ qua, các email còn lại vẫn trả về kết quả bình thường. Giữ nguyên trần `maxResults = 10` cho mỗi lần gọi `fetchUnreadEmails` để tránh vỡ timeout Lambda hoặc chi phí OpenAI tăng đột biến nếu hộp thư có hàng chục email chưa đọc.

#### 4. Sự cố: `connectionId` cache cứng ở tầng UI, gây lỗi `410 Gone` âm thầm

Đây là sự cố khó phát hiện nhất trong toàn bộ mục Flutter, vì không có bất kỳ thông báo lỗi nào hiển thị cho người dùng — chỉ thấy nút Check Gmail xoay mãi.

**Hiện tượng:** log Lambda Worker báo xử lý xong (fetch email, gọi OpenAI thành công, có tính phí token trên OpenAI dashboard) nhưng bước push kết quả cuối cùng thất bại:

```
WARN WebSocket push failed for gTg3TG75DQAYKABzSA==: GoneException UnknownError
{"httpStatusCode":410, "requestId":"...", "attempts":1, "totalRetryDelay":0}
```

![Log GoneException 410 khi push WebSocket](/AWS-HUTECHintership-report/images/5-Workshop/5.5-Flutter-app/worker-gone-exception.jpg)

**Nguyên nhân:** đối chiếu timestamp giữa 2 log:

| Thời điểm | Sự kiện |
|---|---|
| T | Lambda `ws-disconnect` ghi nhận connection đã đóng |
| T + 18s | Người dùng bấm "Check Gmail" |
| T + 27s | Lambda Worker cố push tới connectionId đó → `410 Gone` |

Kết nối đã chết **trước khi** người dùng bấm nút, nhưng app vẫn gửi đi `connectionId` cũ. Nguyên nhân gốc nằm ở tầng UI: màn hình Home lưu `connectionId` vào `State` chỉ một lần lúc `initState()`. Khi WebSocket rớt (đổi mạng, app tạm rời foreground, hay API Gateway tự đóng connection idle), service tầng dưới tự dọn giá trị `connectionId` nội bộ về `null`, nhưng biến `State` phía màn hình **không được đồng bộ theo** — dẫn tới việc dùng một giá trị đã lỗi thời để gọi API.


**Cách xử lý:** đọc `connectionId` trực tiếp từ service (nguồn dữ liệu sống) ngay tại thời điểm bấm nút, và tự động kết nối lại nếu phát hiện giá trị `null`, thay vì tin vào giá trị đã cache trong `State`:

```dart
Future<void> _checkGmail() async {
  var connId = _ws.connectionId;
  if (connId == null) {
    setState(() => _connectingWs = true);
    try {
      await _ws.connect();
      connId = await _ws.requestConnectionId();
      if (mounted) setState(() => _connectionId = connId);
    } finally {
      if (mounted) setState(() => _connectingWs = false);
    }
  }
  if (connId == null) {
    ScaffoldMessenger.of(context).showSnackBar(
      const SnackBar(content: Text('WebSocket chưa sẵn sàng')));
    return;
  }
  // tiếp tục gọi checkGmail với connId
}
```

**Bài học:** với bất kỳ kết nối long-lived nào (WebSocket, gRPC stream...), trạng thái hiển thị trên UI phải luôn được xác minh lại tại nguồn ngay trước khi dùng, không nên tin vào giá trị đã cache — vì kết nối có thể chết bất cứ lúc nào mà không có sự kiện nào báo ngược lại cho tầng gọi nó.

#### 5. Các lỗi khác đã gặp và cách xử lý

| Lỗi | Nguyên nhân | Cách xử lý |
|---|---|---|
| `sam deploy` báo "No changes to deploy" dù đã sửa `template.yaml` | Chạy `sam deploy` mà chưa `sam build` lại — SAM đóng gói bản build cũ, hash trùng bản đã deploy | Luôn chạy `sam build` trước `sam deploy` sau khi sửa template hoặc code |
| Bảng Lambda Console tưởng như thiếu function mới tạo | Danh sách bị giới hạn hiển thị/cần refresh, không phải function không tồn tại | Xác minh chéo qua CloudFormation → Resources thay vì chỉ tin vào 1 lần xem Lambda Console |
| App báo "WebSocket chưa sẵn sàng" dù đã kết nối trước đó | Phiên kết nối cũ đã bị đóng (xem mục 4) | Đọc `connectionId` tươi từ service, tự reconnect khi cần (xem mục 4) |

#### 6. Kết quả

Sau mục này, toàn bộ pipeline InboxIQ chạy hoàn chỉnh trên thiết bị thật, từ lúc bấm nút trên app tới lúc thấy kết quả tóm tắt hiển thị real-time qua WebSocket, không cần polling:

```
Flutter App → Cognito Auth → REST API → SQS → Worker
  → Gmail API + GPT-4o-mini (song song, chịu lỗi từng phần tử)
  → DynamoDB
  → WebSocket push → Flutter App hiển thị SummaryCard
```

![Danh sách email đã tóm tắt hiển thị trên app](/AWS-HUTECHintership-report/images/5-Workshop/5.5-Flutter-app/summaries-displayed.jpg)

#### 7. Hạng mục còn lại, ghi nhận là công việc bảo trì trong tương lai

- Bổ sung `WidgetsBindingObserver` để tự động nối lại WebSocket khi app quay lại từ background — hiện tại cơ chế tự phục hồi chỉ kích hoạt khi người dùng chủ động bấm lại nút Check Gmail
- Rotate Google OAuth Client Secret (đã từng bị lộ một lần trong ảnh chụp trong quá trình trao đổi lấy hỗ trợ)
- Xây dựng màn hình Sign Up để người dùng mới tự đăng ký tài khoản Cognito, thay vì tạo thủ công qua Console — cần thiết nếu mở rộng ra nhiều người dùng thật
- Nâng cấp runtime Lambda từ Node.js 20.x lên phiên bản còn được hỗ trợ dài hạn, trước các mốc ảnh hưởng thực tế (02/2027, 03/2027)
