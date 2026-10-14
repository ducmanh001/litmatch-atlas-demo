# Feature slice: matching

id: slice:matching
status: implemented; owner: core-api/matching

Queue ticket, pairing, guest quota and paid speed-up

This file follows ONE feature through every layer. Layers are joined by the actual endpoint calls found in the frontend code, so it shows what the code does, not what a plan says.

## 1. Product contract and docs

- registry contracts: openapi/core-api.json, docs/services/matching-service.md, docs/06-domain-rules.md
- registry verification tests: apps/core-api/src/modules/matching/matching.integration.spec.ts
- service docs: docs/services/matching-service.md

## 2. Frontend

| route | app | endpoints of this feature it calls | page |
|---|---|---|---|
| [/matching](../fe/route-web-matching.md) | web | GET /matching/invites; GET /matching/tickets/current; GET /matching/tickets/{id}; POST /matching/invites/{id}/accept; POST /matching/invites/{id}/decline; DELETE /matching/tickets/{id} … | app/(app)/matching/page.tsx |

FE features (directories): [web/matching](../fe/feature-web-matching.md), [web/notification](../fe/feature-web-notification.md), [web/wallet](../fe/feature-web-wallet.md)

UI action map card for /matching: Queue actions: Chọn Soul hoặc Voice / Chọn giới tính mong muốn / Bắt đầu tìm kiếm / Huỷ tìm kiếm | Session actions: Soul → Chat ẩn danh / Voice → LiveKit / microphone / Thích / Bỏ qua / Match → Friends / Chat

User actions that mutate this feature's data:
- useDeclineInvite -> POST /matching/invites/{id}/decline (apps/web/src/features/matching/components/incoming-invites.tsx:141)
- useAcceptInvite -> POST /matching/invites/{id}/accept (apps/web/src/features/matching/components/incoming-invites.tsx:151)
- useConfirmTicket -> POST /matching/tickets/{id}/confirm (apps/web/src/features/matching/components/queue-status-panel.tsx:143)
- useCancelTicket -> DELETE /matching/tickets/{id} (apps/web/src/features/matching/components/queue-status-panel.tsx:158)
- useCancelTicket -> DELETE /matching/tickets/{id} (apps/web/src/features/matching/components/queue-status-panel.tsx:326)
- useJoinQueue -> POST /matching/tickets (apps/web/src/features/matching/components/match-type-picker.tsx:69)
- useJoinQueue -> POST /matching/tickets (apps/web/src/features/matching/components/direct-queue-start.tsx:30)
- useSpeedup -> POST /matching/tickets/{id}/speedup (apps/web/src/features/matching/components/speedup-button.tsx:37)

Realtime events the frontend listens to for this feature:
- match.confirmed in /matching (apps/web/src/features/matching/components/queue-status-panel.tsx:102)

## 3. API

| endpoint | auth | idempotency | throttle | FE callers |
|---|---|---|---|---|
| POST /matching/invites ([matching](../api/matching.md)) | JWT |  | { default: { limit: 10, ttl: minutes(1) } } | 1 |
| GET /matching/invites ([matching](../api/matching.md)) | JWT |  |  | 1 |
| GET /matching/invites/{id} ([matching](../api/matching.md)) | JWT |  |  | 0 |
| POST /matching/invites/{id}/accept ([matching](../api/matching.md)) | JWT |  |  | 1 |
| POST /matching/invites/{id}/decline ([matching](../api/matching.md)) | JWT |  |  | 0 |
| POST /matching/invites/{id}/cancel ([matching](../api/matching.md)) | JWT |  |  | 0 |
| POST /matching/tickets ([matching](../api/matching.md)) | JWT | required | { default: { limit: 10, ttl: minutes(1) } } | 1 |
| GET /matching/tickets/current ([matching](../api/matching.md)) | JWT |  |  | 1 |
| GET /matching/tickets/{id} ([matching](../api/matching.md)) | JWT |  |  | 1 |
| DELETE /matching/tickets/{id} ([matching](../api/matching.md)) | JWT |  |  | 1 |
| POST /matching/tickets/{id}/confirm ([matching](../api/matching.md)) | JWT |  |  | 0 |
| POST /matching/tickets/{id}/speedup ([matching](../api/matching.md)) | JWT | required |  | 0 |


## 4. Backend flows (service map)

