# Error codes MatchingErrors

id: errors:MatchingErrors
module: module:matching

| code | http status at throw sites | throw sites | meaning | declared |
|---|---|---|---|---|
| MATCHING_TICKET_NOT_FOUND | NOT_FOUND | 2 |  | apps/core-api/src/modules/matching/matching.errors.ts:3 |
| MATCHING_TICKET_ALREADY_QUEUED | CONFLICT | 1 |  | apps/core-api/src/modules/matching/matching.errors.ts:4 |
| MATCHING_TICKET_INVALID_TRANSITION | CONFLICT | 4 |  | apps/core-api/src/modules/matching/matching.errors.ts:5 |
| MATCHING_TICKET_FORBIDDEN | FORBIDDEN | 1 | Ticket không thuộc user đang gọi — chống IDOR (docs/10 § 10.1.D). | apps/core-api/src/modules/matching/matching.errors.ts:7 |
| MATCHING_TICKET_IDEMPOTENCY_CONFLICT | CONFLICT | 1 | Cùng Idempotency-Key nhưng nội dung request khác (docs/05 § 5.10). | apps/core-api/src/modules/matching/matching.errors.ts:9 |
| MATCHING_SESSION_NOT_FOUND | NOT_FOUND | 2 | Session không tồn tại hoặc caller không phải một trong hai thành viên (gộp, chống oracle). | apps/core-api/src/modules/matching/matching.errors.ts:11 |
| MATCHING_SESSION_NOT_CALLABLE | CONFLICT | 2 |  | apps/core-api/src/modules/matching/matching.errors.ts:12 |
| MATCHING_SESSION_NOT_PENDING | CONFLICT | 3 |  | apps/core-api/src/modules/matching/matching.errors.ts:13 |
| MATCHING_USER_BANNED | FORBIDDEN,CONFLICT | 2 | User bị ban không được vào hàng đợi (re-verify lại lần nữa tại thời điểm ghép). | apps/core-api/src/modules/matching/matching.errors.ts:15 |
| MATCHING_GUEST_DEVICE_TOKEN_REQUIRED | UNAUTHORIZED | 1 |  | apps/core-api/src/modules/matching/matching.errors.ts:16 |
| MATCHING_GUEST_DAILY_QUOTA_EXCEEDED | TOO_MANY_REQUESTS | 1 |  | apps/core-api/src/modules/matching/matching.errors.ts:17 |
| MATCHING_DAILY_QUOTA_EXCEEDED | TOO_MANY_REQUESTS | 1 |  | apps/core-api/src/modules/matching/matching.errors.ts:18 |
| MATCHING_INVITE_TARGET_UNAVAILABLE | UNPROCESSABLE_ENTITY,FORBIDDEN | 4 | Không mời được người đang nằm trong hidden-set (banned/guest/block/report) — oracle-safe, cùng mã bất kể lý do. | apps/core-api/src/modules/matching/matching.errors.ts:20 |
| MATCHING_INVITE_NOT_FOUND | NOT_FOUND | 2 |  | apps/core-api/src/modules/matching/matching.errors.ts:21 |
| MATCHING_INVITE_FORBIDDEN | FORBIDDEN | 5 | Invite không thuộc user đang gọi (không phải inviter/invitee tương ứng hành động). | apps/core-api/src/modules/matching/matching.errors.ts:23 |
| MATCHING_INVITE_INVALID_TRANSITION | CONFLICT | 2 |  | apps/core-api/src/modules/matching/matching.errors.ts:24 |
| MATCHING_INVITE_ALREADY_PENDING | CONFLICT | 1 | Đã có 1 invite PENDING khác tới đúng người này (unique DB, không phải rate-limit chính). | apps/core-api/src/modules/matching/matching.errors.ts:26 |
| MATCHING_INVITE_RATE_LIMITED | TOO_MANY_REQUESTS | 1 |  | apps/core-api/src/modules/matching/matching.errors.ts:27 |
| MATCHING_INVITE_ACCEPT_USER_BUSY | CONFLICT | 1 | 1 trong 2 bên (inviter/invitee) đang có ticket active khác tại thời điểm accept (docs/06). | apps/core-api/src/modules/matching/matching.errors.ts:29 |
| MATCHING_INVITE_CURSOR_INVALID | BAD_REQUEST | 1 |  | apps/core-api/src/modules/matching/matching.errors.ts:30 |
