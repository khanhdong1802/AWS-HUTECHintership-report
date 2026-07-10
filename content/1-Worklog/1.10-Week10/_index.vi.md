---

title: "Worklog Tuần 10"
date: 2026-06-22
weight: 10
chapter: false
pre: " <b> 1.10. </b> "
-----------------------

### Mục tiêu tuần 10: Dựng bộ khung ứng dụng Flutter và lớp xác thực Cognito

Trong phân công của nhóm InboxIQ, tôi phụ trách toàn bộ phần client — ứng dụng di động Flutter mà người dùng cuối trực tiếp tương tác. Tuần này, trong khi thành viên phụ trách backend xây dựng hạ tầng phía server, mục tiêu của tôi là chuẩn bị hoàn chỉnh bộ khung ứng dụng: môi trường phát triển ổn định, cấu trúc project rõ ràng và hoàn thành lớp xác thực Cognito — cánh cửa đầu tiên người dùng phải đi qua trước khi sử dụng các tính năng của hệ thống.

* Thiết lập môi trường Flutter hoàn chỉnh trên Windows với Android Studio, kiểm tra `flutter doctor` không còn cảnh báo.
* Khởi tạo project `inboxiq_app`, khai báo đầy đủ dependencies phục vụ các giai đoạn sau như Amplify, WebSocket và deep link.
* Thiết kế cấu trúc thư mục `lib/` theo từng tầng trách nhiệm: config, services, models, screens và widgets.
* Chốt kiến trúc các service theo hướng instance-based, truyền qua constructor và không sử dụng static.
* Tích hợp Amplify Auth Cognito: cấu hình, đăng nhập và lấy JWT, đồng bộ với User Pool do thành viên phụ trách bảo mật tạo.
* Hoàn thành màn hình Login và cơ chế điều hướng theo trạng thái đăng nhập.

---

### Các công việc cần triển khai trong tuần này:

| Thứ | Hạng mục công việc                                                                                                                                                                                                                                                                                                            | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu tham khảo                               |
| --- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | --------------- | ------------------------------------------------------ |
| 2   | - Họp nhóm chốt kiến trúc và phân công; nhận phần phát triển client Flutter. <br> - Cài đặt hoặc cập nhật Flutter SDK, Android Studio kèm plugin Flutter/Dart, chạy `flutter doctor` và xử lý toàn bộ cảnh báo.                                                                                                               | 22/06/2026   | 22/06/2026      | https://docs.flutter.dev/get-started                   |
| 3   | - Khởi tạo project bằng lệnh `flutter create inboxiq_app --org com.inboxiq`, chỉ chọn nền tảng Android và ngôn ngữ Kotlin. <br> - Khai báo dependencies trong `pubspec.yaml`: `amplify_flutter`, `amplify_auth_cognito`, `http`, `web_socket_channel`, `app_links`, `url_launcher`, `provider`, `shared_preferences`, `intl`. | 23/06/2026   | 23/06/2026      | https://pub.dev                                        |
| 4   | - Dựng cấu trúc thư mục `lib/`: `config/`, `services/`, `models/`, `screens/`, `widgets/`. <br> - Viết `app_config.dart` chứa endpoint và Cognito ID nhận từ phía backend, tách cấu hình khỏi phần logic xử lý.                                                                                                               | 24/06/2026   | 24/06/2026      | Nội bộ nhóm                                            |
| 5   | - Nghiên cứu tài liệu Amplify Flutter: luồng configure, plugin Auth Cognito và các authentication flow. <br> - Viết `amplifyconfiguration.dart`, đối chiếu kỹ `authenticationFlowType` với cấu hình App Client thực tế trên Cognito Console thay vì sao chép giá trị mặc định từ tài liệu.                                    | 25/06/2026   | 25/06/2026      | https://docs.amplify.aws/flutter/                      |
| 6   | - Viết class `AuthService` theo hướng instance-based gồm các phương thức: `configureAmplify`, `signIn`, `signOut`, `isSignedIn`, `getIdToken`. <br> - Viết `main.dart`: khởi tạo `AuthService` một lần duy nhất và sử dụng trạng thái đăng nhập để quyết định màn hình khởi đầu.                                              | 26/06/2026   | 26/06/2026      | https://docs.amplify.aws/flutter/build-a-backend/auth/ |
| 7   | - Viết màn hình Login gồm form email, mật khẩu, gọi `signIn`, điều hướng sang Home khi đăng nhập thành công và hiển thị lỗi khi thất bại. <br> - Kiểm thử đăng nhập bằng tài khoản test trên máy ảo Android.                                                                                                                  | 27/06/2026   | 27/06/2026      | https://docs.flutter.dev                               |
| CN  | - Kiểm thử đăng nhập trên thiết bị Android thật thông qua USB debugging, ghi nhận các vấn đề chỉ xuất hiện trên thiết bị thật như bàn phím tự sửa hoặc thêm ký tự. <br> - Viết báo cáo tổng kết tuần, xác nhận với thành viên backend về định dạng JWT sẽ sử dụng cho các API trong tuần sau.                                 | 28/06/2026   | 28/06/2026      | Nội bộ dự án InboxIQ                                   |

