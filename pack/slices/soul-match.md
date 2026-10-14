# Feature slice: soul-match

id: slice:soul-match
status: implemented; owner: core-api/soul-match

Anonymous text match, rating and friendship unlock

This file follows ONE feature through every layer. Layers are joined by the actual endpoint calls found in the frontend code, so it shows what the code does, not what a plan says.

## 1. Product contract and docs

- registry contracts: openapi/core-api.json, docs/services/soul-match-service.md, docs/services/realtime-gateway.md
- registry verification tests: apps/core-api/src/modules/soul-match/soul-match.integration.spec.ts
- service docs: docs/services/soul-match-service.md

## 2. Frontend

| route | app | endpoints of this feature it calls | page |
|---|---|---|---|
| [/matching/soul/:sessionId](../fe/route-web-matching-soul-sessionid.md) | web | GET /soul-match/sessions/{id}; GET /soul-match/sessions/{id}/messages; GET /soul-match/sessions/{id}/partner; POST /soul-match/sessions/{id}/end; POST /soul-match/sessions/{id}/messages; POST /soul-match/sessions/{id}/rating | app/(app)/matching/soul/[sessionId]/page.tsx |

FE features (directories): [web/soul-match](../fe/feature-web-soul-match.md)

User actions that mutate this feature's data:
- useSendSoulMessage -> POST /soul-match/sessions/{id}/messages (apps/web/src/features/soul-match/components/soul-message-composer.tsx:39)
- useRateSession -> POST /soul-match/sessions/{id}/rating (apps/web/src/features/soul-match/components/soul-rating-buttons.tsx:55)

Realtime events the frontend listens to for this feature:
- soul.message in /matching/soul/:sessionId (apps/web/src/features/soul-match/components/soul-chat-phase-view.tsx:71)
- soul.matched in /matching/soul/:sessionId (apps/web/src/features/soul-match/components/soul-chat-phase-view.tsx:78)
- soul.ended in /matching/soul/:sessionId (apps/web/src/features/soul-match/components/soul-chat-phase-view.tsx:85)

## 3. API

| endpoint | auth | idempotency | throttle | FE callers |
|---|---|---|---|---|
| GET /soul-match/sessions/{id} ([soul-match](../api/soul-match.md)) | JWT |  |  | 1 |
| GET /soul-match/sessions/{id}/messages ([soul-match](../api/soul-match.md)) | JWT |  |  | 0 |
| POST /soul-match/sessions/{id}/messages ([soul-match](../api/soul-match.md)) | JWT | required | { default: { limit: 60, ttl: minutes(1) } } | 0 |
| POST /soul-match/sessions/{id}/rating ([soul-match](../api/soul-match.md)) | JWT |  |  | 0 |
| POST /soul-match/sessions/{id}/end ([soul-match](../api/soul-match.md)) | JWT |  |  | 1 |
| GET /soul-match/sessions/{id}/partner ([soul-match](../api/soul-match.md)) | JWT |  |  | 0 |


## 4. Backend flows (service map)

- [soul-match-room-reads-and-reveal](../flows/soul-match-room-reads-and-reveal.md): Đọc phòng Soul Match và lộ hồ sơ sau mutual-like (3 endpoints of this feature)
- [soul-match-session](../flows/soul-match-session.md): Phòng chat ẩn danh Soul Match và mutual-like (2 endpoints of this feature)
- [soul-match-send-message](../flows/soul-match-send-message.md): Gửi tin nhắn ẩn danh trong phòng Soul Match (1 endpoints of this feature)

## 5. Data

- tables owned by the module(s): `soul_chat_messages`, `soul_match_ratings` (see ../db/soul-match.md)
- status vocabularies enforced by CHECK:
  - soul_match_ratings: rude | boring | like

## 6. Events

- soul.message: producers soul-match.sendMessage; FE consumers 1
- soul.matched: producers soul-match.rate; FE consumers 1
- soul.ended: producers soul-match.endSession; FE consumers 1

## 7. Errors

- error code groups: [SoulMatchErrors](../errors/soulmatcherrors.md)
- codes the FE references for this feature: none

## 8. Configuration

env vars read by the module(s): `REDIS_URL`, `SOUL_CHAT_MESSAGE_MAX_LENGTH`, `SOUL_CHAT_DURATION_SECONDS`, `SOUL_RATING_WINDOW_SECONDS` (see ../config/)

## 9. Tests

- soul-match: 1 unit spec files, 1 integration spec files
- e2e files that call these endpoints: none
- FE specs: apps/web/src/features/soul-match/api.spec.ts, apps/web/src/features/soul-match/components/soul-chat-phase-view.spec.tsx, apps/web/src/features/soul-match/components/soul-countdown-badge.spec.tsx, apps/web/src/features/soul-match/send-message-schema.spec.ts

## 10. Known issues (linked from the ledger)

- P006 [fixed] Swagger thiếu cursor pagination → client sinh kiểu never

See ../issues/index.md.
