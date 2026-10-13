# Feature slice: palm-match

id: slice:palm-match
status: implemented; owner: core-api/palm-match

Deterministic palm reading and anonymous palm match

This file follows ONE feature through every layer. Layers are joined by the actual endpoint calls found in the frontend code, so it shows what the code does, not what a plan says.

## 1. Product contract and docs

- registry contracts: openapi/core-api.json, docs/services/palm-match-service.md
- registry verification tests: apps/core-api/src/modules/palm-match/palm-match.integration.spec.ts
- service docs: docs/services/palm-match-service.md

## 2. Frontend

| route | app | endpoints of this feature it calls | page |
|---|---|---|---|
| [/palm-match](../fe/route-web-palm-match.md) | web | GET /palm-match/current; POST /palm-match/queue; POST /palm-match/sessions/{id}/flip; POST /palm-match/sessions/{id}/rating; DELETE /palm-match/current | app/(app)/palm-match/page.tsx |

FE features (directories): [web/palm-match](../fe/feature-web-palm-match.md)

User actions that mutate this feature's data:
- useJoinPalmQueue -> POST /palm-match/queue (apps/web/src/features/palm-match/components/palm-reading-view.tsx:64)
- useDismissPalmMatch -> DELETE /palm-match/current (apps/web/src/features/palm-match/components/palm-reading-view.tsx:77)
- useDismissPalmMatch -> DELETE /palm-match/current (apps/web/src/features/palm-match/components/palm-reading-view.tsx:86)
- useJoinPalmQueue -> POST /palm-match/queue (apps/web/src/features/palm-match/components/palm-reading-view.tsx:89)
- useRatePalmMatch -> POST /palm-match/sessions/{id}/rating (apps/web/src/features/palm-match/components/palm-reading-view.tsx:262)
- useRatePalmMatch -> POST /palm-match/sessions/{id}/rating (apps/web/src/features/palm-match/components/palm-reading-view.tsx:276)
- useFlipPalmCard -> POST /palm-match/sessions/{id}/flip (apps/web/src/features/palm-match/components/palm-reading-view.tsx:303)

## 3. API

| endpoint | auth | idempotency | throttle | FE callers |
|---|---|---|---|---|
| GET /palm-match/reading ([palm-match](../api/palm-match.md)) | JWT |  |  | 0 |
| POST /palm-match/queue ([palm-match](../api/palm-match.md)) | JWT |  |  | 1 |
| GET /palm-match/current ([palm-match](../api/palm-match.md)) | JWT |  |  | 1 |
| POST /palm-match/sessions/{id}/flip ([palm-match](../api/palm-match.md)) | JWT |  |  | 0 |
| POST /palm-match/sessions/{id}/rating ([palm-match](../api/palm-match.md)) | JWT |  |  | 0 |
| DELETE /palm-match/current ([palm-match](../api/palm-match.md)) | JWT |  |  | 1 |


## 4. Backend flows (service map)

- [palm-match-queue-and-rating](../flows/palm-match-queue-and-rating.md): Palm Match: vào hàng, lật và đánh giá (6 endpoints of this feature)

## 5. Data

- tables owned by the module(s): `palm_match_active_participants`, `palm_match_queue_entries`, `palm_match_sessions`, `palm_reading_templates` (see ../db/palm-match.md)
- status vocabularies enforced by CHECK:
  - palm_match_sessions: like | skip
  - palm_match_sessions: like | skip
  - palm_match_sessions: matched | not_matched | expired | cancelled
  - palm_match_sessions: active | completed
  - palm_reading_templates: love | career | health | general

## 6. Events

No realtime event is produced by these modules.

## 7. Errors

- error code groups: [PalmMatchErrors](../errors/palmmatcherrors.md)
- codes the FE references for this feature: none

## 8. Configuration

env vars read by the module(s): `PALM_MATCH_QUEUE_MAX_WAIT_SECONDS`, `PALM_MATCH_SESSION_DURATION_SECONDS`, `PALM_MATCH_TARGET_NAME_MAX_LENGTH` (see ../config/)

## 9. Tests

- palm-match: 1 unit spec files, 1 integration spec files
- e2e files that call these endpoints: none
- FE specs: apps/web/src/features/palm-match/api.spec.ts, apps/web/src/features/palm-match/components/palm-reading-view.spec.tsx

## 10. Known issues (public subset of the ledger)

- P094 [open] Giới hạn đã biết: advisory lock Palm/Movie Match, outbox chưa có consumer/retention

See ../issues/index.md.
