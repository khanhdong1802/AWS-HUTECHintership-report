---
title: "Xác thực Cognito và kết nối Gmail qua Deep Link"
date: 2026-07-09
weight: 2
chapter: false
pre: " <b> 5.5.2. </b> "
---

#### 1. Cấu hình Amplify cho Cognito

**File:** `lib/amplifyconfiguration.dart` — nội dung là một chuỗi JSON cấu hình plugin Cognito:

```dart
const String amplifyConfig = '''{
  "UserAgent": "aws-amplify-cli/2.0",
  "Version": "1.0",
  "auth": {
    "plugins": {
      "awsCognitoAuthPlugin": {
        "CognitoUserPool": {
          "Default": {
            "PoolId": "<pool-id-that>",
            "AppClientId": "<app-client-id-that>",
            "Region": "us-east-1"
          }
        },
        "Auth": {
          "Default": {
            "authenticationFlowType": "USER_PASSWORD_AUTH",
            "usernameAttributes": ["EMAIL"],
            "signupAttributes": ["EMAIL"],
            "mfaConfiguration": "OFF",
            "verificationMechanisms": ["EMAIL"]
          }
        }
      }
    }
  }
}''';
```

{{% notice warning %}}
Playbook gốc dùng `authenticationFlowType: "USER_SRP_AUTH"`. Trong project thực tế, App Client trên Cognito **chỉ bật `USER_PASSWORD_AUTH`** (kiểm tra lại trong Cognito Console → App client → Authentication flows) — dùng nhầm `USER_SRP_AUTH` khiến `signIn()` luôn thất bại với lỗi flow không được hỗ trợ. Luôn đối chiếu đúng giá trị đã bật trên Console thay vì copy mặc định từ tài liệu.
{{% /notice %}}

![Cognito App Client — Authentication flows đang bật USER_PASSWORD_AUTH](/AWS-HUTECHintership-report/images/5-Workshop/5.5-Flutter-app/cognito-auth-flow.jpg)

#### 2. AuthService — bọc toàn bộ thao tác Amplify Auth

**File:** `lib/services/auth_service.dart`

```dart
class AuthService {
  Future<void> configureAmplify(String config) async {
    if (Amplify.isConfigured) return;
    try {
      await Amplify.addPlugin(AmplifyAuthCognito());
      await Amplify.configure(config);
    } on AmplifyAlreadyConfiguredException {
      // đã config, bỏ qua
    }
  }

  Future<SignInResult> signIn(String email, String password) =>
      Amplify.Auth.signIn(username: email, password: password);

  Future<void> signOut() => Amplify.Auth.signOut();

  Future<bool> isSignedIn() async {
    try {
      final session = await Amplify.Auth.fetchAuthSession();
      return session.isSignedIn;
    } catch (_) {
      return false;
    }
  }

  Future<String?> getIdToken() async {
    try {
      final session =
          await Amplify.Auth.fetchAuthSession() as CognitoAuthSession;
      return session.userPoolTokensResult.value.idToken.raw;
    } catch (_) {
      return null;
    }
  }
}
```

`getIdToken()` là hàm được `RestApiService` và `WebSocketService` gọi mỗi khi cần xác thực — luôn lấy token mới nhất từ Amplify thay vì cache thủ công, để tự động hưởng lợi từ cơ chế refresh token có sẵn của Amplify.

#### 3. Màn hình Login

**File:** `lib/screens/login_screen.dart` — nhận `AuthService` qua constructor, gọi `signIn()` rồi điều hướng sang `HomeScreen` khi thành công.

{{% notice tip %}}
Trên thiết bị thật, bàn phím ảo khi bật lên có thể che khuất nút đăng nhập và gây lỗi layout "BOTTOM OVERFLOWED BY N PIXELS". Bọc `Column` chứa các trường nhập liệu bằng `SingleChildScrollView` để nội dung có thể cuộn lên khi bàn phím xuất hiện, thay vì bị ép co lại.
{{% /notice %}}

Một lỗi thực tế gặp khi test đăng nhập trên điện thoại: bàn phím ảo tự động thêm khoảng trắng ở cuối trường mật khẩu (tính năng gợi ý/sửa lỗi chính tả), khiến `signIn()` báo "Incorrect username or password" dù mật khẩu gõ đúng. Cách xử lý: tắt gợi ý bàn phím cho trường mật khẩu, hoặc `.trim()` giá trị trước khi gửi.

