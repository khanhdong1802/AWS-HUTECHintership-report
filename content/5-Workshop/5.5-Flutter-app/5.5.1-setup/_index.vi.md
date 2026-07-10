---
title: "Thiết lập môi trường và kiến trúc app"
date: 2026-07-09
weight: 1
chapter: false
pre: " <b> 5.5.1. </b> "
---

#### 1. Kiểm tra môi trường

```powershell
flutter doctor
```

Cần sạch hoàn toàn trước khi bắt đầu: Flutter SDK, Android toolchain, Android Studio kèm plugin Flutter/Dart đã cập nhật. Dự án này chỉ nhắm tới Android (không build iOS), nên có thể bỏ qua cảnh báo liên quan tới Xcode/CocoaPods nếu `flutter doctor` có nhắc tới.

Môi trường cụ thể dùng cho InboxIQ: Flutter 3.38.2, IDE là **Android Studio** (không phải VS Code) cho toàn bộ phần Flutter, trong khi backend (SAM/Lambda) vẫn mở bằng VS Code — hai project được đặt cùng một thư mục cha để công cụ hỗ trợ AI (Claude Code) truy cập được cả hai cùng lúc:

```
D:\AWS\Project\AWSF\
├── inboxiq-backend\     ← SAM/Lambda, VS Code
├── inboxiq_app\          ← Flutter, Android Studio
```

{{% notice tip %}}
Nếu pub cache (thường ở ổ `C:`) và project nằm khác ổ đĩa với thư mục project (ở đây là ổ `D:`), Kotlin incremental compilation có thể vỡ cache giữa chừng, báo lỗi "Daemon compilation failed" khó hiểu. Cách xử lý: thêm `kotlin.incremental=false` vào `android/gradle.properties`, sau đó chạy `flutter clean` trước khi build lại.
{{% /notice %}}

#### 2. Khởi tạo project

```powershell
flutter create inboxiq_app --org com.inboxiq
cd inboxiq_app
```

Chỉ chọn platform Android lúc tạo, dùng Kotlin (mặc định của `flutter create` hiện tại).

#### 3. Khai báo dependencies

Sửa `pubspec.yaml`:

```yaml
dependencies:
  flutter:
    sdk: flutter
  cupertino_icons: ^1.0.6

  # AWS
  amplify_flutter: ^2.0.0
  amplify_auth_cognito: ^2.0.0

  # HTTP + WebSocket
  http: ^1.2.0
  web_socket_channel: ^3.0.0

  # Deep link (OAuth callback)
  app_links: ^6.0.0
  url_launcher: ^6.3.0

  # State + storage
  provider: ^6.1.0
  shared_preferences: ^2.2.0

  # UI
  intl: ^0.19.0
```

```powershell
flutter pub get
```

![pubspec.yaml sau khi thêm đầy đủ dependencies](/AWS-HUTECHintership-report/images/5-Workshop/5.5-Flutter-app/pubspec-dependencies.jpg)

#### 4. Cấu trúc thư mục `lib/`

Tổ chức theo tầng trách nhiệm rõ ràng, không gộp logic vào widget:

```
lib/
├── main.dart
├── config/
│   └── app_config.dart          # Endpoint URLs, Cognito IDs
├── services/
│   ├── auth_service.dart         # Cognito login/signup
│   ├── rest_api_service.dart     # HTTP calls
│   ├── websocket_service.dart    # WebSocket connect + listen
│   └── gmail_oauth_service.dart  # OAuth init + deep link handler
├── models/
│   └── email_summary.dart
├── screens/
│   ├── login_screen.dart
│   └── home_screen.dart
└── widgets/
    └── summary_card.dart
```

![Cấu trúc thư mục lib/ trong Android Studio](/AWS-HUTECHintership-report/images/5-Workshop/5.5-Flutter-app/lib-folder-structure.jpg)

#### 5. Quyết định kiến trúc: instance-based, không dùng static

Toàn bộ service (`AuthService`, `RestApiService`, `WebSocketService`, `GmailOAuthService`) được thiết kế **instance-based** — khởi tạo một lần ở `main.dart`, truyền xuống các màn hình qua constructor — thay vì static class hay singleton toàn cục:

```dart
final auth = AuthService();
await auth.configureAmplify(amplifyConfig);
final signedIn = await auth.isSignedIn();
runApp(InboxIQApp(authService: auth, initialSignedIn: signedIn));
```

`RestApiService` và `GmailOAuthService` nhận `AuthService`/`RestApiService` qua constructor để lấy token khi cần, không tự ý khởi tạo lại:

```dart
class RestApiService {
  final AuthService _auth;
  RestApiService(this._auth);
  ...
}
```

Lý do chọn cách này thay vì static: dễ kiểm soát vòng đời (gọi `dispose()` đúng lúc màn hình đóng, đặc biệt quan trọng với `WebSocketService` vì nó giữ một kết nối mạng sống), và tránh trạng thái toàn cục ẩn gây khó debug khi có nhiều màn hình cùng truy cập.

#### 6. Cấu hình platform Android — permission và deep link

Sửa `android/app/src/main/AndroidManifest.xml`, thêm quyền Internet ngay dưới thẻ `<manifest>`:

```xml
<uses-permission android:name="android.permission.INTERNET"/>
```

Thêm intent-filter cho deep link (dùng ở bước OAuth Gmail, xem mục 5.5.2), đặt trong `<activity android:name=".MainActivity">`, giữ nguyên intent-filter mặc định của Flutter:

```xml
<intent-filter>
    <action android:name="android.intent.action.VIEW"/>
    <category android:name="android.intent.category.DEFAULT"/>
    <category android:name="android.intent.category.BROWSABLE"/>
    <data android:scheme="inboxiq"/>
</intent-filter>
```

{{% notice warning %}}
**Không** thêm `android:autoVerify="true"` vào intent-filter này. Thuộc tính đó dùng cho App Links (yêu cầu domain thật với file xác minh `assetlinks.json` trên `https://`), không áp dụng cho custom scheme (`inboxiq://`). Có thêm vào, Android lint sẽ báo thiếu cấu hình host/scheme https — không cần thiết và gây nhiễu cảnh báo build.
{{% /notice %}}

![AndroidManifest.xml sau khi thêm permission và deep link intent-filter](/AWS-HUTECHintership-report/images/5-Workshop/5.5-Flutter-app/android-manifest-deeplink.jpg)

#### 7. Kết quả đạt được sau mục này

- Project Flutter khởi tạo đầy đủ dependencies, chạy được `flutter run` không lỗi
- Kiến trúc service tầng rõ ràng, instance-based, sẵn sàng để cắm logic xác thực và API ở các mục tiếp theo
- Android Manifest đã có quyền Internet và deep link scheme `inboxiq://` sẵn sàng nhận callback OAuth
