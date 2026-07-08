---
title: "OAuth Lambda and Lambda Layer"
date: 2026-07-07
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---

#### 1. Code Architecture

The OAuth part consists of three handlers and a shared Lambda Layer:

```text
src/
├── oauth-callback/
│   ├── index.mjs          # initHandler + callbackHandler
│   └── package.json
├── layers/
│   └── gmail-shared/
│       └── nodejs/
│           ├── gmail-shared.mjs   # getGoogleCredentials + refreshAccessToken
│           └── package.json
└── worker/
    └── index.mjs          # uses refreshAccessToken from Layer

```

The refresh token logic is extracted into a **Lambda Layer** instead of repeating the code in both Worker and OAuth Callback. Reason: SAM builds and zips each function separately based on `CodeUri`, so this function cannot `import` a file located in another function's directory — a Layer is the standard code-sharing mechanism between Lambdas.

#### 2. Init Handler — generating the consent URL

The `POST /auth/gmail/init` endpoint requires Cognito auth and returns the Google consent URL:

```javascript
export const initHandler = async (event) => {
  const userId = event.requestContext.authorizer.claims.sub;
  const { clientId } = await getGoogleCredentials();
  const redirectUri = `https://${event.requestContext.domainName}/${event.requestContext.stage}/auth/gmail/callback`;

  const scope = "[https://www.googleapis.com/auth/gmail.readonly](https://www.googleapis.com/auth/gmail.readonly) [https://www.googleapis.com/auth/userinfo.email](https://www.googleapis.com/auth/userinfo.email)";
  const state = Buffer.from(JSON.stringify({ userId })).toString("base64url");

  const authUrl = new URL("[https://accounts.google.com/o/oauth2/v2/auth](https://accounts.google.com/o/oauth2/v2/auth)");
  authUrl.searchParams.set("client_id", clientId);
  authUrl.searchParams.set("redirect_uri", redirectUri);
  authUrl.searchParams.set("response_type", "code");
  authUrl.searchParams.set("scope", scope);
  authUrl.searchParams.set("access_type", "offline");
  authUrl.searchParams.set("prompt", "consent");
  authUrl.searchParams.set("state", state);

  return {
    statusCode: 200,
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({ authUrl: authUrl.toString() })
  };
};

```

Three important details:

* **`state` carries the `userId` through the redirect cycle**: the callback request comes from Google (browser redirect) without a JWT — `state` is the only channel for the callback to know which user the token belongs to. Google returns the `state` exactly as it was without modification.
* **`access_type=offline`**: a mandatory condition for Google to grant a `refresh_token`; by default, Google only returns an `access_token` that lives for ~1 hour.
* **`prompt=consent`**: forces Google to display the consent screen again and grant a new `refresh_token` on every connection — without this parameter, from the second approval onwards, Google will no longer return a `refresh_token`.

{{% notice warning %}}
MVP limitation: `state` is only base64 encoded, without a signature — malicious actors could forge a `state` impersonating another userId. A production environment needs to sign the `state` using a JWT or temporarily store the state in DynamoDB with a TTL for verification. This is an item in the report's Limitations section.
{{% /notice %}}

#### 3. Callback Handler — exchanging code for token

The `GET /auth/gmail/callback` endpoint is public (Authorizer: NONE). Processing flow: verify `state` → exchange `code` for token via `https://oauth2.googleapis.com/token` → fetch Gmail address via userinfo endpoint → write to DynamoDB:

```javascript
// Google is only guaranteed to return a refresh_token on the first consent —
// if the response doesn't have one, keep the old refresh_token in DynamoDB
let refreshToken = tokens.refresh_token;
if (!refreshToken) {
  const existing = await ddb.send(new GetCommand({
    TableName: "inboxiq-gmail-connections",
    Key: { userId }
  }));
  refreshToken = existing.Item?.refreshToken;
}

await ddb.send(new PutCommand({
  TableName: "inboxiq-gmail-connections",
  Item: {
    userId,
    gmailAddress: userInfo.email,
    accessToken: tokens.access_token,
    refreshToken,
    expiresAt,
    scope: tokens.scope,
    connectedAt: new Date().toISOString()
  }
}));

```

