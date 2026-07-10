---

title: "Week 10 Worklog"
date: 2026-06-22
weight: 10
chapter: false
pre: " <b> 1.10. </b> "
-----------------------

### Week 10 Objectives: Building the Flutter Application Foundation and Cognito Authentication Layer

As part of the task allocation for the InboxIQ project, I was responsible for the entire client-side development — the Flutter mobile application that end users directly interact with. During this week, while the backend team member worked on the server-side infrastructure, my objective was to complete the initial application foundation, including a stable development environment, a well-organized project structure, and the Cognito authentication layer — the first gateway users must pass through before accessing any system features.

* Set up a complete Flutter development environment on Windows with Android Studio and ensure that `flutter doctor` reports no remaining warnings.
* Initialize the `inboxiq_app` project and declare all dependencies required for later stages, including Amplify, WebSocket, and deep linking.
* Design the `lib/` directory structure based on responsibility layers: config, services, models, screens, and widgets.
* Finalize the architectural decision to implement services using an instance-based approach, passing them through constructors instead of using static methods.
* Integrate Amplify Auth Cognito for configuration, user authentication, and JWT retrieval, synchronized with the User Pool created by the team member responsible for security.
* Complete the Login screen and navigation mechanism based on the user's authentication state.

---

### Tasks to Be Implemented During the Week

| Day       | Task Description                                                                                                                                                                                                                                                                                                                                                       | Start Date | Completion Date | Reference                                              |
| --------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------- | --------------- | ------------------------------------------------------ |
| Monday    | - Conduct a team meeting to finalize the system architecture and task allocation; take responsibility for the Flutter client application. <br> - Install or update the Flutter SDK and Android Studio with the Flutter and Dart plugins, run `flutter doctor`, and resolve all environment warnings.                                                                   | 22/06/2026 | 22/06/2026      | https://docs.flutter.dev/get-started                   |
| Tuesday   | - Initialize the project using `flutter create inboxiq_app --org com.inboxiq`, selecting Android as the target platform and Kotlin as the native language. <br> - Declare dependencies in `pubspec.yaml`: `amplify_flutter`, `amplify_auth_cognito`, `http`, `web_socket_channel`, `app_links`, `url_launcher`, `provider`, `shared_preferences`, and `intl`.          | 23/06/2026 | 23/06/2026      | https://pub.dev                                        |
| Wednesday | - Create the `lib/` directory structure: `config/`, `services/`, `models/`, `screens/`, and `widgets/`. <br> - Create `app_config.dart` to store API endpoints and Cognito identifiers received from the backend team, separating configuration values from application logic.                                                                                         | 24/06/2026 | 24/06/2026      | Internal team documentation                            |
| Thursday  | - Study the Amplify Flutter documentation, including the configuration process, Auth Cognito plugin, and supported authentication flows. <br> - Create `amplifyconfiguration.dart` and carefully compare `authenticationFlowType` with the actual App Client configuration on the Cognito Console instead of copying the default configuration from the documentation. | 25/06/2026 | 25/06/2026      | https://docs.amplify.aws/flutter/                      |
| Friday    | - Implement the instance-based `AuthService` class with the following methods: `configureAmplify`, `signIn`, `signOut`, `isSignedIn`, and `getIdToken`. <br> - Implement `main.dart` to initialize `AuthService` only once and use the authentication state to determine the application's initial screen.                                                             | 26/06/2026 | 26/06/2026      | https://docs.amplify.aws/flutter/build-a-backend/auth/ |
| Saturday  | - Implement the Login screen with email and password fields, call `signIn`, navigate to the Home screen after successful authentication, and display an error message when authentication fails. <br> - Test the login flow using a test account on an Android emulator.                                                                                               | 27/06/2026 | 27/06/2026      | https://docs.flutter.dev                               |
| Sunday    | - Test the login flow on a physical Android device using USB debugging and record issues that only appear on real devices, such as the keyboard automatically correcting or inserting characters. <br> - Write the weekly report and confirm the JWT format that will be used by the backend APIs in the following week.                                               | 28/06/2026 | 28/06/2026      | Internal InboxIQ project documentation                 |

---

### Results Achieved in Week 10

#### 1. Development Environment and Project Foundation Completed

The Flutter development environment was successfully configured without any remaining warnings from `flutter doctor`. The environment correctly recognized the Flutter SDK, Android toolchain, Android Studio, and all required plugins.

The `lib/` directory was organized into responsibility-based layers from the beginning. Services do not directly depend on the user interface, and screens do not make API requests directly but instead communicate through the appropriate service classes. This structure allows new features to be added in later weeks without rebuilding the entire project architecture.

One notable environment issue occurred when the Flutter build process occasionally failed with the following error:

`"Daemon compilation failed"`

