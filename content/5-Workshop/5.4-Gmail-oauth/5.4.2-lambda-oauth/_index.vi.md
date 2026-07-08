---
title: "Lambda OAuth và Lambda Layer"
date: 2026-07-07
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---

#### 1. Kiến trúc code

Phần OAuth gồm ba handler và một Lambda Layer dùng chung:

```
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
    └── index.mjs          # dùng refreshAccessToken từ Layer
```

Logic refresh token được tách vào **Lambda Layer** thay vì lặp code ở cả Worker lẫn OAuth Callback. Lý do: SAM build và zip từng function riêng theo `CodeUri`, nên function này không thể `import` file nằm trong thư mục của function khác — Layer là cơ chế chia sẻ code chính thống giữa các Lambda.

#### 2. Handler Init — tạo consent URL

Endpoint `POST /auth/gmail/init` yêu cầu Cognito auth, trả về URL consent của Google:

```javascript
export const initHandler = async (event) => {
  const userId = event.requestContext.authorizer.claims.sub;
  const { clientId } = await getGoogleCredentials();
  const redirectUri = `https://${event.requestContext.domainName}/${event.requestContext.stage}/auth/gmail/callback`;

  const scope = "https://www.googleapis.com/auth/gmail.readonly https://www.googleapis.com/auth/userinfo.email";
  const state = Buffer.from(JSON.stringify({ userId })).toString("base64url");

  const authUrl = new URL("https://accounts.google.com/o/oauth2/v2/auth");
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

Ba chi tiết quan trọng:

- **`state` mang `userId` xuyên vòng redirect**: request callback tới từ Google (trình duyệt redirect), không mang JWT — `state` là kênh duy nhất để callback biết token thuộc về user nào. Google trả nguyên `state` về không chỉnh sửa.
- **`access_type=offline`**: điều kiện bắt buộc để Google cấp `refresh_token`; mặc định Google chỉ trả `access_token` sống ~1 giờ.
- **`prompt=consent`**: ép Google hiện lại màn hình đồng ý và cấp `refresh_token` mới ở mọi lần connect — thiếu tham số này, từ lần approve thứ hai Google sẽ không trả `refresh_token` nữa.

{{% notice warning %}}
Giới hạn của MVP: `state` chỉ encode base64, không có chữ ký — kẻ xấu có thể tự tạo `state` giả mạo userId khác. Môi trường production cần ký `state` bằng JWT hoặc lưu state tạm vào DynamoDB kèm TTL để xác minh. Đây là một mục trong phần Limitations của báo cáo.
{{% /notice %}}

#### 3. Handler Callback — đổi code lấy token

Endpoint `GET /auth/gmail/callback` là public (Authorizer: NONE). Luồng xử lý: xác thực `state` → đổi `code` lấy token qua `https://oauth2.googleapis.com/token` → lấy địa chỉ Gmail qua userinfo endpoint → ghi DynamoDB:

```javascript
// Google chỉ chắc chắn trả refresh_token ở lần consent đầu —
// nếu response không có, giữ lại refresh_token cũ trong DynamoDB
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

Callback trả về **HTML** thay vì JSON vì người xem là trình duyệt của user, không phải app:

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
`charset=utf-8` trong Content-Type là bắt buộc khi HTML chứa tiếng Việt. Thiếu khai báo này, trình duyệt tự đoán bảng mã (thường là Latin-1) và chữ có dấu hiển thị thành ký tự lỗi kiểu "Káº¿t ná»'i" — một lỗi thực tế đã gặp và sửa trong quá trình triển khai.
{{% /notice %}}

#### 4. Lambda Layer dùng chung

Layer `GmailSharedLayer` chứa `getGoogleCredentials()` (đọc secret, cache ở global scope để tiết kiệm lời gọi Secrets Manager giữa các lần warm start) và `refreshAccessToken(userId)` (đổi `refresh_token` lấy `access_token` mới rồi cập nhật DynamoDB). Worker import từ Layer qua đường dẫn tuyệt đối:

```javascript
import { refreshAccessToken } from "/opt/nodejs/gmail-shared.mjs";
```

Lambda mount nội dung Layer vào `/opt` lúc runtime — `/opt/nodejs/...` là quy ước bắt buộc, và cũng vì vậy import kiểu này không chạy được với `sam local invoke`.

Khai báo trong `template.yaml`:

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
Bẫy thực tế đã gặp: với `BuildMethod: nodejs20.x`, SAM **tự bọc thêm một lớp `nodejs/`** khi đóng gói Layer. Nếu `ContentUri` trỏ vào thư mục cha (đã chứa sẵn `nodejs/`), kết quả build bị lồng hai lớp `nodejs/nodejs/` — Lambda runtime sẽ báo `Cannot find module '/opt/nodejs/gmail-shared.mjs'` dù build thành công. Cách sửa: trỏ `ContentUri` sâu thêm một cấp vào thẳng thư mục `nodejs/`, xóa sạch `.aws-sam` rồi build lại (SAM cache có thể giữ artifact cũ).
{{% /notice %}}

#### 5. Khai báo hai function OAuth trong SAM

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

Điểm khác biệt cốt lõi giữa hai function: init bắt buộc `CognitoAuth` (chỉ user đã đăng nhập mới khởi tạo kết nối), callback để `NONE` vì Google không thể mang JWT của user khi redirect.

Build và deploy:

```powershell
sam build
sam deploy
```

Changeset thêm 3 resource mới (`GmailSharedLayer`, hai function OAuth) và modify `WorkerFunction` (gắn Layer), `RestApi` (hai route mới). Việc SAM xóa `RestApiDeployment` cũ và tạo deployment mới là hành vi bình thường mỗi khi route thay đổi.

![Deploy thành công](/AWS-HUTECHintership-report/images/5-Workshop/5.4-Gmail-oauth/deploy-success.jpg)
