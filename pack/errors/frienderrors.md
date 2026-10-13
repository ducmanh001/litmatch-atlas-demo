# Error codes FriendErrors

id: errors:FriendErrors
module: module:friend

| code | http status at throw sites | throw sites | meaning | declared |
|---|---|---|---|---|
| FRIEND_NOT_FRIEND | NOT_FOUND | 2 | Không có conversation với profile (gộp not-found + chưa mở chat, chống oracle). | apps/core-api/src/modules/friend/friend.errors.ts:4 |
| FRIEND_CONVERSATION_NOT_FOUND | NOT_FOUND | 2 | Conversation không tồn tại HOẶC caller không phải thành viên — cùng 1 mã (docs/10 § 10.1.D). | apps/core-api/src/modules/friend/friend.errors.ts:6 |
| FRIEND_MESSAGE_IDEMPOTENCY_CONFLICT | CONFLICT | 1 | Cùng Idempotency-Key nhưng nội dung/conversation khác (docs/05 § 5.10). | apps/core-api/src/modules/friend/friend.errors.ts:8 |
| FRIEND_MESSAGE_TOO_LONG | UNPROCESSABLE_ENTITY | 1 |  | apps/core-api/src/modules/friend/friend.errors.ts:9 |
| FRIEND_MESSAGE_EMPTY | UNPROCESSABLE_ENTITY | 1 | Message không có cả text lẫn ảnh — không có gì để gửi. | apps/core-api/src/modules/friend/friend.errors.ts:11 |
| FRIEND_CURSOR_INVALID | BAD_REQUEST | 2 |  | apps/core-api/src/modules/friend/friend.errors.ts:12 |
