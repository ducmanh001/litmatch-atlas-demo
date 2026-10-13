# Feature slice: safety

id: slice:safety
status: implemented; owner: core-api/safety

Report, block and trust-safety decisions

This file follows ONE feature through every layer. Layers are joined by the actual endpoint calls found in the frontend code, so it shows what the code does, not what a plan says.

## 1. Product contract and docs

- registry contracts: openapi/core-api.json, docs/services/safety-service.md, docs/06-domain-rules.md
- registry verification tests: apps/core-api/src/modules/safety/safety.service.spec.ts
- service docs: docs/services/safety-service.md

## 2. Frontend

| route | app | endpoints of this feature it calls | page |
|---|---|---|---|
| [/chat/:friendUserId](../fe/route-web-chat-frienduserid.md) | web | POST /safety/blocks/{targetUserId}; POST /safety/reports | app/(app)/chat/[friendUserId]/page.tsx |

FE features (directories): [web/friend-chat](../fe/feature-web-friend-chat.md), [web/voice-match](../fe/feature-web-voice-match.md)

UI action map card for /chat/:friendUserId: : Gửi text / ảnh / Mark read / mute / Gọi voice → /call / Movie Match với bạn / Notification khi có message

User actions that mutate this feature's data:
- useBlockUser -> POST /safety/blocks/{targetUserId} (apps/web/src/features/friend-chat/components/conversation-thread.tsx:199)
- useReportUser -> POST /safety/reports (apps/web/src/features/friend-chat/components/conversation-thread.tsx:226)

## 3. API

| endpoint | auth | idempotency | throttle | FE callers |
|---|---|---|---|---|
| POST /safety/reports ([safety](../api/safety.md)) | JWT |  | { default: { limit: 10, ttl: minutes(60) } } | 1 |
| POST /safety/blocks/{targetUserId} ([safety](../api/safety.md)) | JWT |  |  | 1 |
| DELETE /safety/blocks/{targetUserId} ([safety](../api/safety.md)) | JWT |  |  | 0 |
| GET /safety/blocks/{targetUserId} ([safety](../api/safety.md)) | JWT |  |  | 0 |


## 4. Backend flows (service map)

- [safety-block-and-unblock](../flows/safety-block-and-unblock.md): Chặn và bỏ chặn người dùng (3 endpoints of this feature)
- [safety-report-and-trust-score](../flows/safety-report-and-trust-score.md): Báo cáo người dùng và trừ điểm tin cậy (1 endpoints of this feature)

## 5. Data

- tables owned by the module(s): `blocks`, `reports` (see ../db/safety.md)
- status vocabularies enforced by CHECK:
  - reports: user | video

## 6. Events

No realtime event is produced by these modules.

## 7. Errors

- error code groups: [SafetyErrors](../errors/safetyerrors.md)
- codes the FE references for this feature: none

## 8. Configuration

env vars read by the module(s): `SAFETY_REPORT_COOLDOWN_DAYS`, `SAFETY_TRUST_PENALTY_PER_REPORT`, `SAFETY_TRUST_PENALTY_DAILY_CAP`, `SAFETY_TRUST_SCORE_FLOOR`, `SAFETY_REMATCH_COOLDOWN_DAYS` (see ../config/)

## 9. Tests

- safety: 1 unit spec files, 0 integration spec files
- e2e files that call these endpoints: none
- FE specs: apps/web/src/features/friend-chat/api.spec.ts, apps/web/src/features/friend-chat/components/conversation-inbox.spec.tsx, apps/web/src/features/friend-chat/components/conversation-thread.spec.tsx, apps/web/src/features/friend-chat/components/friends-directory.spec.tsx, apps/web/src/features/friend-chat/components/message-list.spec.tsx, apps/web/src/features/friend-chat/send-message-schema.spec.ts, apps/web/src/features/matching/api.spec.ts, apps/web/src/features/matching/components/incoming-invites.spec.tsx, apps/web/src/features/matching/components/match-actions.spec.tsx, apps/web/src/features/matching/components/queue-status-panel.spec.tsx, apps/web/src/features/matching/components/speedup-button.spec.tsx, apps/web/src/features/matching/join-queue-schema.spec.ts

## 10. Known issues (public subset of the ledger)

none linked

See ../issues/index.md.
