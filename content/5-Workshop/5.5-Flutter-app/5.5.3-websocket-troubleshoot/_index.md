---
title: "End-to-End Testing and WebSocket Troubleshooting"
date: 2026-07-09
weight: 3
chapter: false
pre: "  5.5.3.  "
---

#### 1. Incident: New WebSocket Route Not Recognized by API Gateway

**Symptom:** After adding the `whoami` route (Lambda + Route + Integration) to `template.yaml` and receiving a successful confirmation from `sam deploy`, the Flutter app sent the request `{"action": "whoami"}` but consistently received the following response:

```json
{"message": "Forbidden", "connectionId": "...", "requestId": "..."}

```

Checking CloudWatch Logs for the `inboxiq-ws-whoami` Lambda revealed that the log group **did not even exist** — definitive proof that the request never reached the Lambda. The error lay within the API Gateway routing layer, not the code.

![Log group does not exist](/AWS-HUTECHintership-report/images/5-Workshop/5.5-Flutter-app/whoami-log-group-not-exist.jpg)

**Cause:** Verification inside the Console showed that the `WsWhoamiFunction`, `WhoamiRoute`, and `WhoamiIntegration` resources all had a `CREATE_COMPLETE` status in the CloudFormation stack — the route had indeed been created. However, navigating to **API Gateway → WebSocket API → Stages → prod → Deployment history**, the stage was still active on a deployment that **predated the route creation**. This meant that while CloudFormation successfully created the `AWS::ApiGatewayV2::Route` resource, it **did not automatically generate a new deployment for the `prod` stage**, despite the declared `DependsOn` relation between `AWS::ApiGatewayV2::Deployment` and that route. The `DependsOn` attribute only guarantees the creation order of resources; it does not force CloudFormation to interpret it as a "change" requiring a re-deployment.

**Solution:** Go to the API Gateway Console → select the WebSocket API → **Deploy API** → choose the `prod` stage → confirm. Once the new deployment was live, the route functioned immediately without any code modifications or CloudFormation re-deployments.

![New deployment after manual API deployment](/AWS-HUTECHintership-report/images/5-Workshop/5.5-Flutter-app/deployment-history-fixed.jpg)

**Lesson:** When managing `AWS::ApiGatewayV2` with SAM/CloudFormation, adding a new route does not automatically mean that route is "live" on the running stage. Always verify via the Deployment history after every route modification, or consider structuring the template so that the `Deployment` logical ID changes based on the route content (e.g., embedding a hash) to force CloudFormation to generate a new deployment every time.

#### 2. Incident: WebSocket "Assumed Connected" but Actually Still Handshaking

**Symptom:** Immediately after fixing the `whoami` route, the app still intermittently reported "WebSocket not ready" across certain test runs, displaying highly unstable behavior.

**Cause:** The original code utilized:

```dart
_channel = WebSocketChannel.connect(uri);
await Future.delayed(const Duration(milliseconds: 300));

```

`WebSocketChannel.connect()` returns immediately without waiting for the actual handshake to complete (which includes the duration for the Lambda Authorizer to validate the JWT — a process that can exceed 300ms during a cold start). Emitting messages prior to the completion of the handshake caused those messages to drop silently without throwing any errors.

**Solution:** Replace the fixed delay with the library's actual state signal:

```dart
_channel = WebSocketChannel.connect(uri);
try {
  await _channel!.ready;
} catch (e) {
  _channel = null;
  rethrow;
}

```

The `channel.ready` attribute is a `Future` that completes exactly when the handshake finishes successfully, or throws an error if it fails — completely eliminating the need for timing guesswork.

#### 3. Incident: Sequential Email Processing Fragile Under Heavy Loads

**Symptom:** The response time escalated linearly based on the number of emails; a single failing email (due to OpenAI timeout or rate limits) caused a loss of results for the entire batch.

**Cause:** The original code invoked GPT-4o-mini sequentially within a `for` loop, where each invocation carried a maximum timeout of 15 seconds. With a large volume of unread emails, the cumulative execution time could easily push against the Lambda Worker timeout threshold (300 seconds), thereby increasing the risk of the client-side WebSocket connection dropping before receiving any results.

**Solution:** Transition to parallel processing with partial fault tolerance:

```js
const summaries = (await Promise.allSettled(
  emails.map(async (email) => {
    const summary = await summarizeWithOpenAI(email, apiKey);
    await saveSummary(userId, email.id, summary);
    return { emailId: email.id, ...summary };
  })
)).filter(r => r.status === 'fulfilled').map(r => r.value);

```

Utilizing `Promise.allSettled` (instead of `Promise.all`) ensures that a single rejected element does not crash the entire batch — failing emails are safely bypassed while successful ones return their data normally. The hard cap of `maxResults = 10` remains intact within each `fetchUnreadEmails` call to prevent breaching Lambda timeouts or experiencing sudden spikes in OpenAI costs if the mailbox contains dozens of unread messages.

