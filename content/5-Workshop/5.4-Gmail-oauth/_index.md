---
title: "Gmail OAuth Integration"
date: 2026-07-08
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

#### Why a Separate Section for Gmail OAuth

Gmail OAuth is the most multi-step part of the project and is often overlooked because it is not on the main architecture diagram. However, without this part, the Lambda Worker will always fail because it lacks the Gmail access token to read the user's mailbox.

InboxIQ's complete OAuth flow:


```

User clicks "Connect Gmail" on the app
→ App calls REST /auth/gmail/init → receives consent URL
→ App opens consent URL in the browser
→ User logs in and grants permissions on Google
→ Google redirects to the callback URL with an authorization code
→ Lambda callback exchanges code for access_token + refresh_token
→ Lambda saves tokens to DynamoDB (inboxiq-gmail-connections table)
→ Callback page displays "Gmail connection successful"

```

The crucial point of the design: the `/auth/gmail/init` endpoint **requires** Cognito authentication (only logged-in users can initiate the connection), whereas the `/auth/gmail/callback` endpoint is **unauthenticated** — because the request comes from Google redirecting via the browser, without carrying a JWT. The user's identity is passed throughout the redirect cycle via the `state` parameter.

#### Content

{{% children /%}}

#### Results Achieved After This Section

- Google Cloud project with Gmail API and OAuth consent screen correctly configured with minimum scopes (`gmail.readonly`, `userinfo.email`)
- Two new Lambda functions (`inboxiq-oauth-init`, `inboxiq-oauth-callback`) and a shared Lambda Layer (`GmailSharedLayer`) for the refresh token logic
- End-to-end OAuth flow running with a real Gmail account, tokens are saved and automatically refreshed
- The entire pipeline Cognito → REST API → SQS → Worker → Gmail API → GPT-4o-mini → DynamoDB verified with real emails
