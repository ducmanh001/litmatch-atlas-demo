# REST API of mini-game

id: api:mini-game
module: module:mini-game
prefix: /api/v1; success body {data, meta?}; errors are DomainException codes (see ../errors/)
see: ../modules/mini-game.md

### POST /mini-game/sessions

Tạo ván oẳn tù tì với một người bạn (404 nếu không phải bạn), idempotent theo cặp, 409 ALREADY_WAITING nếu user đang chờ move ở cặp khác; publish minigame.session.started cho cả hai.

- handler: MiniGameController.createSession (apps/core-api/src/modules/mini-game/mini-game.controller.ts:34); operationId: MiniGameController_createSession
- auth: Bearer JWT
- body CreateMiniGameSessionDto: friendUserId*:string, gameType*:enum(rock_paper_scissors)[default="rock_paper_scissors"]
- response 200 MiniGameSessionDto: id:string, partnerUserId:string, gameType:string, status:string, myMove:string, opponentHasMoved:boolean, opponentMove:string, winnerUserId:string, resolvedAt:string(date-time)
- FE callers: none in this repo (mobile/third party or dead)

### POST /mini-game/sessions/{id}/moves

Nộp move rock/paper/scissors bằng UPDATE có điều kiện (move IS NULL AND status=waiting_moves); đủ 2 move thì resolve trong cùng transaction và publish minigame.session.resolved kèm cả 2 move.

- handler: MiniGameController.submitMove (apps/core-api/src/modules/mini-game/mini-game.controller.ts:53); operationId: MiniGameController_submitMove
- auth: Bearer JWT
- params: path:id*:string
- body SubmitMiniGameMoveDto: move*:enum(rock|paper|scissors)
- response 200 MiniGameSessionDto: id:string, partnerUserId:string, gameType:string, status:string, myMove:string, opponentHasMoved:boolean, opponentMove:string, winnerUserId:string, resolvedAt:string(date-time)
- FE callers: none in this repo (mobile/third party or dead)

### POST /mini-game/sessions/{id}/cancel

Một trong hai participant huỷ ván đang chờ move (idempotent nếu đã cancelled, 409 nếu đã resolved), giải phóng slot participant.

- handler: MiniGameController.cancelSession (apps/core-api/src/modules/mini-game/mini-game.controller.ts:73); operationId: MiniGameController_cancelSession
- auth: Bearer JWT
- params: path:id*:string
- response 200 MiniGameSessionDto: id:string, partnerUserId:string, gameType:string, status:string, myMove:string, opponentHasMoved:boolean, opponentMove:string, winnerUserId:string, resolvedAt:string(date-time)
- FE callers: none in this repo (mobile/third party or dead)

### GET /mini-game/sessions/{id}

Đọc ván (chỉ participant, 404 gộp nếu không tồn tại/không phải thành viên); là fallback polling khi realtime không tới.

- handler: MiniGameController.getSession (apps/core-api/src/modules/mini-game/mini-game.controller.ts:87); operationId: MiniGameController_getSession
- auth: Bearer JWT
- params: path:id*:string
- response 200 MiniGameSessionDto: id:string, partnerUserId:string, gameType:string, status:string, myMove:string, opponentHasMoved:boolean, opponentMove:string, winnerUserId:string, resolvedAt:string(date-time)
- FE callers: none in this repo (mobile/third party or dead)

