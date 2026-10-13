# Error codes MiniGameErrors

id: errors:MiniGameErrors
module: module:mini-game

| code | http status at throw sites | throw sites | meaning | declared |
|---|---|---|---|---|
| MINI_GAME_NOT_FRIEND | NOT_FOUND | 2 | `friendUserId` không phải bạn của caller HOẶC caller tự nhắm chính mình — gộp 1 mã, cùng 404 (chống oracle dò userId, cùng pattern `MovieMatchErrors.NOT_FRIEND`). | apps/core-api/src/modules/mini-game/mini-game.errors.ts:7 |
| MINI_GAME_ALREADY_WAITING | CONFLICT | 1 | Caller đang chờ move ở 1 CẶP KHÁC — không tự ý huỷ ván cũ thay user. | apps/core-api/src/modules/mini-game/mini-game.errors.ts:9 |
| MINI_GAME_SESSION_NOT_FOUND | NOT_FOUND | 1 | Session không tồn tại HOẶC caller không phải participant — cùng 1 mã (docs/10 § 10.1.D). | apps/core-api/src/modules/mini-game/mini-game.errors.ts:11 |
| MINI_GAME_MOVE_ALREADY_SUBMITTED | CONFLICT | 1 | Nộp move lần 2 (đã nộp trước đó) HOẶC session không còn `waiting_moves` (đã `resolved`/`cancelled`) — update có điều kiện thấy 0 row ảnh hưởng (docs/10 § 10.1.C). | apps/core-api/src/modules/mini-game/mini-game.errors.ts:16 |
| MINI_GAME_NOT_CANCELLABLE | CONFLICT | 2 | Huỷ ván đã `resolved`/`cancelled` — chỉ huỷ được ván đang `waiting_moves`. | apps/core-api/src/modules/mini-game/mini-game.errors.ts:18 |
