---

title: "Worklog Tuần 11"
date: 2026-06-29
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
-----------------------

### Mục tiêu tuần 11: Hai cây cầu nối ứng dụng với thế giới bên ngoài — OAuth Deep Link và WebSocket

Trong tuần này, tôi xây dựng hai “cây cầu” quan trọng nhất giúp ứng dụng kết nối với các hệ thống bên ngoài. Cây cầu thứ nhất kết nối ứng dụng với Google thông qua luồng cấp quyền Gmail, được mở trên trình duyệt ngoài và quay trở lại ứng dụng bằng deep link. Cây cầu thứ hai kết nối ứng dụng với backend theo thời gian thực thông qua WebSocket client.

Cả hai cơ chế đều có một điểm chung: chúng phụ thuộc vào các thành phần nằm ngoài khả năng kiểm soát trực tiếp của mã nguồn Flutter, bao gồm hành vi của trình duyệt, hệ điều hành Android và điều kiện mạng. Vì vậy, phần khó nhất không chỉ nằm ở việc viết code mà còn ở việc dự đoán những tình huống khiến luồng xử lý có thể bị gián đoạn hoặc bị từ chối.

* Cấu hình deep link `inboxiq://` trong `AndroidManifest.xml` và hiểu đúng sự khác biệt giữa custom scheme và Android App Links.
* Xây dựng `GmailOAuthService` để gọi REST API khởi tạo, nhận URL cấp quyền Google, mở trình duyệt ngoài và lắng nghe callback bằng deep link.
* Phối hợp với thành viên backend xử lý sự cố trình duyệt chặn tự động chuyển hướng trở lại ứng dụng.
* Xây dựng `RestApiService` để gọi các REST endpoint có xác thực bằng JWT.
* Xây dựng `WebSocketService` để kết nối kèm JWT trong query string, quản lý luồng message nhận về và gửi yêu cầu `whoami` để lấy WebSocket connection ID.
* Hoàn thiện màn hình Home với chức năng kết nối Gmail, kiểm tra Gmail, hiển thị danh sách kết quả và tự động khởi tạo WebSocket khi màn hình được mở.

---

### Các công việc cần triển khai trong tuần này

| Thứ      | Hạng mục công việc                                                                                                                                                                                                                                                                                                                                                                                                                  | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu tham khảo                         |
| -------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | --------------- | ------------------------------------------------ |
| Thứ 2    | - Thêm quyền `<uses-permission android:name="android.permission.INTERNET" />` và intent-filter deep link `inboxiq://` vào `AndroidManifest.xml`. <br> - Tìm hiểu sự khác nhau giữa custom URL scheme và Android App Links. Xác định không sử dụng `android:autoVerify="true"` vì thuộc tính này dành cho App Links, yêu cầu domain HTTPS thật và file xác minh `assetlinks.json`.                                                   | 29/06/2026   | 29/06/2026      | https://developer.android.com/training/app-links |
| Thứ 3    | - Xây dựng `RestApiService` theo kiến trúc instance-based, nhận `AuthService` thông qua constructor. <br> - Viết các phương thức `initGmailOAuth()` và `checkGmail(connectionId)`, tự động đính kèm JWT vào header xác thực của mỗi request. <br> - Xây dựng `GmailOAuthService` để gọi endpoint khởi tạo, nhận authorization URL và mở bằng `url_launcher` với `LaunchMode.externalApplication`.                                   | 30/06/2026   | 30/06/2026      | https://pub.dev/packages/url_launcher            |
| Thứ 4    | - Viết `listenForCallback()` bằng package `app_links` để lắng nghe URI `inboxiq://gmail-connected` và trích xuất email từ query parameter. <br> - Kiểm thử luồng OAuth đầu tiên trên thiết bị Android thật và phát hiện trình duyệt hiển thị trang thành công nhưng không tự động quay trở lại ứng dụng.                                                                                                                            | 01/07/2026   | 01/07/2026      | https://pub.dev/packages/app_links               |
| Thứ 5    | - Phối hợp với thành viên backend xác định nguyên nhân: Chrome chặn tự động chuyển hướng đến custom scheme lạ khi không có thao tác trực tiếp của người dùng ngay trước đó. <br> - Cập nhật trang callback phía backend bằng cả `meta refresh` và đường dẫn dự phòng để người dùng bấm thủ công. <br> - Kiểm thử lại toàn bộ luồng và xác nhận ứng dụng nhận được callback, đồng thời cập nhật trạng thái Gmail thành “Đã kết nối”. | 02/07/2026   | 02/07/2026      | Tài liệu nội bộ nhóm                             |
| Thứ 6    | - Xây dựng `WebSocketService` bằng `web_socket_channel`, kết nối với URL có dạng `?token=<idToken>`. <br> - Phát message nhận được qua broadcast stream và xóa trạng thái kết nối khi xảy ra `onDone` hoặc `onError`. <br> - Viết `requestConnectionId()` để gửi `{"action": "whoami"}`, chờ `whoami-response`, đặt thời gian timeout và trả về connection ID.                                                                      | 03/07/2026   | 03/07/2026      | https://pub.dev/packages/web_socket_channel      |
| Thứ 7    | - Xây dựng model `EmailSummary` với phương thức `fromJson` có khả năng xử lý dữ liệu thiếu hoặc null. <br> - Hỗ trợ parse trường `createdAt` từ cả giá trị epoch và chuỗi thời gian định dạng ISO. <br> - Xây dựng widget `SummaryCard` để hiển thị nội dung tóm tắt, danh mục, mức độ ưu tiên và hành động đề xuất, kèm chỉ báo trực quan theo mức ưu tiên.                                                                        | 04/07/2026   | 04/07/2026      | https://docs.flutter.dev                         |
| Chủ nhật | - Hoàn thiện màn hình Home, khởi tạo WebSocket và gửi yêu cầu `whoami` trong `initState`. <br> - Thêm nút kết nối Gmail, nút kiểm tra Gmail, danh sách `SummaryCard` và chức năng đăng xuất. <br> - Kiểm thử luồng đến bước gửi yêu cầu kiểm tra Gmail. Phần nhận kết quả push qua WebSocket sẽ được kiểm thử cùng backend trong tuần tiếp theo. <br> - Viết báo cáo tổng kết tuần.                                                 | 05/07/2026   | 05/07/2026      | Tài liệu nội bộ dự án InboxIQ                    |

