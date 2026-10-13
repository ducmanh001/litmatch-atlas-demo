# Issues in area auth-security

id: issues:auth-security

### P008: CodeQL: log rõ thông tin nhạy cảm ở runner CI local

status: fixed; tier: A; date: 2026-07-13; confidence: high

- problem: scripts/ci/local.mjs in command arguments chứa giá trị nhạy cảm, CodeQL báo 'Clear-text logging of sensitive information'; test cũ còn đòi chuỗi thô.
- root cause: Runner log nguyên command arguments, không che dữ liệu nhạy cảm.
- resolution: Hai lần Copilot Autofix che log, rồi 966c92a đơn giản hoá redaction và sửa local.test.mjs.
- evidence: commit 97aae36, commit 5bcb175, commit 966c92a, pr #16

### P009: Cookie refresh token: 2 lỗi chỉ lộ khi test trình duyệt thật

status: fixed; tier: A; date: 2026-07-13; confidence: medium

- problem: Verify bằng browser thật khi chuyển refresh token sang httpOnly cookie phát hiện 2 lỗi mà unit test không bắt: createApiClient thiếu credentials:'include' nên browser bỏ Set-Cookie.
- root cause: Thiếu credentials:'include' mặc định trong createApiClient; lỗi thứ hai bị cắt trong gói bằng chứng.
- resolution: Sửa client mặc định credentials:'include' và lỗi còn lại, thêm Playwright E2E thật (apps/web/e2e) làm bằng chứng browser bắt buộc theo ADR 0003/0007.
- evidence: pr #26

### P018: JWT access token trùng nhau khi cấp trong cùng giây (LRN-010)

status: fixed; tier: A; date: 2026-07-18; confidence: high

- problem: Login rồi refresh ngay có thể ra access JWT giống hệt nhau, phạm hợp đồng rotation khi cùng một giây.
- root cause: iat/exp độ phân giải giây và payload không có ID duy nhất.
- resolution: Mỗi access token thêm jti ngẫu nhiên trong token.service.ts; thêm test cùng giây ở token.service.spec.ts; xoay refresh family giữ nguyên.
- evidence: doc docs/reference/lessons-registry.md, file apps/core-api/src/modules/auth/services/token.service.ts, doc docs/reference/project-handoff.md

### P028: Đăng nhập SMS "accepted" của admin còn bật ở production

status: fixed; tier: A; date: 2026-07-24; confidence: low

- problem: Cấu hình hosted-release còn 'accepted login sms' ở production cho admin.
- root cause: Chưa ro: chỉ có subject, diff 2 dòng ở hosted-release.yml.
- resolution: Gỡ cấu hình accepted login sms khỏi hosted-release.yml (#44).
- evidence: commit daaaa4f, pr #44

### P071: Cổng gọi điện có thể bypass sau khi block (bắt được khi tự review)

status: fixed; tier: A; date: 2026-08-20; confidence: medium

- problem: Self-review thấy block không tự tắt follow cũ, nên cặp đã block vẫn có thể gọi nếu chỉ kiểm tra follow hai chiều.
- root cause: Quyền gọi chỉ dựa trên hai bản ghi follow active, không kiểm tra block.
- resolution: Thêm canCall ở ProfileSocialService kiểm tra block + follow hai chiều tại POST join trong calling.service.ts; thêm unit/integration test (33 test PASS, gate Calling 973 PASS).
- evidence: codex-thread 01a01990, file apps/core-api/src/modules/calling/calling.service.ts

### P082: Báo động giả: Nearby 403 / Feed chặn guest là đúng chính sách

status: false-alarm; tier: C; date: 2026-10-09; confidence: high

- problem: Discovery Nearby trả 403 và Feed chặn guest đăng bài khi test bằng tài khoản khách.
- root cause: Đúng chính sách: chưa bật hiển thị vị trí; tài khoản guest bị giới hạn tính năng.
- resolution: Kết luận không phải lỗi; UI đã hướng dẫn bật vị trí. Đề xuất test bằng 2 user thật để phủ đủ tính năng.
- evidence: codex-thread 01a11dea

### P084: Báo động giả: throttle đăng nhập guest 5 lần/phút chặn retest

status: false-alarm; tier: C; date: 2026-10-09; confidence: medium

- problem: Retest sâu bị API guest login chặn tạm thời do rate limit 5 lần/phút từ IP test.
- root cause: Rate limit đăng nhập hoạt động đúng; test chạy quá nhiều lần login.
- resolution: Chờ hết cửa sổ rate limit rồi chạy lại, không kết luận từ lần retry lỗi.
- evidence: codex-thread 01a11dea

### P087: Bỏ qua kiểm bundleId Apple khi env rỗng

status: fixed; tier: A; date: 2026-10-09; confidence: high

- problem: Webhook Apple bỏ qua kiểm tra bundleId khi ECONOMY_APPLE_BUNDLE_ID rỗng ở chế độ store, nên app khác dùng chung chuỗi chứng chỉ Apple có thể qua.
- root cause: Env validation cho phép bundle id rỗng ở ECONOMY_IAP_VERIFIER=store.
- resolution: Env validation từ chối boot ở chế độ store nếu thiếu ECONOMY_APPLE_BUNDLE_ID; dev/disabled vẫn cho rỗng. Mở rộng sau rà soát (09/10): webhook dùng ECONOMY_APPLE_WEBHOOK_VERIFIER (mặc định store) chứ không phải ECONOMY_IAP_VERIFIER, nên bắt buộc giờ gồm IAP store HOẶC (webhook store và đã điền ECONOMY_APPLE_ROOT_CA_PEM). Docs ghi rõ kiểm receipt hiện chưa so bundle_id.
- evidence: commit 5a56d6a, memory project-litmatch-service-map.md, commit 5a56d6a, claude-session 1f780174

### P091: Xoay JWT_SECRET đăng xuất mọi người

status: fixed; tier: B; date: 2026-10-09; confidence: medium

- problem: Xoay JWT_SECRET sẽ đăng xuất toàn bộ người dùng vì không có khoá cũ để xác minh token đang sống.
- root cause: Chỉ hỗ trợ một khoá ký/xác minh.
- resolution: Thêm JWT_SECRET_PREVIOUS: ký bằng khoá mới, core-api và gateway chỉ thử khoá cũ khi chữ ký không khớp; quy trình xoay (gateway trước, rồi core-api) ở k8s/README.md. Sau rà soát (09/10): xoay theo 3 bước (phân phối khoá mới làm khoá phụ, đổi vai, gỡ khoá phụ) vì rollout cuốn chiếu làm pod cũ từ chối token do pod mới ký; env validation từ chối JWT_SECRET_PREVIOUS trùng AUTH_GUEST_DEVICE_TOKEN_SECRET (nếu trùng, guest-device token sống nhiều ngày sẽ verify được như access token).
- evidence: commit 6d1082f, commit 54c66ab
