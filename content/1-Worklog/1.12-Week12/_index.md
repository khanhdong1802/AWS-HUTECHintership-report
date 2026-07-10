---

title: "Week 12 Worklog"
date: 2026-07-06
weight: 12
chapter: false
pre: " <b> 1.12. </b> "
-----------------------

### Week 12 Objectives: When the Two Halves of the System Meet — Client-Side Integration Debugging and UI Finalization

The client application had been completed, and the backend already contained all the required components. This week was the point at which the two halves of the system were integrated and tested end to end on a physical Android device.

The ideal scenario of “connecting everything and having it work immediately” did not happen. A series of integration issues emerged at the WebSocket layer, where the client-side and server-side states needed to remain synchronized in real time.

As the team member responsible for the client application, my work this week focused on supporting integration debugging, fixing issues originating from the application — including a state-management design flaw that I had introduced in the previous week — and finalizing the user interface for the submission version.

* Conduct end-to-end testing with the backend team member and support issue tracing through Flutter logs and user-interface behavior.
* Fix the WebSocket issue where the connection appeared to be ready before the handshake had actually completed by replacing a fixed delay with `channel.ready`.
* Fix the most serious client-side issue: `connectionId` was cached in the screen state and was not synchronized when the WebSocket connection was lost.
* Upgrade the interface using Material 3, redesign `SummaryCard`, and add a meaningful empty state.
* Complete the end-to-end screenshot set for the team report.

---

### Tasks to Be Implemented During the Week

| Day       | Task Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      | Start Date | Completion Date | Reference                                            |
| --------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------- | --------------- | ---------------------------------------------------- |
| Monday    | - Conduct the first end-to-end test with the backend team member on a physical Android device. <br> - Client-side behavior: after pressing `Check Gmail`, the application displayed “WebSocket is not ready.” The `WS received` log showed that the server returned `{"message": "Forbidden"}` for the `whoami` request. <br> - Provide the complete client-side logs to the backend team member for further investigation.                                                                                                                                                                                                                                           | 06/07/2026 | 06/07/2026      | Internal project documentation                       |
| Tuesday   | - While the backend team member investigated the unpublished server route, review the complete client-side `WebSocketService`. <br> - Identify a weakness in the connection mechanism: `WebSocketChannel.connect()` returns a channel object immediately, but this does not mean that the handshake has been completed. <br> - The existing code used `Future.delayed(const Duration(milliseconds: 300))`, which was unreliable when the Lambda Authorizer experienced a cold start.                                                                                                                                                                                  | 07/07/2026 | 07/07/2026      | https://pub.dev/documentation/web_socket_channel/    |
| Wednesday | - Replace the fixed delay with `await channel.ready`. This Future completes only when the WebSocket handshake succeeds or throws an exception when the connection fails. <br> - Add `try/catch` handling to clear the channel state when an error occurs. <br> - After the backend route was published, test again and confirm that the `whoami` request worked and the application received a valid `connectionId`.                                                                                                                                                                                                                                                  | 08/07/2026 | 08/07/2026      | https://pub.dev/documentation/web_socket_channel/    |
| Thursday  | - Identify a new issue: the `Check Gmail` request was sent successfully, but no result appeared in the application and no client-side exception was raised. <br> - Backend logs showed that the result push failed with `410 Gone`, indicating that the WebSocket connection no longer existed. <br> - Compare timestamps with the backend team member and determine that the connection had been lost before the user pressed the button, while the application was still sending the old `connectionId`.                                                                                                                                                            | 09/07/2026 | 09/07/2026      | Internal project documentation                       |
| Friday    | - Identify the client-side root cause: the Home screen stored `connectionId` in `State` only once during `initState`. When the WebSocket was disconnected, the service cleared its internal value, but the screen state was not updated. <br> - Modify `_checkGmail()` to retrieve the current `connectionId` directly from `WebSocketService` when the user presses the button. <br> - If the value is null, automatically reconnect, send another `whoami` request, and retrieve a new `connectionId` before calling the API. <br> - Test again and confirm that the complete end-to-end workflow worked and the `SummaryCard` results appeared in the application. | 10/07/2026 | 10/07/2026      | https://docs.flutter.dev/data-and-backend/state-mgmt |
| Saturday  | - Upgrade the interface using Material 3 and `ColorScheme.fromSeed`. <br> - Add rounded cards, subtle shadows, and a clearer hierarchy between primary and secondary actions. <br> - Add an empty state with an icon and guidance message instead of displaying only a plain text line. <br> - Redesign `SummaryCard` with priority color indicators, category-specific icons, and a lightly colored recommended-action section.                                                                                                                                                                                                                                      | 11/07/2026 | 11/07/2026      | https://m3.material.io                               |
| Sunday    | - Capture screenshots of the complete end-to-end workflow: Login → Home → Google consent → Gmail connected → checking Gmail → result list. <br> - Write the weekly report. <br> - Provide the screenshots and workflow description to the team member responsible for project documentation.                                                                                                                                                                                                                                                                                                                                                                          | 12/07/2026 | 12/07/2026      | Internal InboxIQ project documentation               |