![Màn Login chạy trên thiết bị Samsung S23 Ultra thật](/AWS-HUTECHintership-report/images/5-Workshop/5.5-Flutter-app/login-screen-device.jpg)

#### 4. Luồng OAuth Gmail phía client — REST init + mở trình duyệt + deep link callback

**File:** `lib/services/gmail_oauth_service.dart`

```dart
class GmailOAuthService {
  final RestApiService _api;
  GmailOAuthService(this._api);

  Future<void> connectGmail() async {
    final authUrl = await _api.initGmailOAuth(); // gọi POST /auth/gmail/init
    await launchUrl(Uri.parse(authUrl), mode: LaunchMode.externalApplication);
  }

  Stream<String> listenForCallback() {
    final appLinks = AppLinks();
    return appLinks.uriLinkStream
        .where((uri) => uri.scheme == 'inboxiq' && uri.host == 'gmail-connected')
        .map((uri) => uri.queryParameters['email'] ?? '');
  }
}
```

Luồng đầy đủ, khớp với phần backend đã trình bày ở mục 5.4:

```
User bấm "Kết nối Gmail"
  → connectGmail() gọi REST /auth/gmail/init (có JWT) → nhận authUrl
  → mở authUrl bằng url_launcher (LaunchMode.externalApplication — bắt buộc mở
     ở trình duyệt ngoài, không dùng WebView nội bộ, vì Google chặn OAuth trong WebView)
  → user đăng nhập + cấp quyền trên Google
  → Google redirect về Lambda callback
  → Lambda callback trả về trang HTML có deep link redirect inboxiq://gmail-connected?email=...
  → app_links bắt được URI, listenForCallback() phát ra email vừa kết nối
  → HomeScreen cập nhật UI: nút chuyển "Đã kết nối Gmail"
```

#### 5. Sự cố: Chrome chặn auto-redirect sang custom scheme

**Hiện tượng:** sau khi cấp quyền trên Google, callback trả về trang HTML nhưng app không tự mở lại — người dùng phải tự bấm link mới quay lại được app.

**Nguyên nhân:** một số trình duyệt (đặc biệt Chrome trên Android) **chặn tự động điều hướng** sang scheme lạ (không phải `http`/`https`) nếu không có tương tác trực tiếp của người dùng (user gesture) ngay trước đó — đây là cơ chế chống mã độc âm thầm mở app khác.

**Cách xử lý:** vá trang HTML callback (`src/oauth-callback/index.mjs`, hàm `callbackHandler`) để vừa có auto-redirect vừa có link dự phòng:

```html
<meta http-equiv="refresh" content="1;url=inboxiq://gmail-connected?email=...">
<p>Đang chuyển về ứng dụng...
  <a href="inboxiq://gmail-connected?email=...">Bấm vào đây nếu không tự chuyển</a>
</p>
```

`meta refresh` vẫn thử tự chuyển sau 1 giây (thành công trên nhiều trình duyệt/thiết bị), còn link dự phòng đảm bảo luôn có đường thoát thủ công nếu trình duyệt chặn.

![Trang callback với link dự phòng "Bấm vào đây nếu không tự chuyển"](/AWS-HUTECHintership-report/images/5-Workshop/5.5-Flutter-app/callback-fallback-link.jpg)

#### 6. Kết quả đạt được sau mục này

- Đăng nhập Cognito hoạt động ổn định trên thiết bị thật, đúng flow `USER_PASSWORD_AUTH` khớp cấu hình App Client thật
- Toàn bộ luồng OAuth Gmail chạy được từ app thật: bấm nút → trình duyệt mở consent → cấp quyền → tự động (hoặc bấm tay) quay lại app → UI cập nhật trạng thái đã kết nối
- Xác nhận trên thiết bị Samsung S23 Ultra qua USB debugging — cột mốc khó nhất của toàn bộ phần Flutter

![Home screen sau khi deep link OAuth hoàn tất, nút chuyển "Đã kết nối Gmail"](/AWS-HUTECHintership-report/images/5-Workshop/5.5-Flutter-app/gmail-connected-home.jpg)
