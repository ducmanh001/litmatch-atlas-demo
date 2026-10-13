# Database tables owned by module safety

id: db:safety
module: module:safety
tables: blocks, reports
see: ../modules/safety.md

### table: blocks

Log block/unblock — append-only theo cặp (blocker, blocked). Trạng thái "đang block" hiện tại = dòng mới nhất theo cặp (docs/services/safety-service.md § 1) — không dùng bảng mutable để giữ toàn bộ lịch sử cho điều tra T&S, khác Wallet/Ledger vì ở đây không có bài toán tái tính tổng, 1 lookup có index đã đủ rẻ.

Entity: Block (apps/core-api/src/modules/safety/entities/block.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | uuid |  | gen_random_uuid() | PK |  |
| blocker_user_id | uuid |  |  | FK→users.id |  |
| blocked_user_id | uuid |  |  | FK→users.id |  |
| action | character varying(16) |  |  |  |  |
| created_at | timestamp with time zone |  | now() |  |  |

Constraints: check chk_blocks_no_self_block ((blocker_user_id <> blocked_user_id))

Indexes: idx_blocks_blocked_blocker_created (blocked_user_id, blocker_user_id, created_at); idx_blocks_pair_created (blocker_user_id, blocked_user_id, created_at)

Access: safety(owner) r3/w4 via raw-sql+repository [apps/core-api/src/modules/safety/safety.service.ts:202, apps/core-api/src/modules/safety/safety.service.ts:203]

### table: reports

Log report — append-only (docs/06: hành động nhạy cảm phải log audit riêng, không xoá được). `trustPenaltyApplied` ghi lại CHÍNH XÁC số điểm bị trừ do report này (0 nếu bị chặn bởi per-pair cooldown hoặc daily cap — docs/services/safety-service.md § 4), cho phép audit lại mà không cần suy luận ngược từ cấu hình hiện tại (config có thể đổi theo thời gian). `status` là phần DUY NHẤT được mutate trên bảng này (moderator resolve/dismiss) — mọi field khác bất biến từ lúc tạo, tách biệt với audit log riêng (`admin_audit_logs`) ghi lại ai/khi nào.

Entity: Report (apps/core-api/src/modules/safety/entities/report.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | uuid |  | gen_random_uuid() | PK |  |
| reporter_user_id | uuid |  |  | FK→users.id |  |
| target_user_id | uuid | null |  | FK→users.id | NULL khi `targetType=video` — mặc định 'user' giữ hành vi cũ 100% không đổi. |
| reason | character varying(32) |  |  |  |  |
| description | text | null |  |  |  |
| trust_penalty_applied | integer |  | 0 |  |  |
| created_at | timestamp with time zone |  | now() |  |  |
| status | character varying(16) |  | 'pending'::character varying |  |  |
| target_type | character varying(8) |  | 'user'::character varying |  |  |
| target_video_id | uuid | null |  |  | NULL khi `targetType=user`. KHÔNG có FK sang bảng `videos` — `short-video` sở hữu bảng đó, tự validate tồn tại trước khi gọi. |

Constraints: check chk_reports_no_self_report ((reporter_user_id <> target_user_id)); check chk_reports_target_matches_type (((((target_type)::text = 'user'::text) AND (target_user_id IS NOT NULL) AND (target_video_id IS NULL)) OR (((target_type)::text = 'video'::text) AND (target_v…; check chk_reports_target_type (((target_type)::text = ANY ((ARRAY['user'::character varying, 'video'::character varying])::text[])))

Indexes: idx_reports_reporter_target_created (reporter_user_id, target_user_id, created_at); idx_reports_status_created_at (status, created_at); idx_reports_target_created (target_user_id, created_at); idx_reports_target_video_created (target_video_id, created_at); UNIQUE uq_reports_video_reporter (reporter_user_id, target_video_id) WHERE ((target_type)::text = 'video'::text)

Access: safety(owner) r8/w5 via manager+manager.querybuilder+manager.save(instance)+querybuilder+raw-sql+repository [apps/core-api/src/modules/safety/safety.service.ts:94, apps/core-api/src/modules/safety/safety.service.ts:104]

