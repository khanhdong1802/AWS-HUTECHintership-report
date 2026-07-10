---

title: "Worklog Tuần 12"
date: 2026-07-06
weight: 12
chapter: false
pre: " <b> 1.12. </b> "
-----------------------

### Mục tiêu tuần 12: Khi hai nửa hệ thống gặp nhau — debug tích hợp phía client và hoàn thiện giao diện

Ứng dụng client đã hoàn thành, backend cũng đã có đầy đủ các thành phần cần thiết. Tuần này là thời điểm ghép hai nửa hệ thống lại với nhau và kiểm thử end-to-end trên thiết bị Android thật.

Kịch bản lý tưởng “kết nối vào là chạy ngay” đã không xảy ra. Một chuỗi lỗi tích hợp xuất hiện tại tầng WebSocket, nơi trạng thái phía client và phía server phải đồng bộ chính xác theo thời gian thực.

Với vai trò phụ trách phần client, công việc của tôi trong tuần này là hỗ trợ truy vết lỗi tích hợp, sửa các vấn đề thuộc phía ứng dụng — trong đó có một lỗi thiết kế state do chính tôi triển khai từ tuần trước — và hoàn thiện giao diện cho phiên bản nộp cuối cùng.

* Kiểm thử end-to-end cùng thành viên backend và hỗ trợ truy vết lỗi từ phía client thông qua log Flutter và hành vi giao diện.
* Sửa lỗi kết nối WebSocket “tưởng đã sẵn sàng nhưng thực tế chưa sẵn sàng” bằng cách thay khoảng chờ cố định bằng `channel.ready`.
* Sửa lỗi nghiêm trọng phía client: `connectionId` bị lưu cố định trong state màn hình và không được đồng bộ khi kết nối WebSocket bị ngắt.
* Nâng cấp giao diện bằng Material 3, thiết kế lại `SummaryCard` và bổ sung trạng thái rỗng có ý nghĩa.
* Hoàn thiện bộ ảnh chụp màn hình của toàn bộ luồng end-to-end để phục vụ báo cáo nhóm.

---

### Các công việc cần triển khai trong tuần này