---

### Kết quả đạt được trong tuần 11

#### 1. OAuth Deep Link — hoàn thành cột mốc khó nhất của phần client

Luồng kết nối Gmail hoàn chỉnh đã hoạt động thành công trên thiết bị Android thật:

`Kết nối Gmail` → mở trình duyệt ngoài → hiển thị màn hình cấp quyền Google → người dùng cấp quyền → mở trang callback của backend → quay trở lại ứng dụng bằng `inboxiq://gmail-connected` → cập nhật trạng thái thành `Đã kết nối Gmail`.

Mặc dù quá trình này có vẻ đơn giản từ góc nhìn người dùng, nhưng thực tế luồng xử lý phải đi qua nhiều ranh giới hệ thống khác nhau:

* Ứng dụng Flutter
* Trình duyệt ngoài
* Máy chủ xác thực của Google
* Backend của InboxIQ
* Hệ điều hành Android
* Quay trở lại ứng dụng Flutter

Mỗi lần chuyển tiếp giữa các thành phần đều có thể trở thành một điểm lỗi.

Sự cố thực tế xảy ra khi Chrome trên Android chặn việc tự động chuyển hướng đến một custom URL scheme không phải HTTP hoặc HTTPS. Nguyên nhân là trước thời điểm redirect không có thao tác trực tiếp từ người dùng. Đây là một cơ chế bảo mật của trình duyệt nhằm ngăn các website độc hại âm thầm mở ứng dụng khác.

Kết quả là trang callback hiển thị thông báo kết nối thành công nhưng người dùng vẫn bị giữ lại trong trình duyệt, không quay về ứng dụng InboxIQ.

Giải pháp được thực hiện thông qua việc phối hợp với thành viên backend. Trang callback được bổ sung hai cơ chế chuyển hướng:

* Sử dụng `meta refresh` để thử tự động chuyển về ứng dụng sau một giây.
* Cung cấp đường dẫn dự phòng để người dùng bấm thủ công nếu trình duyệt không cho phép tự động chuyển hướng.

Cách xử lý này đảm bảo người dùng luôn có một đường quay về ứng dụng, kể cả khi trình duyệt từ chối chuyển hướng tự động.

