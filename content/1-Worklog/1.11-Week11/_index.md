---

title: "Week 11 Worklog"
date: 2026-06-29
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
-----------------------

### Week 11 Objectives: Two Bridges Connecting the Application to the Outside World — OAuth Deep Linking and WebSocket

During this week, I built the two most important “bridges” connecting the application to external systems. The first bridge connected the application to Google through the Gmail authorization flow, which opened in an external browser and returned to the application through a deep link. The second bridge connected the application to the backend in real time through a WebSocket client.

Both mechanisms shared the same challenge: they depended on components outside the direct control of the Flutter application, including browser behavior, the Android operating system, and network conditions. Therefore, the main difficulty was not simply writing the code but anticipating the different ways in which these external components could interrupt or reject the intended flow.

* Configure the `inboxiq://` deep link in `AndroidManifest.xml` and understand the difference between custom schemes and Android App Links.
* Implement `GmailOAuthService` to call the REST initialization endpoint, retrieve the Google consent URL, open an external browser, and listen for the deep-link callback.
* Coordinate with the backend team member to resolve the issue where the browser blocked automatic redirection back to the application.
* Implement `RestApiService` for calling JWT-authenticated REST endpoints.
* Implement `WebSocketService` to connect with a JWT in the query string, manage incoming message streams, and send a `whoami` request to retrieve the WebSocket connection ID.
* Complete the Home screen with Gmail connection and Gmail checking actions, a results list, and automatic WebSocket initialization when the screen opens.

---

### Tasks to Be Implemented During the Week

| Day       | Task Description                                                                                                                                                                                                                                                                                                                                                                                                                            | Start Date | Completion Date | Reference                                        |
| --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------- | --------------- | ------------------------------------------------ |
| Monday    | - Add the `<uses-permission android:name="android.permission.INTERNET" />` permission and the `inboxiq://` deep-link intent filter to `AndroidManifest.xml`. <br> - Study the difference between custom URL schemes and Android App Links. Determine that `android:autoVerify="true"` should not be used because it belongs to the App Links mechanism, which requires a real HTTPS domain and an `assetlinks.json` verification file.      | 29/06/2026 | 29/06/2026      | https://developer.android.com/training/app-links |
| Tuesday   | - Implement the instance-based `RestApiService`, receiving `AuthService` through its constructor. <br> - Implement `initGmailOAuth()` and `checkGmail(connectionId)`, automatically attaching the JWT to the authorization header of each request. <br> - Implement `GmailOAuthService` to call the initialization endpoint, retrieve the authorization URL, and open it using `url_launcher` with `LaunchMode.externalApplication`.        | 30/06/2026 | 30/06/2026      | https://pub.dev/packages/url_launcher            |
| Wednesday | - Implement `listenForCallback()` using the `app_links` package to listen for the `inboxiq://gmail-connected` URI and extract the email address from its query parameters. <br> - Test the first complete OAuth flow on a physical Android device and discover that the browser displayed the success page but did not automatically return to the application.                                                                             | 01/07/2026 | 01/07/2026      | https://pub.dev/packages/app_links               |
| Thursday  | - Coordinate with the backend team member to identify the cause: Chrome blocked automatic redirection to an unfamiliar custom scheme when no direct user gesture occurred immediately before the redirect. <br> - Update the backend callback page with both a meta refresh and a manual fallback link. <br> - Retest the complete flow and confirm that the application received the callback and updated the Gmail status to “Connected.” | 02/07/2026 | 02/07/2026      | Internal team documentation                      |
| Friday    | - Implement `WebSocketService` using `web_socket_channel`, connecting with `?token=<idToken>` in the WebSocket URL. <br> - Expose incoming messages through a broadcast stream and clear connection state when `onDone` or `onError` is triggered. <br> - Implement `requestConnectionId()` to send `{"action": "whoami"}`, wait for a `whoami-response`, apply a timeout, and return the connection ID.                                    | 03/07/2026 | 03/07/2026      | https://pub.dev/packages/web_socket_channel      |
| Saturday  | - Implement the `EmailSummary` model with a tolerant `fromJson` method that accepts missing or null fields and parses `createdAt` from either an epoch value or an ISO-formatted string. <br> - Implement the `SummaryCard` widget to display the email summary, category, priority, and suggested action, with visual indicators based on priority level.                                                                                  | 04/07/2026 | 04/07/2026      | https://docs.flutter.dev                         |
| Sunday    | - Complete the Home screen by initializing the WebSocket connection and sending the `whoami` request in `initState`. <br> - Add Gmail connection and Gmail checking buttons, a list of `SummaryCard` widgets, and a logout action. <br> - Test the workflow up to the Gmail checking request. WebSocket result pushing will be tested with the backend during the following week. <br> - Write the weekly report.                           | 05/07/2026 | 05/07/2026      | Internal InboxIQ project documentation           |

