---
title: "Cấu hình Google Cloud"
date: 2026-07-08
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---

#### 1. Tạo project và enable Gmail API

Truy cập [Google Cloud Console](https://console.cloud.google.com/), bấm **Select a project** trên thanh công cụ → **New Project** trong hộp thoại hiện ra.

- Project name: `InboxIQ`
- Parent resource: **No organization** (tài khoản cá nhân)

{{% notice note %}}
Google giới hạn số project trên mỗi tài khoản miễn phí (mặc định 12). Cảnh báo "You have N projects remaining in your quota" chỉ mang tính thông tin, không chặn việc tạo project.
{{% /notice %}}

![Tạo project InboxIQ](/AWS-HUTECHintership-report/images/5-Workshop/5.4-Gmail-oauth/create-project.jpg)

Sau khi project được tạo, bấm **Select Project** trong thông báo để chuyển console sang đúng project — nếu tài khoản có nhiều project, console rất dễ đứng nhầm ở project cũ khiến các bước sau (enable API, tạo credentials) rơi nhầm chỗ.

Tiếp theo vào **APIs & Services → Library**, tìm **Gmail API** và bấm **Enable**. Project mới mặc định không bật API nào; nếu bỏ qua bước này, mọi lời gọi Gmail API sau này sẽ trả lỗi `403 accessNotConfigured` rất khó truy vết.

![Gmail API đã enable](/AWS-HUTECHintership-report/images/5-Workshop/5.4-Gmail-oauth/Gmail-api-enabled.jpg)

#### 2. Cấu hình OAuth Consent Screen (Google Auth Platform)

{{% notice info %}}
Google đã gộp trang "OAuth consent screen" cũ vào giao diện mới tên **Google Auth Platform** với các mục Overview, Branding, Audience, Clients, Data Access. Các hướng dẫn cũ trên mạng có thể mô tả giao diện khác, nhưng bản chất các bước không đổi.
{{% /notice %}}

Vào **APIs & Services → OAuth consent screen** (chuyển hướng sang Google Auth Platform) → bấm **Get started**:

1. **App name**: `InboxIQ`, **User support email**: email của bạn
2. **Audience**: chọn **External** — lựa chọn Internal chỉ dành cho tổ chức Google Workspace; tài khoản cá nhân bắt buộc chọn External
3. **Contact Information**: email developer
4. Đồng ý điều khoản → **Create**

Sau khi tạo xong, cấu hình tiếp hai mục ở sidebar:

**Data Access** → **Add or Remove Scopes** → chọn đúng 2 scope:

| Scope | Mục đích |
|---|---|
| `.../auth/gmail.readonly` | Đọc email để tóm tắt (restricted scope) |
| `.../auth/userinfo.email` | Lấy địa chỉ Gmail của user để hiển thị |

Nguyên tắc least-privilege: chỉ xin quyền đọc, không xin quyền gửi/xóa/sửa. Càng ít scope, màn hình consent càng ít gây e ngại cho người dùng, và app càng dễ qua vòng verify của Google sau này.

![Scopes đã cấu hình](/AWS-HUTECHintership-report/images/5-Workshop/5.4-Gmail-oauth/oauth-scopes.jpg)

**Audience** → **Test users** → **Add users** → thêm email Google sẽ dùng để test.

{{% notice warning %}}
App đang ở chế độ **Testing** (chưa được Google verify), chỉ những email trong danh sách Test users mới cấp quyền được. Quên bước này sẽ gặp lỗi `access_denied` khi test luồng OAuth.
{{% /notice %}}

#### 3. Tạo OAuth Client ID

Vào mục **Clients** → **Create Client**:

- **Application type**: **Web application** — dù client cuối là app Flutter, bên nhận callback từ Google là API Gateway (một endpoint web)
- **Name**: `InboxIQ Backend Callback`
- **Authorized redirect URIs**: điền chính xác endpoint callback của REST API:

```
https://<api-id>.execute-api.us-east-1.amazonaws.com/prod/auth/gmail/callback
```

{{% notice tip %}}
Hai lưu ý từ thực tế: (1) đừng nhầm ô **Authorized JavaScript origins** — ô đó chỉ nhận domain gốc, điền URL có path sẽ báo "Invalid Origin"; redirect URI đầy đủ phải điền vào ô **Authorized redirect URIs**. (2) Endpoint callback chưa tồn tại tại thời điểm này cũng không sao — Google chỉ so khớp chuỗi URL, không kiểm tra URL có phản hồi hay không.
{{% /notice %}}

Sau khi bấm **Create**, Google hiển thị **Client ID** (dạng `xxxxx.apps.googleusercontent.com`) và **Client Secret** (dạng `GOCSPX-xxxxx`). Lưu ngay hai giá trị này — Client Secret không thể xem lại sau khi đóng hộp thoại.

![OAuth Client đã tạo](/AWS-HUTECHintership-report/images/5-Workshop/5.4-Gmail-oauth/oauth-client-created.jpg)

#### 4. Lưu credentials vào Secrets Manager

Tương tự OpenAI key ở phần backend, Google OAuth credentials được lưu tập trung trong Secrets Manager thay vì hard-code hoặc dùng biến môi trường. Trên PowerShell, ghi JSON ra file trước để tránh lỗi PowerShell nuốt dấu ngoặc kép:

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
Dùng `-Encoding ascii` thay vì `utf8` vì PowerShell mặc định chèn BOM vào đầu file UTF-8, khiến AWS CLI parse JSON thất bại. Xóa file tạm ngay sau khi tạo secret vì nó chứa Client Secret dạng plain text. Về chi phí: mỗi secret trong Secrets Manager tính phí khoảng $0.40/tháng.
{{% /notice %}}

Kết quả: Secrets Manager có 2 secret — `inboxiq/openai-api-key` và `inboxiq/google-oauth`.

![Hai secret trong Secrets Manager](/AWS-HUTECHintership-report/images/5-Workshop/5.4-Gmail-oauth/secrets-manager.jpg)
