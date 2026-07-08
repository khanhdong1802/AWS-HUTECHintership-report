---
title: "Google Cloud Configuration"
date: 2026-07-08
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---

#### 1. Create a project and enable the Gmail API

Access [Google Cloud Console](https://console.cloud.google.com/), click **Select a project** on the toolbar → **New Project** in the pop-up dialog.

- Project name: `InboxIQ`
- Parent resource: **No organization** (personal account)

{{% notice note %}}
Google limits the number of projects per free account (default is 12). The warning "You have N projects remaining in your quota" is for informational purposes only and does not prevent project creation.
{{% /notice %}}

![Create InboxIQ project](/images/5-Workshop/5.4-Gmail-oauth/create-project.jpg)

After the project is created, click **Select Project** in the notification to switch the console to the correct project — if the account has multiple projects, it is very easy for the console to mistakenly stay on the old project, causing the subsequent steps (enabling APIs, creating credentials) to be applied to the wrong place.

Next, go to **APIs & Services → Library**, search for **Gmail API** and click **Enable**. A new project does not have any APIs enabled by default; if you skip this step, all future Gmail API calls will return a `403 accessNotConfigured` error, which is very hard to trace.

![Gmail API enabled](/images/5-Workshop/5.4-Gmail-oauth/Gmail-api-enabled.jpg)

#### 2. Configure OAuth Consent Screen (Google Auth Platform)

{{% notice info %}}
Google has merged the old "OAuth consent screen" page into a new interface called the **Google Auth Platform** with sections for Overview, Branding, Audience, Clients, and Data Access. Older guides on the internet might describe a different interface, but the essence of the steps remains unchanged.
{{% /notice %}}

Go to **APIs & Services → OAuth consent screen** (redirects to Google Auth Platform) → click **Get started**:

1. **App name**: `InboxIQ`, **User support email**: your email
2. **Audience**: select **External** — the Internal option is only for Google Workspace organizations; personal accounts must select External.
3. **Contact Information**: developer email
4. Agree to the terms → **Create**

After creation, continue configuring two sections on the sidebar:

**Data Access** → **Add or Remove Scopes** → select exactly 2 scopes:

| Scope | Purpose |
|---|---|
| `.../auth/gmail.readonly` | Read emails for summarization (restricted scope) |
| `.../auth/userinfo.email` | Retrieve user's Gmail address for display |

Principle of least-privilege: only request read access, not send/delete/modify access. The fewer scopes you request, the less intimidating the consent screen is for users, and the easier it is for the app to pass Google's verification process later.

![Configured scopes](/images/5-Workshop/5.4-Gmail-oauth/oauth-scopes.jpg)

**Audience** → **Test users** → **Add users** → add the Google email that will be used for testing.

{{% notice warning %}}
The app is in **Testing** mode (not yet verified by Google), so only emails in the Test users list can grant access. Forgetting this step will result in an `access_denied` error when testing the OAuth flow.
{{% /notice %}}

#### 3. Create OAuth Client ID

Go to the **Clients** section → **Create Client**:

- **Application type**: **Web application** — even though the final client is a Flutter app, the receiver of the callback from Google is the API Gateway (a web endpoint).
- **Name**: `InboxIQ Backend Callback`
- **Authorized redirect URIs**: enter the exact REST API callback endpoint:

```
https://[.execute-api.us-east-1.amazonaws.com/prod/auth/gmail/callback](https://www.google.com/search?q=https://.execute-api.us-east-1.amazonaws.com/prod/auth/gmail/callback)
```
{{% notice tip %}}
Two practical notes: (1) Do not confuse this with the **Authorized JavaScript origins** box — that box only accepts the root domain, entering a URL with a path will report "Invalid Origin"; the full redirect URI must be entered in the **Authorized redirect URIs** box. (2) It does not matter if the callback endpoint does not exist at this time — Google only matches the URL string and does not check if the URL responds.
{{% /notice %}}

After clicking **Create**, Google displays the **Client ID** (format `xxxxx.apps.googleusercontent.com`) and **Client Secret** (format `GOCSPX-xxxxx`). Save these two values immediately — the Client Secret cannot be viewed again after closing the dialog.

![OAuth Client created](/images/5-Workshop/5.4-Gmail-oauth/oauth-client-created.jpg)

#### 4. Save credentials to Secrets Manager

Similar to the OpenAI key in the backend section, Google OAuth credentials are stored centrally in Secrets Manager instead of being hard-coded or using environment variables. In PowerShell, write the JSON to a file first to prevent PowerShell from swallowing the double quotes:

```powershell
@'
{
  "clientId": "xxxxx.apps.googleusercontent.com",
  "clientSecret": "GOCSPX-xxxxx"
}
'@ | Out-File -FilePath D:\google-oauth-secret.json -Encoding ascii

aws secretsmanager create-secret `
  --name "inboxiq/google-oauth" `
  --description "Google OAuth credentials for Gmail integration" `
  --secret-string file://D:/google-oauth-secret.json `
  --region us-east-1

Remove-Item D:\google-oauth-secret.json

```

{{% notice note %}}
Use `-Encoding ascii` instead of `utf8` because PowerShell automatically inserts a BOM at the beginning of a UTF-8 file by default, causing the AWS CLI to fail when parsing the JSON. Delete the temporary file immediately after creating the secret because it contains the Client Secret in plain text. Regarding costs: each secret in Secrets Manager costs about $0.40/month.
{{% /notice %}}

Result: Secrets Manager has 2 secrets — `inboxiq/openai-api-key` and `inboxiq/google-oauth`.

![Two secret in Secrets Manager](/images/5-Workshop/5.4-Gmail-oauth/secrets-manager.jpg)