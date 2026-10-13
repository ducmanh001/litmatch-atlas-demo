# Error codes PartyRoomErrors

id: errors:PartyRoomErrors
module: module:party-room

| code | http status at throw sites | throw sites | meaning | declared |
|---|---|---|---|---|
| PARTY_ROOM_NOT_FOUND | NOT_FOUND | 4 | Phòng không tồn tại HOẶC đã đóng với người ngoài — gộp, chống oracle (docs/10 § 10.1.D). | apps/core-api/src/modules/party-room/party-room.errors.ts:4 |
| PARTY_ROOM_CLOSED | CONFLICT | 3 | Phòng đã đóng — không join/đổi role/tặng quà được nữa. | apps/core-api/src/modules/party-room/party-room.errors.ts:6 |
| PARTY_ROOM_FULL | CONFLICT | 1 | Phòng đã đủ PARTY_MAX_MEMBERS. | apps/core-api/src/modules/party-room/party-room.errors.ts:8 |
| PARTY_MEMBER_ALREADY_IN_ANOTHER_ROOM | CONFLICT | 2 | User đang ở 1 phòng active khác — 1 user 1 phòng (party-room-service.md § 3). | apps/core-api/src/modules/party-room/party-room.errors.ts:10 |
| PARTY_MEMBER_NOT_A_MEMBER | FORBIDDEN | 4 | Caller không phải member active của phòng. | apps/core-api/src/modules/party-room/party-room.errors.ts:12 |
| PARTY_TARGET_NOT_A_MEMBER | NOT_FOUND | 2 | Target không phải member active của phòng. | apps/core-api/src/modules/party-room/party-room.errors.ts:14 |
| PARTY_MEMBER_NOT_HOST | FORBIDDEN | 2 | Chỉ host mới được cấp/thu quyền speaker (docs/06). | apps/core-api/src/modules/party-room/party-room.errors.ts:16 |
| PARTY_MEMBER_CANNOT_CHANGE_HOST_ROLE | CONFLICT | 1 | Không đổi role của chính host. | apps/core-api/src/modules/party-room/party-room.errors.ts:18 |
| PARTY_SPEAKER_LIMIT_REACHED | CONFLICT | 1 | Đã chạm speaker_limit của phòng (giới hạn cứng — docs/03 § 3.8.A). | apps/core-api/src/modules/party-room/party-room.errors.ts:20 |
| PARTY_SPEAKER_INVITE_NOT_PENDING | CONFLICT | 1 | Target chưa đồng ý lời mời lên speaker. | apps/core-api/src/modules/party-room/party-room.errors.ts:22 |
| PARTY_SPEAKER_INVITE_TARGET_INVALID | CONFLICT | 1 | Target đã là speaker/host hoặc không thể nhận lời mời. | apps/core-api/src/modules/party-room/party-room.errors.ts:24 |
| PARTY_ROOM_TITLE_TOO_LONG | BAD_REQUEST | 1 | Title vượt PARTY_TITLE_MAX_LENGTH. | apps/core-api/src/modules/party-room/party-room.errors.ts:26 |
| PARTY_WEBHOOK_INVALID | UNAUTHORIZED | 2 | Webhook LiveKit không verify được chữ ký. | apps/core-api/src/modules/party-room/party-room.errors.ts:28 |
| PARTY_MEDIA_ROOM_CREATE_FAILED |  | 0 | Tạo room trên SFU thất bại — thử lại sau. | apps/core-api/src/modules/party-room/party-room.errors.ts:30 |
| PARTY_ROOM_CURSOR_INVALID | BAD_REQUEST | 2 |  | apps/core-api/src/modules/party-room/party-room.errors.ts:31 |
| PARTY_ROOM_COMMENT_INVALID | UNPROCESSABLE_ENTITY | 1 |  | apps/core-api/src/modules/party-room/party-room.errors.ts:32 |
| PARTY_ROOM_COMMENT_IDEMPOTENCY_CONFLICT | CONFLICT | 1 |  | apps/core-api/src/modules/party-room/party-room.errors.ts:33 |
