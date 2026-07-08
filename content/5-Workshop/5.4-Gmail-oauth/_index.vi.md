---
title: "Tích hợp OAuth Gmail"
date: 2026-07-08
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

#### Vì sao cần một mục riêng cho OAuth Gmail

OAuth Gmail là phần nhiều công đoạn nhất của dự án và thường bị bỏ qua vì không nằm trên diagram kiến trúc chính. Tuy nhiên nếu thiếu phần này, Lambda Worker sẽ luôn thất bại vì không có Gmail access token để đọc hộp thư của người dùng.

Luồng OAuth đầy đủ của InboxIQ:

```
User bấm "Connect Gmail" trên app
  → App gọi REST /auth/gmail/init → nhận về consent URL
  → App mở consent URL trong trình duyệt
  → User đăng nhập và cấp quyền trên Google
  → Google redirect về callback URL kèm authorization code
  → Lambda callback đổi code lấy access_token + refresh_token
  → Lambda lưu token vào DynamoDB (bảng inboxiq-gmail-connections)
  → Trang callback hiển thị "Kết nối Gmail thành công"
```

Điểm mấu chốt của thiết kế: endpoint `/auth/gmail/init` **bắt buộc** xác thực Cognito (chỉ user đã đăng nhập mới khởi tạo được kết nối), trong khi endpoint `/auth/gmail/callback` **không** xác thực — vì request tới từ Google redirect qua trình duyệt, không mang JWT. Danh tính người dùng được truyền xuyên suốt vòng redirect thông qua tham số `state`.

#### Nội dung

{{% children /%}}

#### Kết quả đạt được sau mục này

- Google Cloud project với Gmail API và OAuth consent screen được cấu hình đúng scope tối thiểu (`gmail.readonly`, `userinfo.email`)
- Hai Lambda function mới (`inboxiq-oauth-init`, `inboxiq-oauth-callback`) và một Lambda Layer dùng chung (`GmailSharedLayer`) cho logic refresh token
- Luồng OAuth chạy end-to-end với tài khoản Gmail thật, token được lưu và tự động refresh
- Toàn bộ pipeline Cognito → REST API → SQS → Worker → Gmail API → GPT-4o-mini → DynamoDB được kiểm chứng với email thật
