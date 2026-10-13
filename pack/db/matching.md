# Database tables owned by module matching

id: db:matching
module: module:matching
tables: guest_match_quotas, match_daily_quotas, match_invites, match_sessions, match_tickets
see: ../modules/matching.md

### table: guest_match_quotas

Entity: GuestMatchQuota (apps/core-api/src/modules/matching/entities/guest-match-quota.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| quota_date | date |  |  | PK |  |
| key_hash | character(64) |  |  | PK | HMAC(kind + identity); không lưu IP, fingerprint hoặc deviceId thô. |
| count | integer |  | 0 |  |  |
| updated_at | timestamp with time zone |  | now() |  |  |

Constraints: check guest_match_quotas_count_check ((count >= 0))

Access: matching(owner) r0/w3/lock1 via manager+manager.querybuilder+querybuilder [apps/core-api/src/modules/matching/services/guest-match-quota.service.ts:90, apps/core-api/src/modules/matching/services/guest-match-quota.service.ts:98]

### table: match_daily_quotas

Quota miễn phí của user đã đăng ký theo ngày UTC và loại match. Guest vẫn dùng GuestMatchQuotaService với ba identity HMAC chống farm.

Entity: MatchDailyQuota (apps/core-api/src/modules/matching/entities/match-daily-quota.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| user_id | uuid |  |  | PK FK→users.id |  |
| quota_date | date |  |  | PK |  |
| match_type | character varying(8) |  |  | PK |  |
| count | integer |  | 0 |  |  |
| updated_at | timestamp with time zone |  | now() |  |  |

Constraints: check match_daily_quotas_count_check ((count >= 0)); check match_daily_quotas_match_type_check (((match_type)::text = ANY ((ARRAY['soul'::character varying, 'voice'::character varying])::text[])))

Access: matching(owner) r0/w3/lock1 via manager+querybuilder [apps/core-api/src/modules/matching/services/guest-match-quota.service.ts:165, apps/core-api/src/modules/matching/services/guest-match-quota.service.ts:177]

### table: match_invites

CTA "mời Voice/Soul Match" (W4, docs/services/matching-service.md § Invite) — directed invite, KHÔNG phải friend-request flow mới. Accept tạo trực tiếp `MatchTicket`/`MatchSession` (bỏ qua hàng đợi shard), tái dùng các chốt an toàn của `tryPair` (`interactionPolicy.canPair`, invariant 1-user-1-queue qua `uq_match_tickets_active_user`) — không check gender preference vì invitee đồng ý trực tiếp đúng người đã xem profile — từ lúc đó luồng y hệt auto-match, không có state/logic riêng ở Soul Match/Calling. Unique 1 invite PENDING/cặp (`uq_match_invites_pending_pair`) — không phải cơ chế rate-limit chính (đó là Redis, xem `matching.constants.ts`), chỉ chặn thêm trường hợp double-submit.

Entity: MatchInvite (apps/core-api/src/modules/matching/entities/match-invite.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | uuid |  | gen_random_uuid() | PK |  |
| inviter_user_id | uuid |  |  | FK→users.id |  |
| invitee_user_id | uuid |  |  | FK→users.id |  |
| match_type | character varying(8) |  |  |  |  |
| status | character varying(16) |  | 'pending'::character varying |  |  |
| expires_at | timestamp with time zone |  |  |  |  |
| responded_at | timestamp with time zone | null |  |  |  |
| session_id | uuid | null |  |  | Set khi accept thành công — trỏ tới `MatchSession` tạo ra từ invite này. |
| created_at | timestamp with time zone |  | now() |  |  |
| updated_at | timestamp with time zone |  | now() |  |  |

Constraints: check chk_match_invites_not_self ((inviter_user_id <> invitee_user_id)); check match_invites_match_type_check (((match_type)::text = ANY ((ARRAY['soul'::character varying, 'voice'::character varying])::text[]))); check match_invites_status_check (((status)::text = ANY ((ARRAY['pending'::character varying, 'accepted'::character varying, 'declined'::character varying, 'expired'::character varying, 'cance…

Indexes: idx_match_invites_invitee_status (invitee_user_id, status); idx_match_invites_inviter (inviter_user_id); UNIQUE uq_match_invites_pending_pair (inviter_user_id, invitee_user_id) WHERE ((status)::text = 'pending'::text)

Access: matching(owner) r4/w7/lock1 via manager+manager(lock option)+manager.save(instance)+querybuilder+raw-sql+repository [apps/core-api/src/modules/matching/jobs/invite-sweeper.service.ts:64, apps/core-api/src/modules/matching/services/invite.service.ts:131]

### table: match_sessions

Flow mới tạo session đã confirmed ngay khi matcher tìm được cặp. `PendingConfirm` được giữ để sweeper/confirm endpoint hoàn tất an toàn các session sinh ra từ phiên bản cũ.

Entity: MatchSession (apps/core-api/src/modules/matching/entities/match-session.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | uuid |  | gen_random_uuid() | PK |  |
| match_type | character varying(8) |  |  |  |  |
| user_a_id | uuid |  |  | FK→users.id |  |
| user_b_id | uuid |  |  | FK→users.id |  |
| ticket_a_id | uuid |  |  | FK→match_tickets.id |  |
| ticket_b_id | uuid |  |  | FK→match_tickets.id |  |
| status | character varying(16) |  | 'pending_confirm'::character varying |  |  |
| confirmed_a_at | timestamp with time zone | null |  |  |  |
| confirmed_b_at | timestamp with time zone | null |  |  |  |
| ended_at | timestamp with time zone | null |  |  |  |
| created_at | timestamp with time zone |  | now() |  |  |
| updated_at | timestamp with time zone |  | now() |  |  |

Constraints: check chk_match_sessions_distinct_users ((user_a_id <> user_b_id)); check match_sessions_match_type_check (((match_type)::text = ANY ((ARRAY['soul'::character varying, 'voice'::character varying])::text[]))); check match_sessions_status_check (((status)::text = ANY ((ARRAY['pending_confirm'::character varying, 'confirmed'::character varying, 'ended'::character varying, 'expired'::character varying])…

Indexes: idx_match_sessions_status_created (status, created_at)

Access: matching(owner) r9/w9/lock5 via manager+manager(lock option)+manager.save(instance)+querybuilder+repository [apps/core-api/src/modules/matching/jobs/matcher-worker.service.ts:281, apps/core-api/src/modules/matching/jobs/ticket-sweeper.service.ts:146]; soul-match(OTHER MODULE) r2/w0/lock2 via manager+manager(lock option) [apps/core-api/src/modules/soul-match/soul-match.service.ts:163, apps/core-api/src/modules/soul-match/soul-match.service.ts:311]

### table: match_tickets

1 yêu cầu ghép cặp (docs/services/matching-service.md § 5). - Nguồn sự thật về trạng thái là Postgres — Redis sorted set chỉ là queue store dẫn xuất, matcher luôn verify lại DB trước khi ghép (docs/10 § 10.0.C). - 1 user chỉ có 1 ticket `queued`/`matched` tại 1 thời điểm — chặn ở DB bằng partial unique index `uq_match_tickets_active_user` (xem migration 1752200000000), không chỉ ở code. - `idempotencyKey` unique ở DB (docs/05 § 5.10): client retry POST /matching/tickets với cùng key nhận lại đúng ticket cũ, không tạo ticket thứ 2.

Entity: MatchTicket (apps/core-api/src/modules/matching/entities/match-ticket.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | uuid |  | gen_random_uuid() | PK |  |
| user_id | uuid |  |  | FK→users.id |  |
| match_type | character varying(8) |  |  |  |  |
| region | character varying(16) |  |  |  | Server tự lấy từ profile user (không tin client — docs/10 § 10.0.B); user chưa set region → 'GLOBAL'. |
| age_band | integer |  |  |  | floor(tuổi / MATCHING_AGE_BAND_SIZE); user chưa khai sinh nhật → -1 (band "chưa rõ tuổi"). |
| status | character varying(16) |  | 'queued'::character varying |  |  |
| enqueued_at | timestamp with time zone |  |  |  | Mốc vào hàng đợi — score Redis = enqueuedAtMs + trustPenaltyMs - priorityBoostMs. Requeue sau confirm-timeout tạo ticket MỚI với enqueuedAt mới. |
| priority_boost_ms | integer |  | 0 |  | Tổng boost speed-up đã mua (ms), cộng dồn trong DB rồi set score Redis TUYỆT ĐỐI (ZADD XX) thay vì ZINCRBY — nhờ đó retry/replay cùng idempotency key không double-boost (chỉ trả tiền 1 lần thì chỉ đư… |
| session_id | uuid | null |  | FK→match_sessions.id |  |
| idempotency_key | character varying(255) |  |  | UQ |  |
| created_at | timestamp with time zone |  | now() |  |  |
| updated_at | timestamp with time zone |  | now() |  |  |
| gender_preference | character varying(10) |  | 'any'::character varying |  | Snapshot lựa chọn giới tính cho LẦN vào queue này (đổi ý = cancel + join lại). Check khớp 2 chiều tại matcher `tryPair` với gender đọc TƯƠI từ users (docs/10 § 10.0.C) — KHÔNG shard theo gender: pref… |
| trust_penalty_ms | integer |  | 0 |  | Snapshot 1 LẦN lúc enqueue từ trust score user (docs/services/safety-service.md § 3.2) — KHÔNG cộng dồn/re-tính lại giữa lúc ticket chờ (khác priorityBoostMs — chỉ đổi qua API speed-up tường minh). C… |
| paid_diamond | boolean |  | false |  | True when this queue entry was opened by paying Diamond after free quota. |

Constraints: check match_tickets_gender_preference_check (((gender_preference)::text = ANY ((ARRAY['any'::character varying, 'male'::character varying, 'female'::character varying])::text[]))); check match_tickets_match_type_check (((match_type)::text = ANY ((ARRAY['soul'::character varying, 'voice'::character varying])::text[]))); check match_tickets_priority_boost_ms_check ((priority_boost_ms >= 0)); check match_tickets_status_check (((status)::text = ANY ((ARRAY['queued'::character varying, 'matched'::character varying, 'confirmed'::character varying, 'expired'::character varying, 'cancel…; unique uq_match_tickets_idempotency_key (idempotency_key)

Indexes: idx_match_tickets_status_enqueued (status, enqueued_at); idx_match_tickets_status_shard (status, match_type, region, age_band); idx_match_tickets_user (user_id); UNIQUE uq_match_tickets_active_user (user_id) WHERE ((status)::text = ANY ((ARRAY['queued'::character varying, 'matched'::character…; UNIQUE uq_match_tickets_idempotency_key (idempotency_key)

Access: matching(owner) r12/w15/lock3 via manager+manager(lock option)+manager.save(instance)+raw-sql+repository [apps/core-api/src/modules/matching/jobs/matcher-worker.service.ts:234, apps/core-api/src/modules/matching/jobs/ticket-sweeper.service.ts:106]