---

### Results Achieved in Week 11

#### 1. OAuth Deep Linking — The Most Difficult Client-Side Milestone Was Completed

The complete Gmail connection flow worked successfully on a physical Android device:

`Connect Gmail` → open the external browser → display the Google consent screen → grant permission → open the backend callback page → return to the application through `inboxiq://gmail-connected` → update the button status to `Gmail Connected`.

Although the process appeared straightforward from the user's perspective, it crossed several separate system boundaries:

* Flutter application
* External browser
* Google authorization server
* InboxIQ backend
* Android operating system
* Flutter application again

Each transition represented a possible point of failure.

The actual failure occurred when Chrome on Android blocked automatic navigation to an unfamiliar custom URL scheme that was neither HTTP nor HTTPS. This behavior was triggered because there was no immediate user interaction before the redirect. It is a browser security mechanism designed to prevent malicious websites from silently opening other applications.

As a result, the callback page displayed a successful connection message, but the user remained in the browser instead of returning to InboxIQ.

The solution required coordination with the backend team member. The callback page was updated to provide two redirection mechanisms:

* A `meta refresh` that attempted to redirect automatically after one second.
* A manual fallback link that allowed the user to return to the application by tapping it.

This approach ensured that users always had a path back to the application, even when the browser rejected automatic redirection.

Another correct decision made during the initial implementation was using `LaunchMode.externalApplication` instead of opening the OAuth flow inside an embedded WebView. Google restricts OAuth authentication in embedded WebViews for security reasons. Using the wrong launch mode could therefore cause the login request to be rejected directly on the Google authentication screen.

#### 2. Understanding Custom Schemes and App Links — Avoiding an Incorrect Configuration

Android Studio suggested adding:

`android:autoVerify="true"`

to the deep-link intent filter.

However, further investigation showed that this property belongs to the Android App Links mechanism. App Links use verified HTTPS domains and require the server to host an `assetlinks.json` file to prove the association between the domain and the Android application.

The InboxIQ project used a custom scheme:

`inboxiq://`

A custom scheme does not use domain verification and therefore does not require `android:autoVerify="true"`.

Adding this property would not improve the custom-scheme implementation and could create misleading configuration warnings.

The key lesson was that IDE suggestions are not always correct for the current technical context. Developers must understand the underlying mechanism before accepting an automated recommendation.

#### 3. WebSocket Client Using a “Who Am I?” Identification Mechanism

`WebSocketService` was implemented to establish a WebSocket connection with the Cognito JWT included in the query string:

`?token=<idToken>`

This approach was used because a Flutter mobile WebSocket client cannot always attach custom HTTP headers in the same way as a standard REST request.

After the connection was established, the client sent the following message:

```json
{
  "action": "whoami"
}
```

The backend returned a `whoami-response` containing the WebSocket `connectionId`.

This connection ID was required when the client called the Gmail checking REST endpoint. The backend worker used it to determine which active WebSocket connection should receive the Gmail summary results.

The `requestConnectionId()` method also included a timeout. Without a timeout, the application could remain indefinitely in a loading state if the server failed to return a response or if the connection was interrupted.