| Thứ      | Hạng mục công việc                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu tham khảo                             |
| -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | --------------- | ---------------------------------------------------- |
| Thứ 2    | - Kiểm thử end-to-end lần đầu cùng thành viên backend trên thiết bị Android thật. <br> - Hiện tượng phía client: khi bấm `Check Gmail`, ứng dụng thông báo “WebSocket chưa sẵn sàng”. Log `WS received` cho thấy server trả về `{"message": "Forbidden"}` đối với request `whoami`. <br> - Cung cấp toàn bộ log phía client cho thành viên backend để truy vết nguyên nhân.                                                                                                                                                                                                                                          | 06/07/2026   | 06/07/2026      | Tài liệu nội bộ dự án                                |
| Thứ 3    | - Trong thời gian thành viên backend xử lý lỗi phía server do route chưa được publish, rà soát lại toàn bộ `WebSocketService` phía client. <br> - Phát hiện điểm yếu trong cơ chế kết nối: `WebSocketChannel.connect()` trả về đối tượng channel ngay lập tức nhưng không đồng nghĩa quá trình handshake đã hoàn thành. <br> - Code hiện tại sử dụng `Future.delayed(const Duration(milliseconds: 300))`, không đảm bảo khi Lambda Authorizer bị cold start.                                                                                                                                                         | 07/07/2026   | 07/07/2026      | https://pub.dev/documentation/web_socket_channel/    |
| Thứ 4    | - Thay khoảng chờ cố định bằng `await channel.ready`. Future này chỉ hoàn thành khi quá trình WebSocket handshake thành công hoặc ném lỗi khi kết nối thất bại. <br> - Bổ sung `try/catch` để dọn trạng thái channel khi xảy ra lỗi. <br> - Sau khi backend publish route, kiểm thử lại và xác nhận request `whoami` hoạt động, ứng dụng nhận được `connectionId`.                                                                                                                                                                                                                                                   | 08/07/2026   | 08/07/2026      | https://pub.dev/documentation/web_socket_channel/    |
| Thứ 5    | - Phát hiện sự cố mới: request `Check Gmail` được gửi thành công nhưng kết quả không hiển thị trên ứng dụng và phía client không xuất hiện exception. <br> - Log backend ghi nhận quá trình push kết quả thất bại với mã `410 Gone`, cho thấy WebSocket connection đã không còn tồn tại. <br> - Phối hợp với thành viên backend đối chiếu timestamp và xác định kết nối đã bị ngắt trước khi người dùng bấm nút, nhưng ứng dụng vẫn gửi `connectionId` cũ.                                                                                                                                                           | 09/07/2026   | 09/07/2026      | Tài liệu nội bộ dự án                                |
| Thứ 6    | - Xác định nguyên nhân gốc phía client: màn hình Home lưu `connectionId` vào `State` một lần duy nhất trong `initState`. Khi WebSocket bị ngắt, service đã xóa giá trị nội bộ nhưng state của màn hình không được cập nhật theo. <br> - Sửa phương thức `_checkGmail()` để đọc trực tiếp `connectionId` hiện tại từ `WebSocketService` tại thời điểm người dùng bấm nút. <br> - Nếu giá trị là null, ứng dụng tự động reconnect, gửi lại `whoami` và lấy `connectionId` mới trước khi gọi API. <br> - Kiểm thử lại và xác nhận luồng end-to-end hoạt động hoàn chỉnh, các `SummaryCard` được hiển thị trên ứng dụng. | 10/07/2026   | 10/07/2026      | https://docs.flutter.dev/data-and-backend/state-mgmt |
| Thứ 7    | - Nâng cấp giao diện bằng Material 3 với `ColorScheme.fromSeed`. <br> - Thiết kế card bo góc, đổ bóng nhẹ và phân cấp rõ nút hành động chính, phụ. <br> - Bổ sung trạng thái rỗng gồm icon và thông điệp hướng dẫn thay vì chỉ hiển thị một dòng chữ. <br> - Thiết kế lại `SummaryCard` với chấm màu thể hiện mức độ ưu tiên, icon theo category và khối hành động đề xuất có nền màu nhạt.                                                                                                                                                                                                                          | 11/07/2026   | 11/07/2026      | https://m3.material.io                               |
| Chủ nhật | - Chụp bộ ảnh màn hình của toàn bộ luồng end-to-end: Login → Home → Google consent → Gmail đã kết nối → đang kiểm tra Gmail → danh sách kết quả. <br> - Viết báo cáo tổng kết tuần. <br> - Bàn giao ảnh và phần mô tả luồng xử lý cho thành viên phụ trách tài liệu của nhóm.                                                                                                                                                                                                                                                                                                                                        | 12/07/2026   | 12/07/2026      | Tài liệu nội bộ dự án InboxIQ                        |

---

### Kết quả đạt được trong tuần 12

#### 1. Bài học về `channel.ready` — không đoán thời gian, phải chờ tín hiệu trạng thái

Lỗi đầu tiên bắt nguồn từ một giả định sai trong phần code tôi triển khai ở tuần trước.

`WebSocketChannel.connect()` trả về đối tượng channel gần như ngay lập tức, nhưng điều đó không có nghĩa quá trình WebSocket handshake đã hoàn thành. Quá trình kết nối thực tế, bao gồm bước Lambda Authorizer phía server xác thực JWT, vẫn tiếp tục diễn ra phía sau.

Trong trường hợp Lambda Authorizer bị cold start, quá trình này có thể mất nhiều thời gian hơn bình thường.

Code cũ sử dụng khoảng chờ cố định:

```dart
await Future.delayed(
  const Duration(milliseconds: 300),
);
```

Khoảng chờ 300 ms thực chất chỉ là một giả định. Nó có thể hoạt động khi server đang ở trạng thái warm nhưng thất bại khi server cold start. Đây là dạng lỗi phụ thuộc thời gian, thường xuất hiện không ổn định và rất khó tái hiện chính xác.

Cách sửa đúng bản chất là sử dụng:

```dart
await channel.ready;
```

