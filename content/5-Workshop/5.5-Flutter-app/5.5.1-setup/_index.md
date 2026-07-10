---
title: "Environment Setup and App Architecture"
date: 2026-07-09
weight: 1
chapter: false
pre: " <b> 5.5.1. </b> "
---

#### 1. Environment Verification

```powershell
flutter doctor

```

Must be completely clean before starting: Flutter SDK, Android toolchain, Android Studio with updated Flutter/Dart plugins. This project only targets Android (no iOS build), so any warnings related to Xcode/CocoaPods mentioned by `flutter doctor` can be safely ignored.

Specific environment used for InboxIQ: Flutter 3.38.2, IDE is **Android Studio** (not VS Code) for the entire Flutter part, while the backend (SAM/Lambda) is still opened with VS Code — both projects are placed in the same parent directory so that the AI assistant tool (Claude Code) can access both simultaneously:

```
D:\AWS\Project\AWSF\
├── inboxiq-backend\     ← SAM/Lambda, VS Code
├── inboxiq_app\          ← Flutter, Android Studio

```

{{% notice tip %}}
If the pub cache (usually on the `C:` drive) and the project are on different drives (here, the `D:` drive), Kotlin incremental compilation might break the cache midway, throwing a confusing "Daemon compilation failed" error. Solution: add `kotlin.incremental=false` to `android/gradle.properties`, then run `flutter clean` before rebuilding.
{{% /notice %}}

#### 2. Project Initialization

```powershell
flutter create inboxiq_app --org com.inboxiq
cd inboxiq_app

```

Select only the Android platform during creation, using Kotlin (the current default of `flutter create`).

#### 3. Declaring Dependencies

Modify `pubspec.yaml`:

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
![pubspec.yaml after adding all dependencies](/AWS-HUTECHintership-report/images/5-Workshop/5.5-Flutter-app/pubspec-dependencies.jpg)

#### 4. `lib/` Directory Structure

Organized by clear layers of responsibility, avoiding bundling logic into widgets:

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

![The lib/ directory structure in Android Studio](/AWS-HUTECHintership-report/images/5-Workshop/5.5-Flutter-app/lib-folder-structure.jpg)

#### 5. Architectural Decision: Instance-based, No Static Used

All services (`AuthService`, `RestApiService`, `WebSocketService`, `GmailOAuthService`) are designed to be **instance-based** — initialized once in `main.dart` and passed down to screens via constructors — instead of using static classes or global singletons:

```dart
final auth = AuthService();
await auth.configureAmplify(amplifyConfig);
final signedIn = await auth.isSignedIn();
runApp(InboxIQApp(authService: auth, initialSignedIn: signedIn));

```

`RestApiService` and `GmailOAuthService` receive `AuthService`/`RestApiService` via their constructors to fetch tokens when needed, rather than re-initializing them on their own:

```dart
class RestApiService {
  final AuthService _auth;
  RestApiService(this._auth);
  ...
}

```

Reason for choosing this over static: easy lifecycle control (calling `dispose()` precisely when the screen closes, which is especially critical for `WebSocketService` as it maintains a live network connection), and avoiding hidden global states that make debugging difficult when multiple screens access them simultaneously.

#### 6. Android Platform Configuration — Permissions and Deep Linking

Modify `android/app/src/main/AndroidManifest.xml`, adding the Internet permission directly beneath the `<manifest>` tag:

```xml
<uses-permission android:name="android.permission.INTERNET"/>

```

Add the intent-filter for deep linking (used in the Gmail OAuth step, see section 5.5.2), placed inside `<activity android:name=".MainActivity">`, while keeping Flutter's default intent-filter intact:

```xml
<intent-filter>
    <action android:name="android.intent.action.VIEW"/>
    <category android:name="android.intent.category.DEFAULT"/>
    <category android:name="android.intent.category.BROWSABLE"/>
    <data android:scheme="inboxiq"/>
</intent-filter>

```

{{% notice warning %}}
**Do not** add `android:autoVerify="true"` to this intent-filter. That attribute is used for App Links (requiring a real domain with an `assetlinks.json` verification file hosted on `https://`), which does not apply to a custom scheme (`inboxiq://`). Adding it will cause Android lint to report a missing https host/scheme configuration — which is unnecessary and creates noise in build warnings.
{{% /notice %}}

![AndroidManifest.xml after adding permissions and deep link intent-filter](/AWS-HUTECHintership-report/images/5-Workshop/5.5-Flutter-app/android-manifest-deeplink.jpg)

#### 7. Achieved Results After This Section

* The Flutter project is fully initialized with all dependencies, running `flutter run` without errors.
* A clear layered, instance-based service architecture, ready for wiring authentication logic and APIs in subsequent sections.
* The Android Manifest now includes Internet permissions and the `inboxiq://` deep link scheme, ready to receive OAuth callbacks.