Một quyết định đúng ngay từ đầu là sử dụng `LaunchMode.externalApplication` thay vì mở luồng OAuth trong WebView nhúng bên trong ứng dụng. Google hạn chế việc xác thực OAuth trong WebView vì lý do bảo mật. Nếu sử dụng sai chế độ mở, yêu cầu đăng nhập có thể bị từ chối ngay tại màn hình xác thực Google.

#### 2. Hiểu đúng custom scheme và App Links — tránh được cấu hình sai

Android Studio đề xuất thêm thuộc tính:

`android:autoVerify="true"`

vào intent-filter của deep link.

Tuy nhiên, sau khi tìm hiểu kỹ, tôi xác định thuộc tính này thuộc cơ chế Android App Links. App Links sử dụng domain HTTPS đã được xác minh và yêu cầu server lưu file `assetlinks.json` để chứng minh mối liên kết giữa domain và ứng dụng Android.

Dự án InboxIQ sử dụng custom scheme:

`inboxiq://`

Custom scheme không sử dụng cơ chế xác minh domain và không cần `android:autoVerify="true"`.

Việc thêm thuộc tính này không cải thiện khả năng hoạt động của custom scheme, đồng thời có thể tạo ra các cảnh báo cấu hình gây hiểu nhầm.

Bài học rút ra là các gợi ý từ IDE không phải lúc nào cũng phù hợp với ngữ cảnh kỹ thuật hiện tại. Trước khi áp dụng một gợi ý tự động, cần hiểu rõ cơ chế phía dưới và mục đích thực sự của cấu hình đó.

#### 3. WebSocket client với cơ chế “Who Am I?”

`WebSocketService` được xây dựng để thiết lập kết nối WebSocket với Cognito JWT được truyền trong query string:

`?token=<idToken>`

Cách triển khai này được sử dụng vì WebSocket client trên Flutter mobile không phải lúc nào cũng có thể đính kèm custom HTTP header theo cách tương tự REST request.

Sau khi kết nối thành công, client gửi message:

```json
{
  "action": "whoami"
}
```

Backend trả về message `whoami-response`, trong đó chứa WebSocket `connectionId`.

Connection ID này là thông tin bắt buộc khi client gọi REST API kiểm tra Gmail. Backend worker sử dụng giá trị này để xác định kết nối WebSocket nào sẽ nhận kết quả tóm tắt Gmail sau khi xử lý hoàn tất.

Phương thức `requestConnectionId()` cũng được thiết kế với timeout. Nếu không có timeout, ứng dụng có thể bị treo ở trạng thái tải vô thời hạn trong trường hợp server không phản hồi hoặc kết nối mạng bị gián đoạn.

#### 4. Model có khả năng xử lý dữ liệu không hoàn hảo

Phương thức `EmailSummary.fromJson` được thiết kế có chủ đích theo hướng linh hoạt với dữ liệu thiếu hoặc không hoàn toàn đồng nhất.

Các field bị thiếu hoặc có giá trị null không làm ứng dụng bị crash. Trường `createdAt` có thể được parse từ:

* Giá trị epoch dạng số.
* Chuỗi thời gian định dạng ISO.

Quyết định này cần thiết vì dữ liệu đi qua một pipeline gồm nhiều giai đoạn:

`Gmail → OpenAI → DynamoDB → WebSocket → ứng dụng Flutter`

Các giai đoạn được phát triển bởi nhiều thành viên khác nhau, do đó định dạng dữ liệu thực tế có thể có sai lệch nhỏ so với đặc tả ban đầu.

Nếu client parse dữ liệu quá nghiêm ngặt, ứng dụng có thể crash chỉ vì những khác biệt nhỏ. Parser linh hoạt giúp ứng dụng vẫn hiển thị được các dữ liệu hợp lệ và xử lý an toàn với những field không bắt buộc.

Tuy nhiên, việc parse linh hoạt không có nghĩa là chấp nhận mọi dữ liệu sai. Các field quan trọng và thông tin thuộc giao thức vẫn cần được kiểm tra để tránh che giấu lỗi tích hợp thực sự.

---

### Đánh giá kết quả tuần 11