`channel.ready` là một Future do thư viện cung cấp, chỉ hoàn thành khi quá trình handshake thực sự thành công. Nếu việc kết nối thất bại, Future sẽ ném lỗi để ứng dụng có thể xử lý rõ ràng.

Bài học rút ra là khi thư viện đã cung cấp tín hiệu trạng thái chính thức, cần sử dụng tín hiệu đó thay vì tự đoán bằng một khoảng thời gian cố định. Mọi giá trị delay cố định đều là một giả định có thể bị phá vỡ khi điều kiện hệ thống thay đổi.

#### 2. Lỗi cache `connectionId` — bài học quan trọng nhất về quản lý state

Đây là lỗi nghiêm trọng nhất phía client và cũng là bài học có giá trị nhất tôi rút ra trong toàn bộ quá trình phát triển dự án.

Màn hình Home lưu `connectionId` vào `State` một lần duy nhất khi màn hình được khởi tạo:

```dart
@override
void initState() {
  super.initState();
  _initializeWebSocket();
}
```

Sau khi nhận được `connectionId`, giá trị này được lưu vào biến state để sử dụng cho những lần gọi `Check Gmail` tiếp theo.

Tuy nhiên, WebSocket là một tài nguyên có trạng thái thay đổi liên tục. Kết nối có thể bị ngắt khi:

* Người dùng chuyển từ Wi-Fi sang mạng di động.
* Ứng dụng tạm thời chuyển xuống nền.
* Thiết bị bị mất mạng.
* Server đóng kết nối do không hoạt động trong một khoảng thời gian.
* Hệ điều hành tạm ngừng tiến trình của ứng dụng.

Khi WebSocket bị ngắt, `WebSocketService` đã dọn giá trị `connectionId` nội bộ về null. Tuy nhiên, biến state trong màn hình Home vẫn giữ bản sao cũ.

Do đó, điều kiện kiểm tra phía giao diện vẫn cho rằng ứng dụng đang có một `connectionId` hợp lệ. Khi người dùng bấm `Check Gmail`, ứng dụng gửi định danh cũ lên backend. Worker phía server sau đó cố gắng push kết quả về một WebSocket connection đã chết và nhận lỗi:

`410 Gone`

Điểm nguy hiểm của lỗi này là phía client không xuất hiện exception rõ ràng. REST request vẫn có thể thành công, giao diện vẫn hiển thị trạng thái đang xử lý nhưng kết quả không bao giờ quay trở lại.

Lỗi chỉ được phát hiện sau khi tôi và thành viên backend đối chiếu timestamp giữa:

* Thời điểm WebSocket bị disconnect.
* Thời điểm người dùng gửi request kiểm tra Gmail.
* Thời điểm backend thực hiện push kết quả.

Cách sửa là áp dụng nguyên tắc:

**Đọc giá trị đang còn hiệu lực tại thời điểm sử dụng, không tin vào bản sao đã được lưu từ trước.**

Phương thức `_checkGmail()` được sửa để lấy trực tiếp `connectionId` hiện tại từ `WebSocketService`:

```dart
Future<void> _checkGmail() async {
  var connectionId = webSocketService.connectionId;

  if (connectionId == null) {
    await webSocketService.connect();
    connectionId =
        await webSocketService.requestConnectionId();
  }

  await restApiService.checkGmail(connectionId);
}
```

Nếu kết nối đã bị ngắt và `connectionId` là null, ứng dụng sẽ:

1. Thiết lập lại kết nối WebSocket.
2. Chờ handshake hoàn thành.
3. Gửi request `whoami`.
4. Nhận `connectionId` mới.
5. Gọi API kiểm tra Gmail bằng định danh mới.

Bài học tổng quát đối với các ứng dụng real-time là trạng thái kết nối mạng có thể thay đổi bất cứ lúc nào. Một bản sao trạng thái được lưu tại tầng giao diện không thể được xem là nguồn dữ liệu đáng tin cậy lâu dài.

Có hai hướng xử lý phù hợp:

* Chủ động lắng nghe sự kiện kết nối và cập nhật state mỗi khi trạng thái thay đổi.
* Xác minh trạng thái thực tế ngay tại thời điểm cần sử dụng.

