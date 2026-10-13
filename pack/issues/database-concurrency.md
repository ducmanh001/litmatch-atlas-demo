# Issues in area database-concurrency

id: issues:database-concurrency

### P011: Transaction Postgres bị aborted sau unique violation (LRN-002)

status: mitigated; tier: A; date: 2026-07-13; confidence: medium

- problem: Bắt unique violation rồi đọc lại trong cùng transaction PostgreSQL tường minh thì transaction đã aborted, mất đường phục hồi.
- root cause: PostgreSQL đánh dấu transaction aborted sau câu lệnh lỗi.
- resolution: Insert/catch/read nằm ngoài transaction tường minh (safety.service). Vẫn thiếu regression test; sau đó d506ca2 áp dụng cùng nguyên tắc cho short-video.
- evidence: doc docs/reference/lessons-registry.md, doc docs/reference/project-handoff.md

### P013: Job định kỳ chồng lấp khi tick chậm/reject (LRN-003)

status: fixed; tier: A; date: 2026-07-18; confidence: high

- problem: Tick định kỳ chậm hoặc reject có thể chồng lấp, gây unhandled error hoặc việc trùng trong process.
- root cause: Timer không có kỷ luật vòng đời/overlap dùng chung.
- resolution: ManagedInterval bắt lỗi ở ranh giới timer, nhả cờ running trong finally; có managed-interval.spec.ts; khoá DB/idempotency vẫn bảo vệ liên process.
- evidence: doc docs/reference/lessons-registry.md, file apps/core-api/src/common/scheduling/managed-interval.ts, doc docs/plans/2026-07-18-managed-interval-refactor-plan.md, doc docs/reference/project-handoff.md

### P033: Khe hở đồng thời ở feed / party-room / safety

status: fixed; tier: A; date: 2026-08-13; confidence: medium

- problem: Các luồng core (feed, party-room, safety) có khe hở đồng thời.
- root cause: Chi tiết chưa ro từ body; thay đổi tập trung vào party-room.service (164 dòng), feed.service, safety.service.
- resolution: Sửa 3 service, thêm integration/unit spec cho feed, party-room, safety, cập nhật docs/services/party-room-service.md; gộp qua #65.
- evidence: commit 53eef5d, commit c04b41e, pr #65

### P052: Sửa nhầm migration đã commit (guard bất biến chặn)

status: fixed; tier: B; date: 2026-08-14; confidence: high

- problem: pre-push fail ở agent:check: migration đã commit 1753100000000-avatar.ts bị sửa (guard migration bất biến).
- root cause: Chỉnh seed trực tiếp trong migration cũ thay vì đặt trong migration mới 1757300000000-avatar-public-assets.ts.
- resolution: Khôi phục migration cũ về bản đã commit, giữ migration mới; pnpm agent:check PASS. Guard hoạt động đúng, không tạo commit mới.
- evidence: codex-thread 019ffee6, file apps/core-api/src/database/migrations/1753100000000-avatar.ts

### P068: Migration gộp gây phụ thuộc chéo + gate kiến trúc đỏ

status: fixed; tier: B; date: 2026-08-19; confidence: medium

- problem: Migration gộp follow + gift context khiến suite Friend phụ thuộc bảng gift_events; gate kiến trúc báo file lỗi sai thư mục và Gift test import nội bộ Friend; notification service sai layout.
- root cause: Vi phạm boundary giữa domain/migration và quy ước thư mục services/errors của module.
- resolution: Tách migration thành profile-follow và profile-gift-context; chuyển errors/service vào thư mục hợp lệ, Gift test import qua public API; architecture test PASS (116 và 103 assertions).
- evidence: codex-thread 01a01908, codex-thread 01a01990