* Luồng OAuth deep link hoàn chỉnh đã hoạt động thành công trên thiết bị Android thật.
* Cột mốc tích hợp có mức độ rủi ro cao nhất của phần client đã được hoàn thành.
* Tầng service của ứng dụng đã được hoàn thiện với `AuthService`, `RestApiService`, `GmailOAuthService` và `WebSocketService`.
* Tất cả service đều tuân theo kiến trúc instance-based đã thống nhất.
* WebSocket client kết nối thành công bằng JWT và lấy được connection ID thông qua giao thức `whoami`.
* Màn hình Home đã hoàn thiện các chức năng kết nối Gmail, kiểm tra Gmail, hiển thị kết quả và đăng xuất.
* Ứng dụng client đã sẵn sàng cho giai đoạn kiểm thử tích hợp end-to-end với backend.
* Hai bài học quan trọng tại ranh giới hệ thống đã được ghi lại cho nhóm: Chrome có thể chặn custom-scheme redirect và Google OAuth không nên chạy trong WebView nhúng.

---

### Khó khăn gặp phải

* Chrome chặn tự động chuyển hướng đến custom scheme nhưng không hiển thị thông báo lỗi rõ ràng. Trang callback chỉ đứng yên, buộc phải thử nghiệm nhiều lần để xác định nguyên nhân.
* Toàn bộ luồng deep link chỉ có thể kiểm thử chính xác trên thiết bị Android thật với trình duyệt thật. Hành vi trên emulator khác thiết bị thật ở những điểm tích hợp quan trọng.
* Debug WebSocket khó hơn REST đáng kể vì không có giao diện đơn giản để quan sát toàn bộ quá trình trao đổi.
* Việc debug WebSocket phụ thuộc nhiều vào log đồng bộ từ cả phía Flutter client và backend.
* Luồng Gmail OAuth đi qua nhiều ranh giới hệ thống, khiến việc xác định thành phần gây lỗi trở nên phức tạp.
* Hành vi của trình duyệt ngoài và hệ điều hành không thể được kiểm soát hoàn toàn từ mã nguồn Flutter.

---

### Hướng khắc phục và Best Practices đúc kết được

* Với các luồng liên quan đến trình duyệt hoặc điều hướng của hệ điều hành, luôn thiết kế một đường dự phòng thủ công bên cạnh cơ chế tự động.
* Luồng OAuth trên thiết bị di động phải được kiểm thử trên thiết bị thật với trình duyệt mặc định mà người dùng thực tế có thể sử dụng.
* Sử dụng `LaunchMode.externalApplication` cho Google OAuth thay vì WebView nhúng.
* Hiểu rõ sự khác biệt giữa custom URL scheme và App Links đã xác minh trước khi thay đổi cấu hình intent-filter trên Android.
* Thiết kế model linh hoạt với các field tùy chọn hoặc dữ liệu sai lệch nhẹ khi dữ liệu đi qua nhiều tầng xử lý do nhiều người phát triển.
* Không nên biến toàn bộ field quan trọng thành tùy chọn chỉ để tránh crash. Các lỗi giao thức vẫn phải được ghi log và hiển thị trong quá trình phát triển.
* Đặt timeout cho mọi thao tác mạng phải chờ phản hồi, kể cả khi server được kỳ vọng sẽ phản hồi ngay lập tức.
* Sử dụng broadcast stream cho WebSocket khi có nhiều thành phần trong ứng dụng cùng cần lắng nghe message.
* Xóa trạng thái kết nối trong cả `onDone` và `onError` để tránh coi một WebSocket đã đóng là kết nối vẫn còn hoạt động.
* Không ghi JWT, OAuth code, dữ liệu Gmail hoặc thông tin nhạy cảm vào log của ứng dụng.
* Thống nhất định dạng log giữa client và backend để có thể truy vết một request xuyên suốt qua REST API, WebSocket và worker xử lý.

---

### Định hướng cho tuần 12

* Thực hiện kiểm thử tích hợp end-to-end cùng thành viên backend trên thiết bị Android thật.
* Kiểm thử toàn bộ luồng từ khi người dùng bấm `Kiểm tra Gmail` đến khi danh sách email đã xử lý được push về qua WebSocket.
* Xác nhận REST request gửi đúng connection ID và backend push kết quả về đúng kết nối WebSocket tương ứng.
* Kiểm thử các trường hợp timeout, mất mạng, token hết hạn, message sai định dạng và kết nối lại.
* Phân tích các lỗi chỉ xuất hiện khi ghép hoàn chỉnh client và backend.
* Cải thiện giao diện sau khi luồng chức năng chính hoạt động ổn định.
* Áp dụng Material 3 theme và thiết kế lại `SummaryCard`.
* Bổ sung trạng thái tải, thành công, không có dữ liệu và lỗi rõ ràng hơn cho quá trình xử lý Gmail.