#### 4. A Tolerant Model for Imperfect Data

The `EmailSummary.fromJson` parser was intentionally designed to tolerate incomplete or slightly inconsistent data.

Missing or null fields did not cause the application to crash. The `createdAt` field could also be parsed from either:

* A numeric epoch timestamp.
* An ISO-formatted date-time string.

This decision was necessary because the data passed through a multi-stage processing pipeline:

`Gmail → OpenAI → DynamoDB → WebSocket → Flutter application`

Different stages were developed by different team members, and actual response formats could differ slightly from the initial specification.

A strict client-side parser could crash because of minor inconsistencies. A tolerant parser allowed the application to display all valid data while safely handling optional or unavailable fields.

However, tolerant parsing should not silently accept every malformed value. Critical fields and protocol-level properties still need validation so that genuine integration errors are not hidden.

---

### Week 11 Result Evaluation

* The complete OAuth deep-link flow worked successfully on a physical Android device.
* The highest-risk client-side integration milestone was completed.
* The application service layer was completed with `AuthService`, `RestApiService`, `GmailOAuthService`, and `WebSocketService`.
* All services followed the agreed instance-based architecture.
* The WebSocket client successfully connected with JWT authentication and retrieved its connection ID through the `whoami` protocol.
* The Home screen was completed with Gmail connection, Gmail checking, result display, and logout functions.
* The client application was ready for end-to-end integration testing with the backend.
* Two important system-boundary lessons were documented for the team: Chrome may block custom-scheme redirection, and Google OAuth should not run inside an embedded WebView.

---

### Challenges Encountered

* Chrome blocked the automatic custom-scheme redirect without displaying an explicit error message. The callback page simply remained open, requiring investigation and repeated testing to identify the cause.
* The complete deep-link flow required a physical Android device and a real browser. Emulator behavior differed from physical-device behavior at critical integration points.
* Debugging WebSocket communication was significantly more difficult than debugging REST requests because there was no simple browser-based interface for inspecting the full interaction.
* WebSocket debugging depended heavily on coordinated logging from both the Flutter client and the backend.
* The Gmail OAuth flow crossed several system boundaries, making it difficult to determine which component was responsible when the process failed.
* External browser and operating-system behavior could not be controlled entirely from Flutter code.

---

### Solutions and Best Practices Learned

* For workflows involving browsers or operating-system navigation, always provide a manual fallback path in addition to automatic redirection.
* Mobile OAuth flows must be tested on physical devices using the same default browsers that end users are likely to use.
* Use `LaunchMode.externalApplication` for Google OAuth instead of an embedded WebView.
* Understand the difference between custom URL schemes and verified App Links before modifying Android intent-filter settings.
* Design data models to tolerate optional or slightly inconsistent values when data passes through multiple independently developed processing stages.
* Do not make critical fields fully optional merely to prevent crashes. Protocol errors should still be logged and surfaced during development.
* Add a timeout to every network operation that waits for a response, even when the server is expected to respond immediately.
* Expose WebSocket messages through a broadcast stream when multiple application components may need to observe incoming events.
* Clear the connection state in both `onDone` and `onError` to avoid treating a terminated WebSocket channel as an active connection.
* Avoid printing JWTs, OAuth codes, Gmail data, or other sensitive values in application logs.
* Coordinate client and backend logging formats so that a single request can be traced across REST, WebSocket, and worker-processing stages.

---

### Direction for Week 12

* Conduct end-to-end integration testing with the backend team member on a physical Android device.
* Test the full workflow from pressing `Check Gmail` to receiving processed email summaries through a WebSocket push.
* Verify that the REST request contains the correct connection ID and that the backend pushes results to the corresponding WebSocket connection.
* Test timeout, network interruption, expired-token, malformed-message, and reconnection scenarios.
* Investigate integration issues that appear only after the client and backend components are connected.
* Improve the user interface after the main workflow becomes stable.
* Apply a Material 3 theme and redesign `SummaryCard`.
* Add clearer loading, success, empty, and error states for Gmail processing.
