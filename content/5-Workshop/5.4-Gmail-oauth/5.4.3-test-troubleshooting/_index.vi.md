---
title: "Kiểm thử end-to-end và xử lý sự cố"
date: 2026-07-08
weight: 3
chapter: false
pre: " <b> 5.4.3. </b> "
---

#### 1. Test luồng OAuth

Lấy ID Token của user test từ Cognito:

```powershell
aws cognito-idp initiate-auth `
  --auth-flow USER_PASSWORD_AUTH `
  --client-id <app-client-id> `
  --auth-parameters USERNAME=<test-user-email>,PASSWORD=<password> `
  --region us-east-1
```

Gọi endpoint init để nhận consent URL (dùng `curl.exe` thay vì `curl` — trên PowerShell, `curl` là alias của `Invoke-WebRequest` với cú pháp khác hẳn):

```powershell
curl.exe -X POST https://<api-id>.execute-api.us-east-1.amazonaws.com/prod/auth/gmail/init `
  -H "Authorization: Bearer <IdToken>"
```

Response trả về `{"authUrl": "https://accounts.google.com/o/oauth2/v2/auth?..."}`. Mở URL này trong trình duyệt, đăng nhập bằng email trong danh sách Test users:

1. Màn hình cảnh báo "Google chưa xác minh ứng dụng này" — bình thường với app đang ở chế độ Testing, bấm **Tiếp tục**
2. Màn hình consent liệt kê các quyền được yêu cầu
3. Sau khi cho phép, Google redirect về callback và trang hiển thị "✓ Kết nối Gmail thành công"

![Kết nối Gmail thành công](/images/5-Workshop/5.4-Gmail-oauth/gmail-connected.jpg)

Xác minh token đã lưu trong DynamoDB (JSON key ghi ra file để tránh lỗi escape dấu ngoặc kép trên PowerShell):

```powershell
@'
{"userId":{"S":"<user-sub>"}}
'@ | Out-File -FilePath D:\get-item-key.json -Encoding ascii

aws dynamodb get-item `
  --table-name inboxiq-gmail-connections `
  --key file://D:/get-item-key.json `
  --region us-east-1
```

Record hợp lệ phải có `accessToken`, `refreshToken`, `gmailAddress`, `expiresAt`, và quan trọng nhất — field `scope` phải chứa `gmail.readonly`.

![Record trong DynamoDB](/images/5-Workshop/5.4-Gmail-oauth/gmail-connection-item.jpg)

#### 2. Sự cố thực tế: Google granular permissions và lỗi 403

Đây là sự cố đáng chú ý nhất của phần này, vì nó thể hiện một hành vi mới của Google mà đa số tài liệu OAuth chưa cập nhật.

**Hiện tượng:** luồng OAuth hoàn tất "thành công" — có token, có record trong DynamoDB — nhưng khi Worker gọi Gmail API thì nhận `403 Forbidden`. Job bị SQS retry mỗi 5 phút (đúng visibility timeout), metric Lambda báo `Errors = 0` nhưng `Messages Deleted` của queue vẫn bằng 0 — dấu hiệu Worker bắt lỗi bên trong và trả batch failure thay vì crash.

**Nguyên nhân:** Google áp dụng cơ chế **granular permissions** — màn hình consent cho phép user tick chọn từng quyền riêng lẻ. Người dùng bấm "Tiếp tục" mà không tick ô quyền Gmail, nên Google vẫn cấp token nhưng chỉ với scope `userinfo.email openid`. Bằng chứng nằm ngay trong record DynamoDB: field `scope` thiếu `gmail.readonly`.

![Record thiếu scope gmail.readonly](/images/5-Workshop/5.4-Gmail-oauth/scope-missing-gmail.jpg)

**Cách xử lý:** chạy lại luồng OAuth. Google nhận ra app đã có một phần quyền và hiển thị màn hình **incremental consent** ("muốn có thêm quyền truy cập") chỉ hỏi đúng quyền còn thiếu. Sau khi đồng ý, record mới có đủ scope và Worker gọi Gmail API thành công.

**Bài học:** với OAuth Google, "consent hoàn tất" không đồng nghĩa "đủ quyền" — luôn kiểm tra field `scope` trong token response thay vì tin vào việc luồng chạy trót lọt. Ứng dụng production nên kiểm tra scope ngay tại callback và chủ động yêu cầu re-consent khi thiếu.

#### 3. Test pipeline hoàn chỉnh với email thật

Gọi luồng chính:

```powershell
curl.exe -X POST https://<api-id>.execute-api.us-east-1.amazonaws.com/prod/check-gmail `
  -H "Authorization: Bearer <IdToken>" `
  -H "Content-Type: application/json" `
  -d '{\"connectionId\": \"test-conn-123\", \"action\": \"check-gmail\"}'
