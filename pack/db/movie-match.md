# Database tables owned by module movie-match

id: db:movie-match
module: module:movie-match
tables: movie_match_queue_entries, movie_session_active_participants, movie_session_messages, movie_sessions
see: ../modules/movie-match.md

### table: movie_match_queue_entries

Hàng chờ ghép ẩn danh — PK userId: 1 user chỉ queue 1 lần (cùng kỹ thuật Palm Match).

Entity: MovieMatchQueueEntry (apps/core-api/src/modules/movie-match/entities/movie-match-anon.entities.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| user_id | uuid |  |  | PK |  |
| queued_at | timestamp with time zone |  | now() |  |  |

Access: movie-match(owner) r3/w6 via manager+querybuilder+raw-sql [apps/core-api/src/modules/movie-match/movie-match.service.ts:308, apps/core-api/src/modules/movie-match/movie-match.service.ts:311]

### table: movie_session_active_participants

Bảng phụ trợ THUẦN để enforce bất biến "1 user chỉ có 1 MovieSession active tại 1 thời điểm" — PK trên `userId` chặn 1 user xuất hiện ở > 1 session active BẤT KỂ họ là `userLowId` hay `userHighId` của session đó. Lý do cần bảng riêng thay vì 2 partial unique index trên `movie_sessions(user_low_id)`/ `(user_high_id)`: 2 index đơn cột đó KHÔNG đủ — 1 user có thể là `userLowId` ở session A (với bạn X) và `userHighId` ở session B (với bạn Y) CÙNG LÚC; mỗi index chỉ soi 1 cột nên không phát hiện xung đột CHÉO cột (phát hiện qua integration test race, xem `movie-match.integration.spec.ts`). PK trên `userId` ở ĐÚNG 1 cột gộp cả 2 vai trò thì chặn được toàn bộ trường hợp. Ghi/xoá LUÔN cùng transaction với thay đổi `MovieSession.status` (`MovieMatchService.createSession`/`endSession`) — không có API đọc/ghi bảng này ngoài đó.

Entity: MovieSessionActiveParticipant (apps/core-api/src/modules/movie-match/entities/movie-session-active-participant.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| user_id | uuid |  |  | PK FK→users.id |  |
| session_id | uuid |  |  | FK→movie_sessions.id |  |

Indexes: idx_movie_session_active_participants_session (session_id)

Access: movie-match(owner) r4/w7 via manager+querybuilder [apps/core-api/src/modules/movie-match/movie-match.service.ts:153, apps/core-api/src/modules/movie-match/movie-match.service.ts:259]

### table: movie_session_messages

Chat trong phiên ẩn danh — APPEND-ONLY, sống theo session (khác Friend Chat: 2 người CHƯA là bạn nên không có Conversation; mutual-like xong chat dài hạn chuyển sang Tin nhắn). DTO trả vai trò tương đối `me|partner`, KHÔNG lộ senderUserId (ẩn danh tới khi matched).

Entity: MovieSessionMessage (apps/core-api/src/modules/movie-match/entities/movie-match-anon.entities.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | uuid |  | gen_random_uuid() | PK |  |
| seq | bigint |  |  |  | Thứ tự DB cấp cho cursor keyset — cùng lý do Message.seq (không dùng createdAt). |
| session_id | uuid |  |  | FK→movie_sessions.id |  |
| sender_user_id | uuid |  |  |  |  |
| content | text |  |  |  |  |
| idempotency_key | character varying(255) |  |  | UQ | Prefix `movie:msg:{userId}:{key}` — unique DB, client retry không nhân đôi. |
| created_at | timestamp with time zone |  | now() |  |  |

Constraints: unique movie_session_messages_idempotency_key_key (idempotency_key)

Indexes: idx_movie_session_messages_session_seq (session_id, seq); UNIQUE movie_session_messages_idempotency_key_key (idempotency_key)

Access: movie-match(owner) r2/w2 via querybuilder+repository [apps/core-api/src/modules/movie-match/movie-match.service.ts:553, apps/core-api/src/modules/movie-match/movie-match.service.ts:554]

### table: movie_sessions

Phiên xem chung 2 người ĐÃ LÀ BẠN (docs/services/movie-match-service.md). Cặp canonical `userLowId < userHighId` — cùng kỹ thuật `Friendship`/`Conversation`. KHÔNG có bảng message riêng: chat trong lúc xem đi thẳng qua `Conversation`/`FriendService` đã có (spec § 2). Bất biến DB (migration 1753200000000): 1 user chỉ có 1 MovieSession `active` tại 1 thời điểm, enforce qua bảng phụ trợ `MovieSessionActiveParticipant` (PK `userId`) — xem comment ở đó để biết vì sao KHÔNG dùng 2 partial unique index đơn cột trên `userLowId`/`userHighId`.

Entity: MovieSession (apps/core-api/src/modules/movie-match/entities/movie-session.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | uuid |  | gen_random_uuid() | PK |  |
| user_low_id | uuid |  |  | FK→users.id |  |
| user_high_id | uuid |  |  | FK→users.id |  |
| video_url | text |  |  |  | Whitelist domain validate ở service (`MOVIE_MATCH_ALLOWED_VIDEO_HOSTS`) — không proxy/transcode. |
| position_seconds | double precision |  | 0 |  | Nguồn sự thật vị trí phát — client nội suy giữa 2 lần cập nhật, không tin đồng hồ client. |
| is_playing | boolean |  | false |  |  |
| position_updated_at | timestamp with time zone |  | now() |  | Server timestamp của lần ghi `positionSeconds`/`isPlaying` gần nhất. |
| status | character varying(16) |  | 'active'::character varying |  |  |
| ended_at | timestamp with time zone | null |  |  |  |
| end_reason | character varying(16) | null |  |  |  |
| created_at | timestamp with time zone |  | now() |  |  |
| updated_at | timestamp with time zone |  | now() |  |  |
| mode | character varying(12) |  | 'friend'::character varying |  | ---- flow ẩn danh (movie-match.html) — mặc định/`NULL` cho session bạn bè cũ ---- |
| expires_at | timestamp with time zone | null |  |  | Deadline phiên ẩn danh (config MOVIE_MATCH_ANON_DURATION_SECONDS) — lazy expiry. |
| watch_ended_at | timestamp with time zone | null |  |  | Mốc "Kết thúc"/hết giờ — sang phase rating; NULL = đang xem. |
| low_rating | character varying(8) | null |  |  |  |
| high_rating | character varying(8) | null |  |  |  |
| outcome | character varying(12) | null |  |  |  |

Constraints: check chk_movie_sessions_canonical ((user_low_id < user_high_id)); check chk_movie_sessions_end_reason (((end_reason IS NULL) OR ((end_reason)::text = ANY ((ARRAY['left'::character varying, 'replaced'::character varying])::text[])))); check chk_movie_sessions_position_nonneg ((position_seconds >= (0)::double precision)); check chk_movie_sessions_status (((status)::text = ANY ((ARRAY['active'::character varying, 'ended'::character varying])::text[])))

Indexes: idx_movie_sessions_pair_status (user_low_id, user_high_id, status)

Access: movie-match(owner) r7/w12/lock3 via manager+manager(lock option)+manager.save(instance)+querybuilder+repository [apps/core-api/src/modules/movie-match/movie-match.service.ts:141, apps/core-api/src/modules/movie-match/movie-match.service.ts:209]

