# Module mini-game

id: module:mini-game
kind: core-api module
code: apps/core-api/src/modules/mini-game

Sở hữu phiên oẳn tù tì giữa 2 người đã là bạn (mini_game_sessions + bảng participant giữ bất biến 1 ván chờ move/user) và logic thắng thua. Không sở hữu quan hệ bạn bè (Friend), không chạm Economy, không tích hợp tin nhắn chat; chỉ publish 2 event realtime sau khi commit.

## Facts

- tables owned: mini_game_active_participants, mini_game_sessions
- API: 4 endpoints, 0 public, 0 admin-only, 0 require Idempotency-Key, 0 with own throttle
- publishes realtime: minigame.session.started, minigame.session.resolved
- env vars read: REDIS_URL
- error codes: 5 in MiniGameErrors
- tests: 1 unit spec files, 1 integration spec files
- history: 7 commits (1 fixes), last 2026-10-09
- docs: docs/services/mini-game-service.md (last changed 2026-10-09)
- features: mini-game

## Where to read next

- tables: ../db/mini-game.md
- API contract: ../api/mini-game.md
- why it talks to other services: ../service-map/mini-game.md and ../service-map/mini-game-techniques.md
- error codes: ../errors/minigameerrors.md
- flows involving it: [mini-game-session](../flows/mini-game-session.md)

## Linked issues (heuristic, public subset)

- P090 [fixed] Huỷ ván mini-game ghi đè ván đã resolved

See ../issues/index.md.