---

### Results Achieved in Week 12

#### 1. Lesson Learned from `channel.ready` — Do Not Guess the Timing; Wait for the State Signal

The first issue originated from an incorrect assumption in the code I had implemented during the previous week.

`WebSocketChannel.connect()` returns a channel object almost immediately, but this does not mean that the WebSocket handshake has been completed. The actual connection process, including JWT validation by the server-side Lambda Authorizer, continues asynchronously.

When the Lambda Authorizer experiences a cold start, this process may take significantly longer than normal.

The previous code used a fixed delay:

```dart
await Future.delayed(
  const Duration(milliseconds: 300),
);
```

The 300-millisecond waiting period was only an assumption. It could work when the server was warm but fail when the server experienced a cold start. This created a timing-dependent issue that appeared inconsistently and was difficult to reproduce reliably.

The correct solution was to use:

```dart
await channel.ready;
```

`channel.ready` is a Future provided by the library. It completes only when the WebSocket handshake has actually succeeded. If the connection fails, the Future throws an exception that the application can handle explicitly.

The key lesson is that when a library provides an official state signal, that signal should be used instead of estimating readiness with a fixed delay. Any hard-coded delay is an assumption that may fail when system conditions change.

#### 2. The Cached `connectionId` Issue — The Most Important State-Management Lesson

This was the most serious client-side issue and the most valuable lesson I learned during the entire project.

The Home screen stored the `connectionId` in `State` only once when the screen was initialized:

```dart
@override
void initState() {
  super.initState();
  _initializeWebSocket();
}
```

After receiving the `connectionId`, the value was stored in a state variable and reused for subsequent `Check Gmail` requests.

However, a WebSocket is an external resource whose state can change at any time. The connection may be interrupted when:

* The user switches from Wi-Fi to mobile data.
* The application moves to the background.
* The device temporarily loses network access.
* The server closes the connection after an idle period.
* The operating system suspends the application process.

When the WebSocket disconnected, `WebSocketService` correctly cleared its internal `connectionId` value. However, the Home screen still retained the old copied value in its state.

As a result, the user interface continued to assume that a valid `connectionId` existed. When the user pressed `Check Gmail`, the application sent the stale identifier to the backend. The backend worker then attempted to push the result to a dead WebSocket connection and received:

`410 Gone`

The dangerous aspect of this issue was that the client did not raise a clear exception. The REST request could still succeed, and the interface could continue displaying a loading state, but the result would never return.

The issue was identified only after the backend team member and I compared timestamps for:

* The WebSocket disconnection.
* The Gmail checking request.
* The backend result push.

The solution was based on the following principle:

**Read the live value at the moment it is needed instead of trusting a previously stored copy.**

The `_checkGmail()` method was modified to retrieve the current `connectionId` directly from `WebSocketService`:

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

If the WebSocket had disconnected and `connectionId` was null, the application would:

1. Establish a new WebSocket connection.
2. Wait for the handshake to complete.
3. Send another `whoami` request.
4. Receive a new `connectionId`.
5. Call the Gmail checking API using the new identifier.

The broader lesson for real-time applications is that network connection state can change at any time. A copied value stored in the user-interface layer cannot be treated as a permanently reliable source of truth.

There are two appropriate approaches:

* Actively listen for connection-state changes and synchronize the user-interface state whenever the connection changes.
* Verify the actual connection state at the moment the value is needed.

The incorrect approach is to copy the state once and assume that it will remain valid throughout the entire screen lifecycle.

#### 3. Controlled Material 3 Interface Upgrade Before the Submission Deadline

After the main workflow became stable, I upgraded the interface using Material 3.

The main rule during this stage was:

**Only modify the presentation layer and do not change the service logic or state-management mechanisms that had just become stable.**

The improvements included:

* Using `ColorScheme.fromSeed` to create a consistent application color scheme.
* Redesigning action buttons with clear primary and secondary hierarchy.
* Adding rounded corners and subtle shadows to cards.
* Adding an empty state with an icon, title, and user guidance.
* Redesigning `SummaryCard` with colored indicators for priority levels.
* Displaying category-specific icons.
* Placing the recommended action inside a lightly colored section.
* Adjusting spacing between interface elements to improve readability and visual structure.