- [matching-ticket-status-cancel-speedup](../flows/matching-ticket-status-cancel-speedup.md): Xem, huỷ và tăng tốc vé ghép đôi (4 endpoints of this feature)
- [matching-invite-create](../flows/matching-invite-create.md): Mời chơi có chủ đích: tạo và xem lời mời (3 endpoints of this feature)
- [matching-invite-decline-cancel](../flows/matching-invite-decline-cancel.md): Từ chối hoặc huỷ lời mời (2 endpoints of this feature)
- [matching-to-call](../flows/matching-to-call.md): Từ hàng đợi ghép đôi đến cuộc gọi thoại (2 endpoints of this feature)
- [matching-invite-accept](../flows/matching-invite-accept.md): Chấp nhận lời mời và tạo phiên ghép (1 endpoints of this feature)

## 5. Data

- tables owned by the module(s): `guest_match_quotas`, `match_daily_quotas`, `match_invites`, `match_sessions`, `match_tickets` (see ../db/matching.md)
- status vocabularies enforced by CHECK:
  - match_daily_quotas: soul | voice
  - match_invites: soul | voice
  - match_invites: pending | accepted | declined | expired | cancelled
  - match_sessions: soul | voice
  - match_sessions: pending_confirm | confirmed | ended | expired
  - match_tickets: any | male | female
  - match_tickets: soul | voice
  - match_tickets: queued | matched | confirmed | expired | cancelled

## 6. Events

- match.confirmed: producers matching.tryPair, matching.confirmTicket, matching.acceptInvite; FE consumers 1

## 7. Errors

- error code groups: [MatchingErrors](../errors/matchingerrors.md)
- codes the FE references for this feature: none

## 8. Configuration

env vars read by the module(s): `MATCHING_INVITE_SWEEPER_INTERVAL_MS`, `MATCHING_MATCHER_INTERVAL_MS`, `MATCHING_MATCHER_BATCH_SIZE`, `MATCHING_SWEEPER_INTERVAL_MS`, `MATCHING_QUEUE_MAX_WAIT_SECONDS`, `MATCHING_CONFIRM_TIMEOUT_SECONDS`, `MATCHING_SPEEDUP_PRICE_DIAMOND`, `MATCHING_SVIP_SPEEDUP_PRICE_DIAMOND`, `MATCHING_VIP_SPEEDUP_PRICE_DIAMOND`, `MATCHING_EXTRA_SOUL_PRICE_DIAMOND`, `MATCHING_EXTRA_VOICE_PRICE_DIAMOND`, `MATCHING_PRIORITY_BOOST_MS`, `MATCHING_AGE_BAND_SIZE`, `MATCHING_TRUST_PENALTY_MS_PER_POINT`, `MATCHING_TRUST_PENALTY_MAX_MS`, `REDIS_URL`, `MATCHING_GUEST_DAILY_LIMIT`, `MATCHING_SVIP_DAILY_SOUL_LIMIT`, `MATCHING_SVIP_DAILY_VOICE_LIMIT`, `MATCHING_VIP_DAILY_SOUL_LIMIT`, `MATCHING_VIP_DAILY_VOICE_LIMIT`, `MATCHING_DAILY_SOUL_LIMIT`, `MATCHING_DAILY_VOICE_LIMIT`, `MATCHING_GUEST_QUOTA_PEPPER`, `MATCHING_INVITE_RATE_LIMIT_PER_HOUR`, `MATCHING_INVITE_TTL_SECONDS` (see ../config/)

## 9. Tests

- matching: 6 unit spec files, 1 integration spec files
- e2e files that call these endpoints: apps/web/e2e/login-and-join-matching-queue.spec.ts
- FE specs: apps/web/src/features/matching/api.spec.ts, apps/web/src/features/matching/components/incoming-invites.spec.tsx, apps/web/src/features/matching/components/match-actions.spec.tsx, apps/web/src/features/matching/components/queue-status-panel.spec.tsx, apps/web/src/features/matching/components/speedup-button.spec.tsx, apps/web/src/features/matching/join-queue-schema.spec.ts, apps/web/src/features/notification/api.spec.ts, apps/web/src/features/notification/components/notification-bell.spec.tsx, apps/web/src/features/notification/notification-copy.spec.ts, apps/web/src/features/wallet/components/payos-payment-status.spec.tsx, apps/web/src/features/wallet/components/topup-packages.spec.tsx, apps/web/src/features/wallet/components/vip-plans.spec.tsx

## 10. Known issues (linked from the ledger)

none linked

See ../issues/index.md.
