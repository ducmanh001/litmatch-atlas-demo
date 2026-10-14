# Feature slice: mini-game

id: slice:mini-game
status: implemented; owner: core-api/mini-game

Two-player rock-paper-scissors mini game

This file follows ONE feature through every layer. Layers are joined by the actual endpoint calls found in the frontend code, so it shows what the code does, not what a plan says.

## 1. Product contract and docs

- registry contracts: openapi/core-api.json, docs/services/mini-game-service.md, docs/services/realtime-gateway.md
- registry verification tests: apps/core-api/src/modules/mini-game/mini-game.integration.spec.ts
- service docs: docs/services/mini-game-service.md

## 2. Frontend

No web/admin route calls this feature's endpoints in the repo (a mobile app or another client may).

## 3. API

| endpoint | auth | idempotency | throttle | FE callers |
|---|---|---|---|---|
| POST /mini-game/sessions ([mini-game](../api/mini-game.md)) | JWT |  |  | 0 |
| POST /mini-game/sessions/{id}/moves ([mini-game](../api/mini-game.md)) | JWT |  |  | 0 |
| POST /mini-game/sessions/{id}/cancel ([mini-game](../api/mini-game.md)) | JWT |  |  | 0 |
| GET /mini-game/sessions/{id} ([mini-game](../api/mini-game.md)) | JWT |  |  | 0 |


## 4. Backend flows (service map)

- [mini-game-session](../flows/mini-game-session.md): Mini game giữa hai bạn bè (2 endpoints of this feature)

## 5. Data

- tables owned by the module(s): `mini_game_active_participants`, `mini_game_sessions` (see ../db/mini-game.md)
- status vocabularies enforced by CHECK:
  - mini_game_sessions: rock | paper | scissors
  - mini_game_sessions: rock | paper | scissors
  - mini_game_sessions: waiting_moves | resolved | cancelled

## 6. Events

- minigame.session.started: producers mini-game.createSession; FE consumers 0
- minigame.session.resolved: producers mini-game.submitMove; FE consumers 0

## 7. Errors

- error code groups: [MiniGameErrors](../errors/minigameerrors.md)
- codes the FE references for this feature: none

## 8. Configuration

env vars read by the module(s): `REDIS_URL` (see ../config/)

## 9. Tests

- mini-game: 1 unit spec files, 1 integration spec files
- e2e files that call these endpoints: none
- FE specs: none

## 10. Known issues (linked from the ledger)

- P090 [fixed] Huỷ ván mini-game ghi đè ván đã resolved

See ../issues/index.md.