The callback returns **HTML** instead of JSON because the viewer is the user's browser, not the app:

```javascript
function htmlResponse(statusCode, body) {
  return {
    statusCode,
    headers: { "Content-Type": "text/html; charset=utf-8" },
    body
  };
}

```

{{% notice tip %}}
`charset=utf-8` in the Content-Type is mandatory when HTML contains Vietnamese. Without this declaration, the browser guesses the wrong encoding (usually Latin-1), and accented characters render as garbled text like "Káº¿t ná»'i" — an actual issue encountered and fixed during implementation.
{{% /notice %}}

#### 4. Shared Lambda Layer

The `GmailSharedLayer` Layer contains `getGoogleCredentials()` (reads the secret, caching it in the global scope to save Secrets Manager calls between warm starts) and `refreshAccessToken(userId)` (exchanges the `refresh_token` for a new `access_token` and updates DynamoDB). The Worker imports from the Layer via an absolute path:

```javascript
import { refreshAccessToken } from "/opt/nodejs/gmail-shared.mjs";

```

Lambda mounts the Layer's content into `/opt` at runtime — `/opt/nodejs/...` is a mandatory convention, which is also why this import style doesn't work with `sam local invoke`.

Declaration in `template.yaml`:

```yaml
  GmailSharedLayer:
    Type: AWS::Serverless::LayerVersion
    Properties:
      LayerName: gmail-shared
      ContentUri: src/layers/gmail-shared/nodejs/
      CompatibleRuntimes:
        - nodejs20.x
    Metadata:
      BuildMethod: nodejs20.x

```

{{% notice warning %}}
Actual pitfall encountered: with `BuildMethod: nodejs20.x`, SAM **automatically wraps an extra `nodejs/` layer** when packaging the Layer. If `ContentUri` points to the parent directory (which already contains `nodejs/`), the build result is nested two layers deep `nodejs/nodejs/` — the Lambda runtime will report `Cannot find module '/opt/nodejs/gmail-shared.mjs'` despite a successful build. The fix: point `ContentUri` one level deeper directly into the `nodejs/` folder, completely delete `.aws-sam`, and rebuild (the SAM cache might hold the old artifact).
{{% /notice %}}

#### 5. Declaring the Two OAuth Functions in SAM

```yaml
  OAuthInitFunction:
    Type: AWS::Serverless::Function
    Properties:
      FunctionName: inboxiq-oauth-init
      CodeUri: src/oauth-callback/
      Handler: index.initHandler
      Timeout: 5
      Role: !Ref LambdaRoleArn
      Layers:
        - !Ref GmailSharedLayer
      Events:
        InitApi:
          Type: Api
          Properties:
            RestApiId: !Ref RestApi
            Path: /auth/gmail/init
            Method: POST
            Auth:
              Authorizer: CognitoAuth

  OAuthCallbackFunction:
    Type: AWS::Serverless::Function
    Properties:
      FunctionName: inboxiq-oauth-callback
      CodeUri: src/oauth-callback/
      Handler: index.callbackHandler
      Timeout: 10
      Role: !Ref LambdaRoleArn
      Layers:
        - !Ref GmailSharedLayer
      Events:
        CallbackApi:
          Type: Api
          Properties:
            RestApiId: !Ref RestApi
            Path: /auth/gmail/callback
            Method: GET
            Auth:
              Authorizer: NONE

```

The core difference between the two functions: init strictly requires `CognitoAuth` (only logged-in users can initiate the connection), while callback is set to `NONE` because Google cannot carry the user's JWT when redirecting.

Build and deploy:

```powershell
sam build
sam deploy

```

The changeset adds 3 new resources (`GmailSharedLayer`, the two OAuth functions) and modifies `WorkerFunction` (attaching the Layer) and `RestApi` (two new routes). SAM deleting the old `RestApiDeployment` and creating a new deployment is normal behavior whenever routes change.

![Deploy success](/AWS-HUTECHintership-report/images/5-Workshop/5.4-Gmail-oauth/deploy-success.jpg)