#### 4. Incident: Stale Cached `connectionId` at UI Layer Causing Silent `410 Gone` Errors

This proved to be the most elusive issue to uncover throughout the entire Flutter implementation, as absolutely no error messages were displayed to the user — the Check Gmail button simply spun indefinitely.

**Symptom:** The Lambda Worker logs indicated successful processing (fetched emails, successfully called OpenAI, token consumption recorded on the OpenAI dashboard), yet the final execution step to push results failed:

```
WARN WebSocket push failed for gTg3TG75DQAYKABzSA==: GoneException UnknownError
{"httpStatusCode":410, "requestId":"...", "attempts":1, "totalRetryDelay":0}

```

![Log GoneException 410 khi push WebSocket](/AWS-HUTECHintership-report/images/5-Workshop/5.5-Flutter-app/worker-gone-exception.jpg)

**Cause:** Cross-referencing timestamps between the two logs revealed the following sequence:

| Timestamp | Event |
| --- | --- |
| T | Lambda `ws-disconnect` logs that the connection has closed |
| T + 18s | User clicks "Check Gmail" |
| T + 27s | Lambda Worker attempts to push to that connectionId → `410 Gone` |

The connection had died **before** the user clicked the button, yet the app still sent out the outdated `connectionId`. The root cause resided at the UI layer: the Home screen stored the `connectionId` into its `State` only once during `initState()`. When the WebSocket dropped (due to network switching, the app temporarily leaving the foreground, or API Gateway closing an idle connection), the low-level service cleared its internal `connectionId` value back to `null`, but the `State` variable on the screen **was never synchronized**. As a result, an obsolete identifier was utilized to invoke the API.

**Solution:** Fetch the `connectionId` directly from the service (the live data source) at the exact moment the button is pressed, and trigger an automatic reconnection if a `null` value is detected, rather than trusting the cached value within the screen's `State`:

```dart
Future<void> _checkGmail() async {
  var connId = _ws.connectionId;
  if (connId == null) {
    setState(() => _connectingWs = true);
    try {
      await _ws.connect();
      connId = await _ws.requestConnectionId();
      if (mounted) setState(() => _connectionId = connId);
    } finally {
      if (mounted) setState(() => _connectingWs = false);
    }
  }
  if (connId == null) {
    ScaffoldMessenger.of(context).showSnackBar(
      const SnackBar(content: Text('WebSocket not ready')));
    return;
  }
  // proceed to call checkGmail with the fresh connId
}

```

**Lesson:** For any long-lived connections (WebSocket, gRPC stream...), the state displayed on the UI must always be re-verified at the original source immediately prior to use. One should never assume a cached value is valid, as connections can drop at any moment without throwing a reverse event to alert the calling layer.

#### 5. Summary Table of Other Errors Encountered and Solutions

| Error | Cause | Solution |
| --- | --- | --- |
| `sam deploy` reports "No changes to deploy" despite modifying `template.yaml` | Running `sam deploy` without running `sam build` first — SAM packages the old build folder, matching the hash of the deployed version. | Always run `sam build` prior to running `sam deploy` after making changes to templates or code. |
| Lambda Console unexpectedly appears to be missing a newly created function | The display list was capped or needed a manual refresh; the function did exist. | Cross-verify via CloudFormation → Resources instead of relying strictly on a single view of the Lambda Console. |
| App reports "WebSocket not ready" despite having connected previously | The old connection session had already closed (see section 4). | Read a fresh `connectionId` directly from the service, auto-reconnecting when necessary (see section 4). |

#### 6. Results

Following this implementation, the entire InboxIQ pipeline runs seamlessly on a real device, extending from the initial button press on the app to the real-time display of summarized results via WebSocket without any polling mechanisms:

```
Flutter App → Cognito Auth → REST API → SQS → Worker
  → Gmail API + GPT-4o-mini (Parallelized, partial failure tolerant)
  → DynamoDB
  → WebSocket push → Flutter App displays SummaryCard

```

![The summarized email list is displayed in the app](/AWS-HUTECHintership-report/images/5-Workshop/5.5-Flutter-app/summaries-displayed.jpg)

#### 7. Remaining Backlog Marked for Future Maintenance Task Items

* Integrate a `WidgetsBindingObserver` to auto-reconnect to the WebSocket when the app returns from the background — currently, the self-healing mechanism is only triggered when the user explicitly clicks the Check Gmail button again.
* Rotate the Google OAuth Client Secret (which was accidentally exposed once in a screenshot during a support communication thread).
* Develop a Sign Up screen to allow new users to register their own Cognito accounts, rather than relying on manual creations via the AWS Console — essential for scaling up to real end-users.
* Upgrade the Lambda runtime environments from Node.js 20.x to a version with longer-term support, ahead of the actual end-of-support dates (02/2027, 03/2027).