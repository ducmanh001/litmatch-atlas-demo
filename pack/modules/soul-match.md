# Module soul-match

id: module:soul-match
kind: core-api module
code: apps/core-api/src/modules/soul-match

Sở hữu phòng chat ẩn danh ngắn sau khi Matching ghép cặp: tin nhắn (append-only), rating 2 chiều rude|boring|like (bất biến) và quyết định 'cả hai like → mở Friendship + unlock profile'. KHÔNG sở hữu MatchSession (Matching ghi), Friendship/Conversation (Friend ghi), hồ sơ người dùng (User) và không có entity phòng riêng: phase chatting/rating/closed được derive từ giờ server.

## Facts

- tables owned: soul_chat_messages, soul_match_ratings
- touches tables of other modules: match_sessions (owner matching; read 2, write 0)
- API: 6 endpoints, 0 public, 0 admin-only, 1 require Idempotency-Key, 1 with own throttle
- publishes realtime: soul.message, soul.matched, soul.ended
- env vars read: REDIS_URL, SOUL_CHAT_MESSAGE_MAX_LENGTH, SOUL_CHAT_DURATION_SECONDS, SOUL_RATING_WINDOW_SECONDS
- error codes: 9 in SoulMatchErrors
- tests: 1 unit spec files, 1 integration spec files
- history: 17 commits (2 fixes), last 2026-08-13
- docs: docs/services/soul-match-service.md (last changed 2026-08-01)
- features: soul-match

## Where to read next

- tables: ../db/soul-match.md
- API contract: ../api/soul-match.md
- why it talks to other services: ../service-map/soul-match.md and ../service-map/soul-match-techniques.md
- error codes: ../errors/soulmatcherrors.md
- flows involving it: [soul-match-room-reads-and-reveal](../flows/soul-match-room-reads-and-reveal.md), [soul-match-send-message](../flows/soul-match-send-message.md), [soul-match-session](../flows/soul-match-session.md)

## Linked issues (heuristic, public subset)

- P006 [fixed] Swagger thiếu cursor pagination → client sinh kiểu never

See ../issues/index.md.