The error message did not clearly indicate its cause. After investigation, I determined that Kotlin incremental compilation had encountered a cache problem because the Pub Cache was located on drive `C:`, while the Flutter project was stored on drive `D:`.

The issue was resolved by adding the following configuration to `android/gradle.properties`:

`kotlin.incremental=false`

After that, I ran `flutter clean` and rebuilt the application. Environment-related errors that are not directly caused by the source code often require the most troubleshooting time because the error messages do not clearly identify the actual source of the problem.

#### 2. Architectural Decision: Instance-Based Services Instead of Static Services

Many Flutter examples available online implement services using static methods or global singleton objects for convenient access. I chose a different approach: each service is implemented as a regular class, initialized once in `main.dart`, and passed to the relevant screens through constructors.

The benefits of this decision will become clearer in the following development stages. `WebSocketService` must maintain a persistent network connection and requires strict lifecycle management, including when to connect, disconnect, and dispose of resources.

Using hidden global state would make WebSocket connection tracking and debugging significantly more difficult. The instance-based approach provides clearer ownership, lifecycle control, and dependency management.

#### 3. Lesson Learned About `authenticationFlowType` — Do Not Copy Default Configuration from Documentation

The Amplify documentation examples use:

`USER_SRP_AUTH`

However, after comparing the client configuration with the actual Cognito App Client created by the team member responsible for security, I found that the enabled authentication flow was:

`USER_PASSWORD_AUTH`

If the value from the documentation had been used without verification, the `signIn()` method would have failed because the authentication flow configured on the client was not supported by the Cognito App Client.

This type of issue is difficult to troubleshoot because the returned error message does not clearly state that the client-side authentication flow is inconsistent with the server-side configuration.

The key lesson is that every client-side configuration value must be verified against the actual server-side configuration. Example values from documentation should not be treated as production-ready defaults.

#### 4. An Issue That Only Appeared on a Physical Device

The login flow worked correctly on the Android emulator but failed on a physical Android device with the following error:

`"Incorrect username or password"`

This occurred even though the username and password were entered correctly.

The root cause was the virtual keyboard on the physical device automatically correcting the input or inserting additional characters into the password field. To resolve the issue, autocorrect and text suggestions were disabled for the password field before submitting the credentials.

This demonstrated why testing on a physical device from the early stages of development is necessary. Some issues related to keyboards, operating systems, and device-specific behavior cannot be reproduced on an emulator.

---

### Week 10 Result Evaluation

* The application foundation was completed with a stable environment, a clear project structure, and all required dependencies for later development stages.
* The Cognito authentication flow worked reliably on both the Android emulator and a physical Android device.
* The instance-based architecture was agreed upon and consistently applied from the first service implementation.
* The login, logout, authentication-state checking, and JWT retrieval functions were completed.
* The Login screen and authentication-based navigation mechanism were completed.
* The JWT authentication mechanism was confirmed with the backend team. APIs developed in the following week will use the `idToken` retrieved through `AuthService.getIdToken()`.

---

### Challenges Encountered

* Kotlin build errors occurred because the Pub Cache and Flutter project were located on different drives. The build output did not clearly identify this environment-related issue, requiring additional investigation through community issue trackers.
* Amplify Flutter has undergone significant API changes between major versions, while many online code examples mix old and new package versions.
* The initial client-side `authenticationFlowType` configuration did not match the actual Cognito App Client configuration.
* On the physical Android device, the keyboard automatically corrected or inserted characters into the password field, causing an authentication error that was difficult to diagnose.

---

### Solutions and Best Practices Learned

* When a build error is difficult to understand and does not appear to be directly related to the source code, environmental factors such as paths, storage drives, caches, and tool versions should be checked before investigating the application logic.
* Code examples should always be verified against the exact library version used in `pubspec.yaml`. Official documentation corresponding to the installed version should be prioritized.
* All Cognito client configurations should be compared with the actual configuration on the AWS Console, particularly the Region, User Pool ID, App Client ID, and authentication flow.
* Services should be designed using an instance-based approach to provide clear dependency and object lifecycle management.
* Testing should be conducted on both an emulator and a physical device from the first implemented feature rather than waiting until the final stage of development.
* Autocorrect and text suggestions should be disabled for password fields. The password value should not automatically be processed with `.trim()` because this could change a valid password that intentionally contains leading or trailing spaces.

---

### Direction for the Following Week

* Build the Gmail connection flow on the Flutter client by calling the initialization REST API, opening the browser for user consent, and receiving the callback through the `inboxiq://` deep link.
* Implement `WebSocketService` to connect to the WebSocket API being developed by the backend team and handle JWT authentication during the connection establishment process.
* Manage the WebSocket lifecycle, including connect, disconnect, dispose, and reconnection when the connection is interrupted.
* Coordinate with the backend team member to finalize the bidirectional WebSocket message format, including messages such as `whoami` and `summary-ready`.
