# Database tables owned by module mini-game

id: db:mini-game
module: module:mini-game
tables: mini_game_active_participants, mini_game_sessions
see: ../modules/mini-game.md

### table: mini_game_active_participants

Bảng phụ trợ THUẦN để enforce bất biến "1 user chỉ có 1 MiniGameSession `waiting_moves` tại 1 thời điểm" — PK trên `userId` chặn 1 user xuất hiện ở > 1 session đang chờ move BẤT KỂ họ là `userLowId` hay `userHighId` của session đó. COPY kỹ thuật `MovieSessionActiveParticipant` (đã chứng minh đúng bằng integration test race ở `movie-match.integration.spec.ts`): lý do cần bảng riêng thay vì 2 partial unique index trên `mini_game_sessions(user_low_id)`/`(user_high_id)` — 2 index đơn cột đó KHÔNG đủ, vì 1 user có thể là `userLowId` ở session A (với bạn X) và `userHighId` ở session B (với bạn Y) CÙNG LÚC; mỗi index chỉ soi 1 cột nên không phát hiện xung đột CHÉO cột. PK trên `userId` ở ĐÚNG 1 cột gộp cả 2 vai trò thì chặn được toàn bộ trường hợp. Ghi/xoá LUÔN cùng transaction với thay đổi `MiniGameSession.status` (`MiniGameService.createSession`/`submitMove` khi resolve/`cancelSession`) — không có API đọc/ghi bảng này ngoài đó.

Entity: MiniGameActiveParticipant (apps/core-api/src/modules/mini-game/entities/mini-game-active-participant.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| user_id | uuid |  |  | PK FK→users.id |  |
| session_id | uuid |  |  | FK→mini_game_sessions.id |  |

Indexes: idx_mini_game_active_participants_session (session_id)

Access: mini-game(owner) r0/w4 via manager+querybuilder [apps/core-api/src/modules/mini-game/mini-game.service.ts:101, apps/core-api/src/modules/mini-game/mini-game.service.ts:201]

### table: mini_game_sessions

Ván oẳn tù tì 2 người ĐÃ LÀ BẠN (docs/services/mini-game-service.md). Cặp canonical `userLowId < userHighId` — cùng kỹ thuật `Friendship`/`MovieSession`. Cột đặt tên theo PARTICIPANT (`lowMove`/`highMove`) thay vì "player1/player2" để khỏi mơ hồ ai là ai (spec § 2). `gameType` là varchar — hiện chỉ `rock_paper_scissors`, mở rộng thêm giá trị enum khi có game thứ 2 thay vì đổi cấu trúc bảng. Bất biến DB (migration 1753400000000): 1 user chỉ có 1 MiniGameSession `waiting_moves` tại 1 thời điểm, enforce qua bảng phụ trợ `MiniGameActiveParticipant` (PK `userId`) — xem comment ở đó (và ở `MovieSessionActiveParticipant` — cùng kỹ thuật) để biết vì sao KHÔNG dùng 2 partial unique index đơn cột trên `userLowId`/`userHighId`.

Entity: MiniGameSession (apps/core-api/src/modules/mini-game/entities/mini-game-session.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | uuid |  | gen_random_uuid() | PK |  |
| user_low_id | uuid |  |  | FK→users.id |  |
| user_high_id | uuid |  |  | FK→users.id |  |
| game_type | character varying(32) |  | 'rock_paper_scissors'::character varying |  |  |
| low_move | character varying(16) | null |  |  |  |
| high_move | character varying(16) | null |  |  |  |
| status | character varying(16) |  | 'waiting_moves'::character varying |  |  |
| winner_user_id | uuid | null |  | FK→users.id | null nếu hoà. |
| resolved_at | timestamp with time zone | null |  |  |  |
| created_at | timestamp with time zone |  | now() |  |  |
| updated_at | timestamp with time zone |  | now() |  |  |

Constraints: check chk_mini_game_sessions_canonical ((user_low_id < user_high_id)); check chk_mini_game_sessions_game_type (((game_type)::text = 'rock_paper_scissors'::text)); check chk_mini_game_sessions_high_move (((high_move IS NULL) OR ((high_move)::text = ANY ((ARRAY['rock'::character varying, 'paper'::character varying, 'scissors'::character varying])::text[])))); check chk_mini_game_sessions_low_move (((low_move IS NULL) OR ((low_move)::text = ANY ((ARRAY['rock'::character varying, 'paper'::character varying, 'scissors'::character varying])::text[])))); check chk_mini_game_sessions_status (((status)::text = ANY ((ARRAY['waiting_moves'::character varying, 'resolved'::character varying, 'cancelled'::character varying])::text[]))); check chk_mini_game_sessions_winner (((winner_user_id IS NULL) OR ((winner_user_id = user_low_id) OR (winner_user_id = user_high_id))))

Indexes: idx_mini_game_sessions_pair_status (user_low_id, user_high_id, status)

Access: mini-game(owner) r3/w8 via manager+manager.save(instance)+querybuilder+repository [apps/core-api/src/modules/mini-game/mini-game.service.ts:88, apps/core-api/src/modules/mini-game/mini-game.service.ts:156]

