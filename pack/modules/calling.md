# Module calling

id: module:calling
kind: core-api module
code: apps/core-api/src/modules/calling

Sở hữu CallSession (pending/active/ended), VoiceMatchReaction, token join LiveKit và bộ đếm thời gian Voice Match enforce ở server (7 phút, pending timeout, reconnect grace). KHÔNG sở hữu MatchSession (Matching ghi), Friendship (Friend ghi), cũng không chạy SFU (media-server) và không có export cho module khác gọi vào.

## Facts

- tables owned: call_sessions, voice_match_reactions
- API: 7 endpoints, 1 public, 0 admin-only, 0 require Idempotency-Key, 2 with own throttle
- publishes realtime: call.ended
- jobs: CallTickerService
- env vars read: CALLING_FREE_CALL_SECONDS, CALLING_TOKEN_TTL_SECONDS, LIVEKIT_URL, LIVEKIT_REGION_URLS, CALLING_TICKER_INTERVAL_MS, CALLING_PENDING_TIMEOUT_SECONDS, CALLING_RECONNECT_WINDOW_SECONDS, REDIS_URL
- error codes: 6 in CallingErrors
- tests: 3 unit spec files, 1 integration spec files
- history: 20 commits (1 fixes), last 2026-08-20
- docs: docs/services/calling-service.md (last changed 2026-08-20)
- features: calling

## Where to read next

- tables: ../db/calling.md
- API contract: ../api/calling.md
- why it talks to other services: ../service-map/calling.md and ../service-map/calling-techniques.md
- error codes: ../errors/callingerrors.md
- flows involving it: [calling-end-and-ticker](../flows/calling-end-and-ticker.md), [calling-like-after-call](../flows/calling-like-after-call.md), [friend-direct-call](../flows/friend-direct-call.md), [matching-to-call](../flows/matching-to-call.md)

## Linked issues (heuristic, public subset)

- P071 [fixed] Cổng gọi điện có thể bypass sau khi block (bắt được khi tự review)

See ../issues/index.md.
