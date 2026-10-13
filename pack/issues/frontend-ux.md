# Issues in area frontend-ux

id: issues:frontend-ux

### P006: Swagger thiếu cursor pagination → client sinh kiểu never

status: fixed; tier: A; date: 2026-07-13; confidence: high

- problem: CursorPageQueryDto và meta CursorPageMeta thiếu @ApiProperty/@ApiQuery nên openapi-typescript sinh query/meta thành never/object rỗng, FE phải ép kiểu tay.
- root cause: DTO nằm ở entry common-dtos dùng chung FE nên không kéo @nestjs/swagger vào.
- resolution: Thêm ApiCursorPageQuery()/ApiCursorPageMeta() trong common/decorators, gắn vào 7 controller, regenerate openapi/core-api.json và libs/api-client. Chỉ annotation, không đổi runtime.
- evidence: commit 800a9e8, pr #14

### P048: Guest thấy nút đăng bài/like/comment nhưng backend luôn 403

status: fixed; tier: A; date: 2026-08-14; confidence: high

- problem: Guest vẫn thấy nút tạo bài/comment/like ở Feed nhưng backend luôn trả 403; người dùng chỉ biết sau khi submit.
- root cause: UI không phản ánh domain rule cấm guest thao tác Feed.
- resolution: post-composer, like-button, comment-composer không hiện form cho guest và báo rõ lý do; thêm spec cho like-button/post-composer.
- evidence: codex-thread 019ffec1, commit a3f0f61

### P049: Thông báo Facebook gõ sai "ckhả dụng"

status: fixed; tier: A; date: 2026-08-14; confidence: high

- problem: Thông báo API Facebook sai chính tả: 'Đăng nhập Facebook ckhả dụng.'
- root cause: Lỗi gõ chuỗi trong capabilities.service.ts.
- resolution: Sửa thành 'chưa khả dụng' tại apps/core-api/src/app/capabilities.service.ts.
- evidence: codex-thread 019ffec1, commit a3f0f61

### P057: Màn matching PC: CTA tĩnh, không có trạng thái chọn

status: fixed; tier: A; date: 2026-08-14; confidence: high

- problem: Màn matching PC: hai CTA Soul/Voice là span/Link tĩnh, không chọn được, không đổi màu khi chọn như mobile; kích thước nút mobile không đồng nhất.
- root cause: CTA desktop (từ PR #29, 41bc055) là phần tử tĩnh không đọc query ?match=; mobile mới có aria-pressed/màu selected.
- resolution: Desktop và mobile dùng chung state match trong match-actions.tsx; CTA nối với form, có aria-current/màu active; hai CTA mobile h-13 min-h-13 w-full. Thêm match-actions.spec.tsx, commit 71f9e17.
- evidence: codex-thread 01a0015f, codex-thread 019ffebd, commit 41bc055, commit 71f9e17

### P058: Nhiều nút action thiếu cursor pointer

status: fixed; tier: A; date: 2026-08-14; confidence: high

- problem: Nhiều nút action không hiện con trỏ pointer khi hover.
- root cause: Native button và shared Button không đặt cursor:pointer.
- resolution: Thêm cursor:pointer ở apps/web/src/app/global.css và shared/ui/button.tsx.
- evidence: codex-thread 019ffebd, file apps/web/src/shared/ui/button.tsx

### P059: Test Feed đỏ do thiếu setup session

status: fixed; tier: A; date: 2026-08-14; confidence: high

- problem: Hai test Feed (like-button.spec, post-composer.spec) đỏ vì giao diện hiện trạng thái guest.
- root cause: Test mock /users/me nhưng quên bật session trong tokenStore nên hook không gọi API.
- resolution: Sửa setup test bật session authenticated; 6/6 PASS, không đổi business code.
- evidence: codex-thread 019fff81, file apps/web/src/features/feed/components/like-button.spec.tsx

### P067: Toast đỏ sai ngữ cảnh khi hồ sơ yêu cầu tặng quà

status: fixed; tier: A; date: 2026-08-19; confidence: medium

- problem: Nút Nhắn tin gọi mutation dù server báo requiresGift gây toast đỏ sai ngữ cảnh; Discovery còn modal trung gian và 2 nút Voice/Soul Match cũ.
- root cause: UI không dùng state messageAvailable/requiresGift của server; card Discovery mở DiscoveryDetailSheet thay vì đi thẳng profile.
- resolution: public-profile-view không gọi mở chat khi requiresGift (hiện khu tặng quà), bỏ 2 nút cũ; discovery-card thành Link tới /users/{id}, bỏ sheet; thêm regression test profile/discovery.
- evidence: codex-thread 01a01990, file apps/web/src/features/profile/components/public-profile-view.tsx

### P070: Inbox không hiện hội thoại trực tiếp

status: fixed; tier: A; date: 2026-08-20; confidence: medium

- problem: Bấm Nhắn tin từ profile báo đã có conversation nhưng trang Tin nhắn trống; chat với người chưa kết bạn không hiện trong inbox.
- root cause: /friends chỉ query bảng friendship (friend.service.ts) và friends-list chỉ hiện conversation có lastMessageAt, nên direct conversation chưa friend/chưa có tin bị ẩn.
- resolution: Inbox đọc mọi conversation (listForUser), hiển thị cả conversation chưa có message; canCall derive từ follow hai chiều; thêm test unit/integration. Calling gate 98 suites/973 tests PASS.
- evidence: codex-thread 01a01990, codex-thread 01a01990

### P085: Guest reload mất device token → Matching 401

status: open; tier: A; date: 2026-10-09; confidence: high

- problem: Guest reload trang rồi vào Matching: POST /matching/tickets trả 401; GET /support/tickets/me cũng có thể 401. SPA navigation không reload thì 201.
- root cause: guestDeviceToken chỉ lưu trong memory (token-store.ts), mất sau reload; matching.controller yêu cầu header X-Guest-Device-Token.
- resolution: Chỉ chẩn đoán: cần lưu/reissue guest token khi restore session và guard query support trước khi có phiên; chưa thấy commit sửa.
- evidence: codex-thread 01a11dea, file libs/api-client/src/lib/token-store.ts