```

Response tức thời `{"message":"Processing started","jobId":"..."}` đến từ Producer; việc xử lý thật diễn ra bất đồng bộ ở Worker. Sau khoảng 15–20 giây, query bảng kết quả:

```powershell
aws dynamodb query `
  --table-name inboxiq-email-summaries `
  --key-condition-expression "userId = :u" `
  --expression-attribute-values file://D:/query-values.json `
  --region us-east-1
```

Kết quả: email thật trong hộp thư được GPT-4o-mini phân tích với đầy đủ `summary` (tóm tắt tiếng Việt), `category`, `priority`, `action` (gợi ý hành động), và `expireAt` (TTL để DynamoDB tự dọn record cũ).

![Email summary trong DynamoDB](/images/5-Workshop/5.4-Gmail-oauth/email-summary-result.jpg)

{{% notice note %}}
Lưu ý khi push WebSocket: log Worker sẽ có cảnh báo "connection not found" vì `connectionId` trong lệnh test là giá trị giả — đây là hành vi mong đợi, kết nối WebSocket thật sẽ được thiết lập từ app Flutter ở phần sau.
{{% /notice %}}

#### 4. Các lỗi khác đã gặp và cách xử lý

| Lỗi | Nguyên nhân | Cách xử lý |
|---|---|---|
| `Invalid Origin: URIs must not contain a path` khi tạo OAuth Client | Điền redirect URI vào nhầm ô **Authorized JavaScript origins** | Redirect URI đầy đủ (có path) phải điền vào ô **Authorized redirect URIs**; ô origins có thể để trống |
| `Cannot find module '/opt/nodejs/gmail-shared.mjs'` dù build thành công | SAM `BuildMethod: nodejs20.x` tự bọc thêm lớp `nodejs/`, gây lồng đôi `nodejs/nodejs/` | Trỏ `ContentUri` vào thẳng thư mục `nodejs/`; xóa `.aws-sam` rồi build lại vì cache có thể giữ artifact cũ |
| Chữ tiếng Việt hiển thị lỗi ("Káº¿t ná»'i...") ở trang callback | Header `Content-Type: text/html` thiếu charset, trình duyệt tự đoán bảng mã sai | Đổi thành `text/html; charset=utf-8` |
| `The incoming token has expired` khi test API | ID Token Cognito hết hạn (~1 giờ) | Chạy lại `initiate-auth` lấy token mới |
| AWS CLI báo `'charmap' codec can't encode character` khi in kết quả có tiếng Việt | AWS CLI v2 bản đóng gói trên Windows không đổi được bảng mã output, kể cả với `chcp 65001` | Xem dữ liệu qua DynamoDB Console (Explore table items) thay vì terminal |
| Lỗi parse JSON với tham số `--key`, `--expression-attribute-values` | PowerShell nuốt dấu ngoặc kép trong tham số JSON inline | Ghi JSON ra file với `-Encoding ascii` rồi trỏ `file://` |

#### 5. Kết quả

Sau mục này, toàn bộ backend InboxIQ hoạt động hoàn chỉnh với dữ liệu thật:

```
Cognito (xác thực) → REST API → SQS → Worker
  → Gmail API (đọc email thật, token tự refresh)
  → GPT-4o-mini (tóm tắt + phân loại + chấm ưu tiên)
  → DynamoDB (lưu kết quả kèm TTL)
```

Phần còn lại của dự án là xây dựng client Flutter kết nối vào hệ thống này — trình bày ở mục tiếp theo.