---

### Kết quả đạt được tuần 10:

#### 1. Môi trường và bộ khung project sẵn sàng cho cả dự án

`flutter doctor` không còn cảnh báo, môi trường đã nhận đầy đủ Flutter SDK, Android toolchain, Android Studio và các plugin cần thiết. Cấu trúc `lib/` được chia theo từng tầng trách nhiệm ngay từ đầu — services không phụ thuộc vào UI, screens không tự gọi API mà thực hiện thông qua service — giúp việc bổ sung các tính năng trong những tuần sau không phải thay đổi lại toàn bộ cấu trúc project.

Một sự cố môi trường đáng ghi nhận là quá trình build Flutter đôi lúc xuất hiện lỗi `"Daemon compilation failed"` nhưng không chỉ rõ nguyên nhân. Sau khi kiểm tra, tôi xác định Kotlin incremental compilation bị lỗi cache khi Pub Cache nằm trên ổ `C:` còn project nằm trên ổ `D:`. Lỗi được khắc phục bằng cách thêm `kotlin.incremental=false` vào file `android/gradle.properties`, sau đó chạy `flutter clean` và build lại project. Những lỗi không liên quan trực tiếp đến phần code thường tốn nhiều thời gian xử lý nhất vì thông báo lỗi không chỉ ra đúng nguồn gốc vấn đề.

#### 2. Quyết định kiến trúc: instance-based thay vì static

Nhiều mẫu code Flutter trên Internet xây dựng service theo kiểu static hoặc singleton toàn cục để thuận tiện khi gọi. Tôi lựa chọn hướng ngược lại: mỗi service là một class thông thường, được khởi tạo một lần tại `main.dart` và truyền xuống các màn hình thông qua constructor.

Lý do của quyết định này sẽ thể hiện rõ hơn trong các tuần tiếp theo. `WebSocketService` phải duy trì một kết nối mạng liên tục và cần kiểm soát vòng đời chặt chẽ, bao gồm thời điểm connect, disconnect và dispose. Nếu sử dụng trạng thái toàn cục ẩn, việc theo dõi và debug kết nối WebSocket sẽ trở nên khó kiểm soát.

#### 3. Bài học về `authenticationFlowType` — không sao chép mặc định từ tài liệu

Tài liệu mẫu của Amplify sử dụng `USER_SRP_AUTH`, nhưng khi đối chiếu với App Client thực tế trên Cognito Console do thành viên phụ trách bảo mật tạo, cấu hình được bật là `USER_PASSWORD_AUTH`.

Nếu tiếp tục sử dụng giá trị trong tài liệu mẫu, phương thức `signIn()` sẽ thất bại do authentication flow phía client không được App Client hỗ trợ. Đây là loại lỗi khó truy vết vì thông báo trả về không chỉ rõ rằng flow được cấu hình trên client không đồng bộ với phía server.

Nguyên tắc rút ra là mọi giá trị cấu hình phía client phải được đối chiếu với trạng thái thực tế của hệ thống phía server, không nên mặc định tin hoàn toàn vào cấu hình trong tài liệu mẫu.

#### 4. Lỗi chỉ xuất hiện trên thiết bị thật

Luồng đăng nhập hoạt động bình thường trên máy ảo nhưng thất bại với thông báo `"Incorrect username or password"` trên điện thoại thật dù thông tin đăng nhập được nhập chính xác.

