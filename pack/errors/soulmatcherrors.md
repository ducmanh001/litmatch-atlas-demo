# Error codes SoulMatchErrors

id: errors:SoulMatchErrors
module: module:soul-match

| code | http status at throw sites | throw sites | meaning | declared |
|---|---|---|---|---|
| SOUL_MATCH_SESSION_NOT_FOUND | NOT_FOUND | 2 | Session không tồn tại HOẶC caller không phải thành viên — gộp 1 mã, không làm oracle dò sessionId. | apps/core-api/src/modules/soul-match/soul-match.errors.ts:4 |
| SOUL_MATCH_CHAT_NOT_OPEN | CONFLICT | 4 | Phòng chưa mở (chưa đủ 2 confirm / không phải soul) hoặc đã qua phase cho phép. | apps/core-api/src/modules/soul-match/soul-match.errors.ts:6 |
| SOUL_MATCH_RATING_NOT_OPEN | CONFLICT | 1 | Hết cửa sổ rating (phase closed). | apps/core-api/src/modules/soul-match/soul-match.errors.ts:8 |
| SOUL_MATCH_RATING_CONFLICT | CONFLICT | 1 | Đã rate verdict khác — rating immutable (docs/10 § Soul Match). | apps/core-api/src/modules/soul-match/soul-match.errors.ts:10 |
| SOUL_MATCH_MESSAGE_IDEMPOTENCY_CONFLICT | CONFLICT | 1 | Cùng Idempotency-Key nhưng nội dung/session khác (docs/05 § 5.10). | apps/core-api/src/modules/soul-match/soul-match.errors.ts:12 |
| SOUL_MATCH_MESSAGE_TOO_LONG | UNPROCESSABLE_ENTITY | 1 | Vượt SOUL_CHAT_MESSAGE_MAX_LENGTH (config) — DTO chỉ chặn sanity cap. | apps/core-api/src/modules/soul-match/soul-match.errors.ts:14 |
| SOUL_MATCH_PARTNER_LOCKED | FORBIDDEN | 1 | Chưa match (chưa có Friendship) — profile đối phương còn khoá (docs/01 #1). | apps/core-api/src/modules/soul-match/soul-match.errors.ts:16 |
| SOUL_MATCH_CURSOR_INVALID | BAD_REQUEST | 1 |  | apps/core-api/src/modules/soul-match/soul-match.errors.ts:17 |
| SOUL_MATCH_USER_BANNED | FORBIDDEN | 1 | Bị ban giữa chừng — re-check tại thời điểm gửi (docs/10 § 10.0.C). | apps/core-api/src/modules/soul-match/soul-match.errors.ts:19 |
