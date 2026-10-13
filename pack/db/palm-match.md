# Database tables owned by module palm-match

id: db:palm-match
module: module:palm-match
tables: palm_match_active_participants, palm_match_queue_entries, palm_match_sessions, palm_reading_templates
see: ../modules/palm-match.md

### table: palm_match_active_participants

Chốt DB cho bất biến một user chỉ trỏ tới một Palm session chưa dismiss. Hai partial unique index trên low/high không chặn được trường hợp user nằm khác cột, nên dùng bảng participant.

Entity: PalmMatchActiveParticipant (apps/core-api/src/modules/palm-match/entities/palm-match-session.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| user_id | uuid |  |  | PK FK→users.id |  |
| session_id | uuid |  |  | FK→palm_match_sessions.id |  |
| created_at | timestamp with time zone |  | now() |  |  |

Indexes: idx_palm_match_active_session (session_id)

Access: palm-match(owner) r5/w4 via manager+querybuilder [apps/core-api/src/modules/palm-match/palm-match.service.ts:147, apps/core-api/src/modules/palm-match/palm-match.service.ts:180]

### table: palm_match_queue_entries

Hàng đợi idempotent: PK theo user ngăn hai tab enqueue hai lần.

Entity: PalmMatchQueueEntry (apps/core-api/src/modules/palm-match/entities/palm-match-session.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| user_id | uuid |  |  | PK FK→users.id |  |
| queued_at | timestamp with time zone |  | now() |  |  |

Indexes: idx_palm_match_queue_order (queued_at, user_id)

Access: palm-match(owner) r3/w6 via manager+querybuilder+raw-sql [apps/core-api/src/modules/palm-match/palm-match.service.ts:128, apps/core-api/src/modules/palm-match/palm-match.service.ts:131]

### table: palm_match_sessions

Snapshot của một lượt Palm Match. Sign, phần trăm và fortune được chốt một lần ở server để reload/retry không thể quay lại kết quả khác. Cặp user luôn canonical `low < high`.

Entity: PalmMatchSession (apps/core-api/src/modules/palm-match/entities/palm-match-session.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | uuid |  | gen_random_uuid() | PK |  |
| user_low_id | uuid |  |  | FK→users.id |  |
| user_high_id | uuid |  |  | FK→users.id |  |
| low_sign | character varying(24) |  |  |  |  |
| high_sign | character varying(24) |  |  |  |  |
| compatibility_percent | smallint |  |  |  |  |
| fortune | text |  |  |  |  |
| low_flipped_at | timestamp with time zone | null |  |  |  |
| high_flipped_at | timestamp with time zone | null |  |  |  |
| low_rating | character varying(8) | null |  |  |  |
| high_rating | character varying(8) | null |  |  |  |
| status | character varying(16) |  | 'active'::character varying |  |  |
| outcome | character varying(16) | null |  |  |  |
| expires_at | timestamp with time zone |  |  |  |  |
| closed_at | timestamp with time zone | null |  |  |  |
| created_at | timestamp with time zone |  | now() |  |  |
| updated_at | timestamp with time zone |  | now() |  |  |

Constraints: check chk_palm_match_compatibility (((compatibility_percent >= 60) AND (compatibility_percent <= 99))); check chk_palm_match_high_rating (((high_rating IS NULL) OR ((high_rating)::text = ANY ((ARRAY['like'::character varying, 'skip'::character varying])::text[])))); check chk_palm_match_low_rating (((low_rating IS NULL) OR ((low_rating)::text = ANY ((ARRAY['like'::character varying, 'skip'::character varying])::text[])))); check chk_palm_match_outcome (((outcome IS NULL) OR ((outcome)::text = ANY ((ARRAY['matched'::character varying, 'not_matched'::character varying, 'expired'::character varying, 'cancelled'…; check chk_palm_match_pair_canonical ((user_low_id < user_high_id)); check chk_palm_match_status (((status)::text = ANY ((ARRAY['active'::character varying, 'completed'::character varying])::text[]))); check chk_palm_match_terminal (((((status)::text = 'active'::text) AND (outcome IS NULL) AND (closed_at IS NULL)) OR (((status)::text = 'completed'::text) AND (outcome IS NOT NULL) AND (clo…

Indexes: idx_palm_match_sessions_expires_at (expires_at) WHERE ((status)::text = 'active'::text)

Access: palm-match(owner) r4/w6/lock3 via manager+manager(lock option)+manager.save(instance) [apps/core-api/src/modules/palm-match/palm-match.service.ts:313, apps/core-api/src/modules/palm-match/palm-match.service.ts:363]

### table: palm_reading_templates

Catalog nội dung bói toán giải trí (docs/services/palm-match-service.md § 2) — KHÔNG có bảng lịch sử, kết quả deterministic theo (userId, category, ngày server) tính lại mỗi lần gọi. `content` có thể chứa placeholder `{name}` được thay bằng `targetName` nếu client truyền vào. `isActive=false` cho phép tắt 1 dòng nội dung lỗi mà không xoá (giữ dấu vết nếu cần audit).

Entity: PalmReadingTemplate (apps/core-api/src/modules/palm-match/entities/palm-reading-template.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | integer |  | nextval('palm_reading_templates_id_seq'::regclass) | PK |  |
| category | character varying(16) |  |  |  |  |
| content | text |  |  |  |  |
| is_active | boolean |  | true |  |  |

Constraints: check chk_palm_reading_templates_category (((category)::text = ANY ((ARRAY['love'::character varying, 'career'::character varying, 'health'::character varying, 'general'::character varying])::text[])))

Indexes: idx_palm_reading_templates_category_active (category, is_active)

Access: palm-match(owner) r2/w0 via manager+repository [apps/core-api/src/modules/palm-match/palm-match.service.ts:86, apps/core-api/src/modules/palm-match/palm-match.service.ts:339]