Không nên sao chép trạng thái một lần rồi mặc định rằng giá trị đó sẽ luôn đúng trong toàn bộ vòng đời của màn hình.

#### 3. Nâng cấp giao diện Material 3 có kiểm soát trước thời hạn nộp

Sau khi luồng chức năng chính hoạt động ổn định, tôi tiến hành nâng cấp giao diện bằng Material 3.

Nguyên tắc được đặt ra trong giai đoạn này là:

**Chỉ thay đổi tầng hiển thị, không chỉnh sửa logic service và cơ chế quản lý state vừa được ổn định.**

Các hạng mục được cải thiện gồm:

* Sử dụng `ColorScheme.fromSeed` để tạo hệ màu nhất quán.
* Thiết kế lại các nút hành động theo cấp độ chính và phụ.
* Bổ sung bo góc và đổ bóng nhẹ cho card.
* Thêm trạng thái rỗng có icon, tiêu đề và hướng dẫn người dùng.
* Thiết kế lại `SummaryCard` với chấm màu thể hiện mức độ ưu tiên.
* Hiển thị icon tương ứng với từng category.
* Đưa nội dung hành động đề xuất vào một khối nền màu nhạt để dễ nhận biết.
* Điều chỉnh khoảng cách giữa các thành phần để giao diện rõ ràng và dễ đọc hơn.

Việc giới hạn phạm vi thay đổi chỉ ở tầng giao diện giúp ứng dụng có diện mạo hoàn chỉnh hơn mà không làm phát sinh lỗi hồi quy trong phần kết nối WebSocket và xử lý dữ liệu.

#### 4. Hoàn thành luồng end-to-end trên thiết bị Android thật

Đến cuối tuần, toàn bộ luồng chính của ứng dụng đã được kiểm thử thành công nhiều lần trên thiết bị Android thật:

`Đăng nhập` → `Kết nối Gmail` → `Cấp quyền Google` → `Quay về ứng dụng bằng deep link` → `Bấm Check Gmail` → `Backend xử lý email` → `Kết quả được push qua WebSocket` → `Danh sách SummaryCard hiển thị trên ứng dụng`.

Luồng xử lý không sử dụng polling và không yêu cầu người dùng tự refresh màn hình. Khi backend hoàn thành việc xử lý email, kết quả được gửi trực tiếp về đúng WebSocket connection của thiết bị.

Bộ ảnh chụp màn hình đã được hoàn thiện, bao gồm:

* Màn hình đăng nhập.
* Màn hình Home khi chưa kết nối Gmail.
* Màn hình cấp quyền Google.
* Trạng thái Gmail đã kết nối.
* Trạng thái đang kiểm tra Gmail.
* Danh sách kết quả tóm tắt email.
* Chi tiết các `SummaryCard` với mức độ ưu tiên và hành động đề xuất.

Bộ ảnh và phần mô tả luồng đã được bàn giao cho thành viên phụ trách tài liệu để sử dụng trong báo cáo nhóm.

---

### Đánh giá kết quả tuần 12

* Hai lỗi tích hợp phía client gồm handshake chưa hoàn thành và cache `connectionId` đã được xử lý đúng nguyên nhân gốc, không sử dụng giải pháp vá tạm thời.
* WebSocket client sử dụng `channel.ready` để chờ kết nối thực sự sẵn sàng thay vì phụ thuộc vào delay cố định.
* Ứng dụng tự kiểm tra và khôi phục WebSocket connection trước khi thực hiện yêu cầu kiểm tra Gmail.
* Luồng end-to-end hoạt động hoàn chỉnh trên thiết bị Android thật.
* Kết quả tóm tắt email được backend push về ứng dụng theo thời gian thực mà không cần polling.
* Giao diện được nâng cấp theo Material 3 mà không gây lỗi hồi quy trong tầng service và state.
* Bộ ảnh minh họa toàn bộ luồng sử dụng đã được hoàn thiện và bàn giao cho nhóm.
* Mục tiêu chính của phần client trong dự án InboxIQ đã được hoàn thành.

---

### Khó khăn gặp phải

