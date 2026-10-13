# Issues in area matching

id: issues:matching

### P010: Bộ lọc vùng Discovery cho độ chính xác giả (LRN-001)

status: fixed; tier: B; date: 2026-07-13; confidence: low

- problem: Discovery W1 định lọc theo city-region nhưng không có đường ghi city thật, filter sẽ cho độ chính xác giả.
- root cause: Giả định thiết kế không khớp vòng đời dữ liệu.
- resolution: Bỏ region filtering khỏi W1; Nearby có mô hình location/settings riêng.
- evidence: doc docs/reference/lessons-registry.md, doc docs/reference/project-handoff.md

### P032: Lỗi boot production khi chạy profile local (Matching capability)

status: fixed; tier: A; date: 2026-08-12; confidence: low

- problem: Lỗi production boot khi chạy local deployment profile cho Matching capability boundary.
- root cause: Chưa ro chi tiết; review chỉ nói thiếu adapter S3 tường minh cho profile local.
- resolution: Sửa ở local deployment profile bằng S3 adapter explicit; verify PASS với runtime Docker smoke.
- evidence: doc docs/plans/2026-08-12-matching-capability-boundary-verify.md

### P034: Matching trả 400 (useDiamond) + nút CTA sai trạng thái

status: fixed; tier: A; date: 2026-08-14; confidence: high

- problem: Bấm ghép đôi trả 400 COMMON_VALIDATION_FAILED vì frontend gửi useDiamond:false.
- root cause: PR #64 (db82c67) cho FE gửi useDiamond:false; API runtime chạy bản cũ còn forbidNonWhitelisted nên coi field là input thừa.
- resolution: apps/web/src/features/matching/api.ts: chỉ gửi useDiamond khi true, false thì bỏ field; thêm test api.spec.ts và queue-status-panel.spec.tsx. Đã commit 71f9e17 và push.
- evidence: codex-thread 019ffebd, codex-thread 01a0015f, commit 71f9e17, commit db82c67

### P046: Guest bấm Matching → 401 vì thiếu X-Guest-Device-Token

status: fixed; tier: A; date: 2026-08-14; confidence: high

- problem: Guest bấm Matching trên web luôn lỗi 401 MATCHING_GUEST_DEVICE_TOKEN_REQUIRED dù guest login thành công.
- root cause: Backend bắt header X-Guest-Device-Token nhưng frontend chỉ lưu accessToken/csrfToken, không lưu và không gửi guestDeviceToken.
- resolution: Lưu guestDeviceToken trong memory ở token-store (libs/api-client) và client.ts tự gắn header cho Matching; thêm test token-store/client. Hard reload chưa giữ token (ghi nhận là hạn chế).
- evidence: codex-thread 019ffec1, commit a3f0f61, pr #66, commit 0dc42d5

### P090: Huỷ ván mini-game ghi đè ván đã resolved

status: fixed; tier: A; date: 2026-10-09; confidence: high

- problem: Huỷ ván mini-game có thể ghi đè ván đã resolved thành cancelled khi thua race.
- root cause: Cancel không phải conditional update (check-then-act).
- resolution: UPDATE ... WHERE status = waiting_moves; thua race trả 409 (đã có kết quả) hoặc trạng thái cancelled idempotent.
- evidence: commit d4f3e81, memory project-litmatch-service-map.md, commit d4f3e81, claude-session 1f780174

### P094: Giới hạn đã biết: advisory lock Palm/Movie Match, outbox chưa có consumer/retention

status: open; tier: B; date: 2026-10-09; confidence: medium

- problem: Khoá advisory toàn cục của Palm/Movie Match là giới hạn scale; outbox Economy chưa có consumer và retention.
- root cause: Thiết kế ban đầu chấp nhận khoá toàn cục; outbox relay tắt ở profile hosted-free nên chưa có consumer.
- resolution: Mới ghi tài liệu giới hạn đã biết, điều kiện đổi và quyết định cần chốt trước khi bật relay ở production; chưa đổi code.
- evidence: commit 67742ff, commit 67742ff
