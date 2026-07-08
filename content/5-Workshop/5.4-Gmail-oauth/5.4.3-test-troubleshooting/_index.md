---
title: "End-to-End Testing and Troubleshooting"
date: 2026-07-08
weight: 3
chapter: false
pre: " <b> 5.4.3. </b> "
---

#### 1. Test the OAuth Flow

Retrieve the ID Token of the test user from Cognito:

```powershell
aws cognito-idp initiate-auth `
  --auth-flow USER_PASSWORD_AUTH `
  --client-id <app-client-id> `
  --auth-parameters USERNAME=<test-user-email>,PASSWORD=<password> `
  --region us-east-1

```

Call the init endpoint to receive the consent URL (use `curl.exe` instead of `curl` — on PowerShell, `curl` is an alias for `Invoke-WebRequest` with a completely different syntax):

```powershell
curl.exe -X POST https://<api-id>[.execute-api.us-east-1.amazonaws.com/prod/auth/gmail/init](https://.execute-api.us-east-1.amazonaws.com/prod/auth/gmail/init) `
  -H "Authorization: Bearer <IdToken>"

```

The response returns `{"authUrl": "https://accounts.google.com/o/oauth2/v2/auth?..."}`. Open this URL in a browser, and log in with an email from the Test users list:

1. Warning screen "Google hasn't verified this app" — normal for an app in Testing mode, click **Continue**
2. Consent screen listing the requested permissions
3. After allowing, Google redirects to the callback and the page displays "✓ Gmail connection successful"

![Connect Gmail success](/AWS-HUTECHintership-report/images/5-Workshop/5.4-Gmail-oauth/gmail-connected.jpg)

Verify the saved token in DynamoDB (write the JSON key to a file to avoid quote escaping issues on PowerShell):

```powershell
@'
{"userId":{"S":"<user-sub>"}}
'@ | Out-File -FilePath D:\get-item-key.json -Encoding ascii

aws dynamodb get-item `
  --table-name inboxiq-gmail-connections `
  --key file://D:/get-item-key.json `
  --region us-east-1

```

A valid record must have `accessToken`, `refreshToken`, `gmailAddress`, `expiresAt`, and most importantly — the `scope` field must contain `gmail.readonly`.

![Record trong DynamoDB](/AWS-HUTECHintership-report/images/5-Workshop/5.4-Gmail-oauth/gmail-connection-item.jpg)

#### 2. Real-World Incident: Google Granular Permissions and 403 Error

This is the most notable incident in this section, as it demonstrates a new Google behavior that most OAuth documentation hasn't updated yet.

**Symptom:** the OAuth flow completes "successfully" — token received, record in DynamoDB — but when the Worker calls the Gmail API, it receives `403 Forbidden`. The job is retried by SQS every 5 minutes (matching the visibility timeout), Lambda metrics report `Errors = 0` but the queue's `Messages Deleted` remains 0 — a sign that the Worker caught the error internally and returned a batch failure instead of crashing.

**Cause:** Google applies a **granular permissions** mechanism — the consent screen allows users to check individual permissions. The user clicked "Continue" without checking the Gmail permission box, so Google still granted a token but only with the `userinfo.email openid` scope. The proof is right in the DynamoDB record: the `scope` field is missing `gmail.readonly`.

![Record missing gmail.readonly scope](/AWS-HUTECHintership-report/images/5-Workshop/5.4-Gmail-oauth/scope-missing-gmail.jpg)

**Resolution:** re-run the OAuth flow. Google recognizes the app already has partial permissions and displays an **incremental consent** screen ("wants additional access") asking only for the missing permission. After agreeing, the new record has the full scope and the Worker calls the Gmail API successfully.

**Lesson:** with Google OAuth, "consent completed" does not mean "sufficient permissions" — always check the `scope` field in the token response instead of trusting that the flow went through smoothly. A production app should check the scope right at the callback and proactively request re-consent if it is missing.

#### 3. Test the Complete Pipeline with Real Emails

Call the main flow:

```powershell
curl.exe -X POST https://<api-id>[.execute-api.us-east-1.amazonaws.com/prod/check-gmail](https://.execute-api.us-east-1.amazonaws.com/prod/check-gmail) `
  -H "Authorization: Bearer <IdToken>" `
  -H "Content-Type: application/json" `
  -d '{\"connectionId\": \"test-conn-123\", \"action\": \"check-gmail\"}'

```

The immediate response `{"message":"Processing started","jobId":"..."}` comes from the Producer; the actual processing happens asynchronously in the Worker. After about 15–20 seconds, query the results table:

```powershell
aws dynamodb query `
  --table-name inboxiq-email-summaries `
  --key-condition-expression "userId = :u" `
  --expression-attribute-values file://D:/query-values.json `
  --region us-east-1

```

Result: real emails in the mailbox are analyzed by GPT-4o-mini with a full `summary` (Vietnamese summary), `category`, `priority`, `action` (suggested action), and `expireAt` (TTL for DynamoDB to auto-cleanup old records).

![Email summary in DynamoDB](/AWS-HUTECHintership-report/images/5-Workshop/5.4-Gmail-oauth/email-summary-result.jpg)

{{% notice note %}}
Note regarding WebSocket push: the Worker log will show a "connection not found" warning because the `connectionId` in the test command is a dummy value — this is expected behavior; the real WebSocket connection will be established from the Flutter app in the next section.
{{% /notice %}}

#### 4. Other Encountered Errors and Resolutions

| Error | Cause | Resolution |
| --- | --- | --- |
| `Invalid Origin: URIs must not contain a path` when creating OAuth Client | Entered the redirect URI into the wrong **Authorized JavaScript origins** box | The full redirect URI (with path) must be entered into the **Authorized redirect URIs** box; the origins box can be left blank |
| `Cannot find module '/opt/nodejs/gmail-shared.mjs'` despite successful build | SAM `BuildMethod: nodejs20.x` automatically wraps an extra `nodejs/` layer, causing a nested `nodejs/nodejs/` | Point `ContentUri` directly to the `nodejs/` folder; delete `.aws-sam` and rebuild as the cache might hold the old artifact |
| Vietnamese text displays incorrectly ("Káº¿t ná»'i...") on the callback page | The `Content-Type: text/html` header is missing a charset, causing the browser to guess the wrong encoding | Change to `text/html; charset=utf-8` |
| `The incoming token has expired` when testing API | Cognito ID Token expired (~1 hour) | Re-run `initiate-auth` to get a new token |
| AWS CLI reports `'charmap' codec can't encode character` when printing results with Vietnamese | The packaged AWS CLI v2 on Windows cannot change the output encoding, even with `chcp 65001` | View the data via the DynamoDB Console (Explore table items) instead of the terminal |
| JSON parse error with `--key`, `--expression-attribute-values` parameters | PowerShell swallows quotes in inline JSON parameters | Write JSON to a file with `-Encoding ascii` and point to `file://` |

#### 5. Results

After this section, the entire InboxIQ backend operates perfectly with real data:

```
Cognito (authentication) → REST API → SQS → Worker
  → Gmail API (reads real emails, token auto-refreshes)
  → GPT-4o-mini (summarizes + categorizes + prioritizes)
  → DynamoDB (saves results with TTL)

```

The remainder of the project involves building the Flutter client to connect to this system — covered in the next section.
