---
title: "Cognito Authentication and Gmail Connection via Deep Link"
date: 2026-07-09
weight: 2
chapter: false
pre: " <b> 5.5.2. </b> "
---

#### 1. Amplify Configuration for Cognito

**File:** `lib/amplifyconfiguration.dart` — its content is a JSON string configuring the Cognito plugin:

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
The original playbook used `authenticationFlowType: "USER_SRP_AUTH"`. In the actual project, the App Client on Cognito **only has `USER_PASSWORD_AUTH` enabled** (verify this in Cognito Console → App client → Authentication flows) — using `USER_SRP_AUTH` by mistake will cause `signIn()` to always fail with an unsupported flow error. Always match the exact value enabled on the Console instead of blindly copying defaults from the documentation.
{{% /notice %}}

![Cognito App Client — Authentication flows are on USER_PASSWORD_AUTH](/AWS-HUTECHintership-report/images/5-Workshop/5.5-Flutter-app/cognito-auth-flow.jpg)

#### 2. AuthService — Wrapping All Amplify Auth Operations

**File:** `lib/services/auth_service.dart`

```dart
class AuthService {
  Future<void> configureAmplify(String config) async {
    if (Amplify.isConfigured) return;
    try {
      await Amplify.addPlugin(AmplifyAuthCognito());
      await Amplify.configure(config);
    } on AmplifyAlreadyConfiguredException {
      // already configured, skip
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

`getIdToken()` is the method called by `RestApiService` and `WebSocketService` whenever authentication is needed — always fetching the latest token from Amplify instead of caching it manually, which automatically leverages Amplify's built-in token refresh mechanism.

#### 3. Login Screen

**File:** `lib/screens/login_screen.dart` — receives `AuthService` via its constructor, calls `signIn()`, and navigates to `HomeScreen` upon success.

{{% notice tip %}}
On real devices, when the virtual keyboard pops up, it can obscure the login button and cause a "BOTTOM OVERFLOWED BY N PIXELS" layout error. Wrap the `Column` containing the input fields with a `SingleChildScrollView` so the content can scroll up when the keyboard appears, rather than being forced to compress.
{{% /notice %}}

A real-world bug encountered during login testing on a phone: the virtual keyboard automatically added a trailing space at the end of the password field (due to text suggestions/autocorrect features), causing `signIn()` to return "Incorrect username or password" even though the password was typed correctly. Solution: disable keyboard suggestions for the password field, or call `.trim()` on the value before submitting.

![The login screen is running on a real Samsung S23 Ultra device](/AWS-HUTECHintership-report/images/5-Workshop/5.5-Flutter-app/login-screen-device.jpg)

#### 4. Client-side Gmail OAuth Flow — REST Init + Browser Launch + Deep Link Callback

**File:** `lib/services/gmail_oauth_service.dart`

```dart
class GmailOAuthService {
  final RestApiService _api;
  GmailOAuthService(this._api);

  Future<void> connectGmail() async {
    final authUrl = await _api.initGmailOAuth(); // calls POST /auth/gmail/init
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

The full flow, aligning with the backend presented in section 5.4:

```
User clicks "Connect Gmail"
  → connectGmail() calls REST /auth/gmail/init (with JWT) → receives authUrl
  → opens authUrl via url_launcher (LaunchMode.externalApplication — mandatory to open
      in an external browser, do not use an internal WebView, as Google blocks OAuth inside WebViews)
  → user logs in + grants permissions on Google
  → Google redirects back to Lambda callback
  → Lambda callback returns an HTML page containing a deep link redirect: inboxiq://gmail-connected?email=...
  → app_links captures the URI, listenForCallback() emits the newly connected email
  → HomeScreen updates the UI: button switches to "Gmail Connected"

```

#### 5. Incident: Chrome Blocks Auto-redirect to Custom Schemes

**Symptom:** After granting permissions on Google, the callback returns the HTML page, but the app fails to open automatically — the user must manually click the link to return to the app.

**Cause:** Certain browsers (especially Chrome on Android) **block automatic navigation** to unknown schemes (non-`http`/`https`) if there is no direct user interaction (user gesture) immediately preceding it — this is a security mechanism against malware silently launching other apps.

**Solution:** Patch the callback HTML page (`src/oauth-callback/index.mjs`, inside the `callbackHandler` function) to provide both an auto-redirect and a fallback link:

```html
<meta http-equiv="refresh" content="1;url=inboxiq://gmail-connected?email=...">
<p>Redirecting back to the application...
  <a href="inboxiq://gmail-connected?email=...">Click here if you are not automatically redirected</a>
</p>

```

The `meta refresh` still attempts to auto-redirect after 1 second (succeeding on many browsers/devices), while the fallback link guarantees a manual escape route if the browser blocks the operation.

![Callback page with backup link: "Click here if it doesn't redirect automatically"](/AWS-HUTECHintership-report/images/5-Workshop/5.5-Flutter-app/callback-fallback-link.jpg)

#### 6. Achieved Results After This Section

* Cognito login operates stably on real devices, matching the `USER_PASSWORD_AUTH` flow configured on the actual App Client.
* The complete Gmail OAuth flow runs successfully from the real app: clicking the button → browser opens consent → permissions granted → automatically (or manually) returns to the app → UI updates the connected state.
* Verified on a Samsung S23 Ultra device via USB debugging — the hardest milestone of the entire Flutter section.

![On the home screen after the OAuth deep linking is complete, the "Gmail Connected" button will appear](/AWS-HUTECHintership-report/images/5-Workshop/5.5-Flutter-app/gmail-connected-home.jpg)