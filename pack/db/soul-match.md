# Database tables owned by module soul-match

id: db:soul-match
module: module:soul-match
tables: soul_chat_messages, soul_match_ratings
see: ../modules/soul-match.md

### table: soul_chat_messages

Message của chat ẩn danh tạm thời — APPEND-ONLY (không updatedAt, không extend BaseAppEntity): phòng đóng thì khoá qua API chứ KHÔNG xoá dữ liệu — giữ bằng chứng cho Report/T&S về sau (docs/10 § Soul Match, docs/services/soul-match-service.md § 1).

Entity: SoulChatMessage (apps/core-api/src/modules/soul-match/entities/soul-chat-message.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | uuid |  | gen_random_uuid() | PK |  |
| seq | bigint |  |  |  | Thứ tự tăng dần DB cấp (bigint identity) cho cursor keyset — không dùng createdAt làm cursor (2 message cùng mili-giây làm trùng/mất dòng khi phân trang). bigint → TypeORM đọc về string. |
| session_id | uuid |  |  | FK→match_sessions.id |  |
| sender_user_id | uuid |  |  | FK→users.id |  |
| content | text |  |  |  |  |
| idempotency_key | character varying(255) |  |  | UQ | Prefix `soul:msg:{userId}:{key}` — unique DB, client retry không nhân đôi message. |
| created_at | timestamp with time zone |  | now() |  |  |

Constraints: unique uq_soul_chat_messages_idempotency_key (idempotency_key)

Indexes: idx_soul_chat_messages_session_seq (session_id, seq); UNIQUE uq_soul_chat_messages_idempotency_key (idempotency_key)

Access: soul-match(owner) r3/w2 via manager+manager.save(instance)+querybuilder+repository [apps/core-api/src/modules/soul-match/soul-match.service.ts:187, apps/core-api/src/modules/soul-match/soul-match.service.ts:226]

### table: soul_match_ratings

1 người 1 verdict cho 1 session — IMMUTABLE (đổi ý = mở đường thăm dò "like thử xem bên kia like chưa rồi đổi lại" — docs/10 § Soul Match). Append-only nên không extend BaseAppEntity (không có updatedAt — cùng lý do LedgerEntry). Unique (sessionId, raterUserId) ở DB là idempotency tự nhiên, không cần Idempotency-Key.

Entity: SoulMatchRating (apps/core-api/src/modules/soul-match/entities/soul-match-rating.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | uuid |  | gen_random_uuid() | PK |  |
| session_id | uuid |  |  | FK→match_sessions.id |  |
| rater_user_id | uuid |  |  | FK→users.id |  |
| verdict | character varying(8) |  |  |  |  |
| created_at | timestamp with time zone |  | now() |  |  |

Constraints: check soul_match_ratings_verdict_check (((verdict)::text = ANY ((ARRAY['rude'::character varying, 'boring'::character varying, 'like'::character varying])::text[]))); unique uq_soul_match_ratings_session_rater (session_id,rater_user_id)

Indexes: UNIQUE uq_soul_match_ratings_session_rater (session_id, rater_user_id)

Access: soul-match(owner) r4/w2 via manager+manager.save(instance)+repository [apps/core-api/src/modules/soul-match/soul-match.service.ts:92, apps/core-api/src/modules/soul-match/soul-match.service.ts:316]