* Lỗi cache `connectionId` xảy ra âm thầm, không tạo exception và không có log lỗi rõ ràng phía client. Lỗi chỉ được phát hiện khi đối chiếu log giữa client và backend.
* Delay cố định 300 ms tạo ra lỗi chập chờn phụ thuộc vào trạng thái cold hoặc warm của Lambda Authorizer. Có thời điểm kiểm thử thành công và có thời điểm thất bại, dễ dẫn đến kết luận sai rằng lỗi đã được xử lý.
* Quá trình debug cần đồng bộ timestamp giữa log Flutter, API backend và WebSocket push.
* Việc xác định `410 Gone` yêu cầu hiểu rằng REST request thành công không đồng nghĩa WebSocket connection vẫn còn tồn tại.
* Có áp lực hoàn thiện giao diện sát thời hạn nộp trong khi phần logic tích hợp vừa mới ổn định.
* Việc thay đổi giao diện ở giai đoạn cuối có nguy cơ gây lỗi hồi quy nếu vô tình chỉnh sửa cả logic và state.

---

### Hướng khắc phục và Best Practices đúc kết được

* Với trạng thái của tài nguyên bên ngoài như kết nối mạng, WebSocket hoặc phiên đăng nhập, luôn xác minh giá trị tại thời điểm sử dụng thay vì tin vào bản sao đã được lưu trước đó.
* Không sử dụng delay cố định để thay thế tín hiệu trạng thái do thư viện cung cấp.
* Sử dụng `channel.ready` để xác định thời điểm WebSocket handshake thực sự hoàn thành.
* Khi WebSocket bị ngắt, phải xóa toàn bộ trạng thái liên quan, bao gồm channel, connection status và `connectionId`.
* Trước khi gọi API phụ thuộc vào WebSocket connection, cần kiểm tra kết nối còn hoạt động hay không.
* Xây dựng cơ chế reconnect và lấy lại `connectionId` mới trước khi tiếp tục request.
* Lỗi tích hợp phải được debug bằng dữ liệu từ cả client và backend. Mỗi phía chỉ quan sát log của riêng mình sẽ khó xác định được nguyên nhân gốc.
* Cần đồng bộ timestamp trong log để truy vết thứ tự xảy ra của các sự kiện.
* Phân biệt rõ REST request thành công với WebSocket push thành công. Đây là hai giai đoạn độc lập và có thể thất bại riêng biệt.
* Khi chỉnh sửa giao diện gần thời hạn nộp, cần giới hạn phạm vi thay đổi ở tầng hiển thị và không can thiệp vào service hoặc state đã ổn định.
* Sau mỗi thay đổi giao diện, phải kiểm thử lại toàn bộ luồng chính để phát hiện lỗi hồi quy.
* Không ghi JWT, nội dung Gmail hoặc thông tin nhạy cảm vào log khi chụp ảnh hoặc bàn giao báo cáo.

---

### Định hướng sau dự án

* Bổ sung `WidgetsBindingObserver` để theo dõi vòng đời ứng dụng và tự động reconnect WebSocket khi ứng dụng quay lại từ trạng thái nền.
* Hiện tại, cơ chế phục hồi kết nối chỉ được kích hoạt khi người dùng bấm nút `Check Gmail`; cần bổ sung cơ chế chủ động hơn.
* Xử lý lỗi tràn layout tại màn hình Login khi bàn phím xuất hiện bằng cách sử dụng `SingleChildScrollView` hoặc bố cục responsive phù hợp.
* Xây dựng màn hình đăng ký tài khoản để người dùng mới có thể tự tạo tài khoản thay vì phải tạo thủ công trên Cognito Console.
* Bổ sung chức năng xác nhận email và gửi lại mã xác nhận.
* Xây dựng cơ chế refresh token và xử lý phiên đăng nhập hết hạn rõ ràng hơn.
* Bổ sung trạng thái reconnect và thông báo mất kết nối trên giao diện.
* Viết unit test cho `AuthService`, `RestApiService`, `WebSocketService` và các model parse JSON.
* Viết integration test cho luồng Login, Gmail OAuth và nhận kết quả qua WebSocket.
* Tổng hợp các bài học về quản lý state của kết nối dài hạn thành tài liệu chia sẻ nội bộ nhóm.