Limiting the changes to the presentation layer allowed the application to gain a more complete appearance without introducing regressions in the WebSocket connection and data-processing logic.

#### 4. Complete End-to-End Workflow on a Physical Android Device

By the end of the week, the complete application workflow had been tested successfully multiple times on a physical Android device:

`Login` → `Connect Gmail` → `Grant Google permission` → `Return to the application through the deep link` → `Press Check Gmail` → `Backend processes the emails` → `Results are pushed through WebSocket` → `SummaryCard list appears in the application`.

The workflow did not use polling and did not require users to refresh the screen manually. When the backend completed email processing, the result was pushed directly to the correct WebSocket connection for the device.

The completed screenshot set included:

* Login screen.
* Home screen before Gmail connection.
* Google consent screen.
* Gmail connected state.
* Gmail checking state.
* Email summary result list.
* Detailed `SummaryCard` components showing priority and recommended actions.

The screenshots and workflow description were delivered to the team member responsible for documentation for inclusion in the group report.

---

### Week 12 Result Evaluation

* The two client-side integration issues — incomplete handshake readiness and cached `connectionId` — were resolved by addressing their root causes rather than applying temporary patches.
* The WebSocket client now uses `channel.ready` to wait until the connection is genuinely available instead of relying on a fixed delay.
* The application verifies and restores the WebSocket connection before sending a Gmail checking request.
* The complete end-to-end workflow operated successfully on a physical Android device.
* Email summaries were pushed from the backend to the application in real time without polling.
* The interface was upgraded to Material 3 without introducing regressions into the service or state-management layers.
* The complete workflow screenshot set was finalized and delivered to the team.
* The primary client-side objectives of the InboxIQ project were completed.

---

### Challenges Encountered

* The cached `connectionId` issue failed silently. It produced no exception and no clear client-side error log. The issue was identified only by comparing client and backend logs.
* The fixed 300-millisecond delay created intermittent behavior depending on whether the Lambda Authorizer was cold or warm. Some tests succeeded while others failed, making it easy to incorrectly conclude that the issue had been resolved.
* Debugging required timestamp synchronization across Flutter logs, backend APIs, and WebSocket push events.
* Understanding the `410 Gone` response required recognizing that a successful REST request did not mean the WebSocket connection was still active.
* There was pressure to complete the interface close to the submission deadline while the integration logic had only recently become stable.
* User-interface changes at the final stage could have caused regressions if presentation, logic, and state modifications had not been strictly separated.

---

### Solutions and Best Practices Learned

* For external-resource states such as network connections, WebSockets, or authentication sessions, always verify the value at the moment of use instead of trusting a previously stored copy.
* Never use a fixed delay as a replacement for a state signal already provided by the library.
* Use `channel.ready` to determine when the WebSocket handshake has actually completed.
* When a WebSocket disconnects, clear all related state, including the channel, connection status, and `connectionId`.
* Before calling an API that depends on a WebSocket connection, verify that the connection is still active.
* Implement reconnection and retrieve a new `connectionId` before continuing the request.
* Integration issues must be debugged using data from both the client and backend. Each side inspecting only its own logs makes root-cause analysis significantly more difficult.
* Synchronize timestamps across logs to reconstruct the exact order of events.
* Distinguish between REST request success and WebSocket push success. These are separate stages and can fail independently.
* When modifying the interface near a deadline, strictly limit changes to the presentation layer and avoid altering stable services or state logic.
* Retest the complete main workflow after every interface change to detect regressions.
* Do not include JWTs, Gmail content, or other sensitive information in logs or screenshots used for reports.

---

### Post-Project Direction

* Add `WidgetsBindingObserver` to monitor the application lifecycle and automatically reconnect the WebSocket when the application returns from the background.
* The current recovery mechanism is triggered only when the user presses `Check Gmail`; a more proactive reconnection mechanism should be added.
* Fix the layout overflow on the Login screen when the keyboard appears by using `SingleChildScrollView` or an appropriate responsive layout.
* Build a Sign Up screen so that new users can create their own accounts instead of requiring manual account creation through the Cognito Console.
* Add email confirmation and confirmation-code resend functions.
* Implement clearer refresh-token and expired-session handling.
* Add a reconnection state and connection-loss notification to the user interface.
* Write unit tests for `AuthService`, `RestApiService`, `WebSocketService`, and JSON parsing models.
* Write integration tests for the Login flow, Gmail OAuth flow, and WebSocket result reception.
* Compile the lessons learned about long-lived connection state management into internal team documentation.