Nguyên nhân được xác định là bàn phím ảo trên điện thoại tự động sửa nội dung hoặc thêm ký tự vào trường mật khẩu. Tôi đã tắt tính năng tự sửa và gợi ý từ cho trường mật khẩu trước khi thực hiện đăng nhập.

Đây là lý do việc kiểm thử trên thiết bị thật ngay từ tuần đầu phát triển tính năng là cần thiết, vì có một nhóm lỗi liên quan đến bàn phím, hệ điều hành và hành vi thiết bị không thể xuất hiện trên emulator.

---

### Đánh giá kết quả tuần 10:

* Bộ khung ứng dụng đã hoàn chỉnh: môi trường ổn định, cấu trúc rõ ràng và dependencies đầy đủ cho các giai đoạn tiếp theo.
* Luồng xác thực Cognito hoạt động ổn định trên cả máy ảo và thiết bị Android thật.
* Kiến trúc instance-based đã được thống nhất và áp dụng nhất quán từ service đầu tiên.
* Hoàn thành các chức năng đăng nhập, đăng xuất, kiểm tra trạng thái đăng nhập và lấy JWT.
* Hoàn thành màn hình Login và điều hướng người dùng theo trạng thái xác thực.
* Đã xác nhận cơ chế JWT với phía backend: các API trong tuần tiếp theo sẽ xác thực bằng `idToken` được lấy từ `AuthService.getIdToken()`.

---

### Khó khăn gặp phải:

* Lỗi build Kotlin do Pub Cache và project nằm trên hai ổ đĩa khác nhau — loại lỗi môi trường không được thể hiện rõ trong thông báo build và phải tìm hiểu thêm từ issue tracker cộng đồng.
* Amplify Flutter có nhiều phiên bản với API thay đổi đáng kể giữa các bản lớn, trong khi code mẫu trên Internet thường bị lẫn giữa phiên bản cũ và mới.
* Cấu hình `authenticationFlowType` trên client ban đầu không đồng bộ với App Client thực tế trên Cognito.
* Trường mật khẩu trên thiết bị thật bị bàn phím can thiệp, tự sửa hoặc thêm ký tự, gây lỗi đăng nhập khó xác định nguyên nhân.

---

### Hướng khắc phục và Best Practices đúc kết được:

* Với các lỗi build khó hiểu và không liên quan trực tiếp đến source code, cần kiểm tra sớm các yếu tố môi trường như đường dẫn, ổ đĩa, cache và phiên bản công cụ trước khi nghi ngờ logic chương trình.
* Luôn kiểm tra code mẫu cùng với phiên bản thư viện; ưu tiên tài liệu chính thức tương ứng với version đang được khai báo trong `pubspec.yaml`.
* Đối chiếu toàn bộ cấu hình Cognito phía client với cấu hình thực tế trên AWS Console, đặc biệt là Region, User Pool ID, App Client ID và authentication flow.
* Thiết kế service theo hướng instance-based để kiểm soát rõ dependency và vòng đời của từng đối tượng.
* Kiểm thử trên thiết bị thật song song với máy ảo ngay từ tính năng đầu tiên, không chờ đến cuối dự án.
* Với trường mật khẩu, cần tắt tính năng tự sửa và gợi ý từ của bàn phím. Không nên tự động sử dụng `.trim()` cho mật khẩu vì có thể làm thay đổi mật khẩu hợp lệ nếu người dùng chủ động đặt khoảng trắng ở đầu hoặc cuối.

---

### Định hướng cho tuần tiếp theo:

* Xây dựng luồng kết nối Gmail phía client: gọi REST API khởi tạo, mở trình duyệt để người dùng thực hiện consent và nhận callback thông qua deep link `inboxiq://`.
* Viết `WebSocketService` kết nối đến WebSocket API mà phía backend đang xây dựng, xử lý xác thực JWT trong quá trình thiết lập kết nối.
* Quản lý vòng đời kết nối WebSocket gồm connect, disconnect, dispose và reconnect khi kết nối bị gián đoạn.
* Phối hợp với thành viên backend để thống nhất định dạng message WebSocket hai chiều, bao gồm các message `whoami` và `summary-ready`.
