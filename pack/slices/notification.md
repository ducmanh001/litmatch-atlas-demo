# Feature slice: notification

id: slice:notification
status: implemented; owner: core-api/notification

In-process notification dispatch with isolated push and analytics adapters

This file follows ONE feature through every layer. Layers are joined by the actual endpoint calls found in the frontend code, so it shows what the code does, not what a plan says.

## 1. Product contract and docs

- registry contracts: openapi/core-api.json, docs/services/notification-service.md
- registry verification tests: apps/core-api/src/modules/notification/notification.service.spec.ts, apps/core-api/src/common/platform/platform.adapters.spec.ts
- service docs: docs/services/notification-service.md

## 2. Frontend

| route | app | endpoints of this feature it calls | page |
|---|---|---|---|
| [/discovery](../fe/route-web-discovery.md) | web | GET /notifications; GET /notifications/unread-count; POST /notifications/{notificationId}/read; POST /notifications/web-push/subscription; DELETE /notifications/web-push/subscription | app/(app)/discovery/page.tsx |
| [/feed](../fe/route-web-feed.md) | web | GET /notifications; GET /notifications/unread-count; POST /notifications/{notificationId}/read; POST /notifications/web-push/subscription; DELETE /notifications/web-push/subscription | app/(app)/feed/page.tsx |
| [/home](../fe/route-web-home.md) | web | GET /notifications; GET /notifications/unread-count; POST /notifications/{notificationId}/read; POST /notifications/web-push/subscription; DELETE /notifications/web-push/subscription | app/(app)/home/page.tsx |
| [/matching](../fe/route-web-matching.md) | web | GET /notifications; GET /notifications/unread-count; POST /notifications/{notificationId}/read; POST /notifications/web-push/subscription; DELETE /notifications/web-push/subscription | app/(app)/matching/page.tsx |
| [/messages](../fe/route-web-messages.md) | web | GET /notifications; GET /notifications/unread-count; POST /notifications/{notificationId}/read; POST /notifications/web-push/subscription; DELETE /notifications/web-push/subscription | app/(app)/messages/page.tsx |
| [/more](../fe/route-web-more.md) | web | GET /notifications; GET /notifications/unread-count; POST /notifications/{notificationId}/read; POST /notifications/web-push/subscription; DELETE /notifications/web-push/subscription | app/(app)/more/page.tsx |
| [/party](../fe/route-web-party.md) | web | GET /notifications; GET /notifications/unread-count; POST /notifications/{notificationId}/read; POST /notifications/web-push/subscription; DELETE /notifications/web-push/subscription | app/(app)/party/page.tsx |
| [/profile](../fe/route-web-profile.md) | web | GET /notifications; GET /notifications/unread-count; POST /notifications/{notificationId}/read; POST /notifications/web-push/subscription; DELETE /notifications/web-push/subscription | app/(app)/profile/page.tsx |
| [/wallet](../fe/route-web-wallet.md) | web | GET /notifications; GET /notifications/unread-count; POST /notifications/{notificationId}/read; POST /notifications/web-push/subscription; DELETE /notifications/web-push/subscription | app/(app)/wallet/page.tsx |

FE features (directories): [web/discovery](../fe/feature-web-discovery.md), [web/notification](../fe/feature-web-notification.md), [web/wallet](../fe/feature-web-wallet.md), [web/feed](../fe/feature-web-feed.md), [web/party-room](../fe/feature-web-party-room.md), [web/matching](../fe/feature-web-matching.md), [web/friend-chat](../fe/feature-web-friend-chat.md), [web/profile](../fe/feature-web-profile.md)

UI action map card for /discovery: Browse actions: Lọc mọi người / Lọc giới tính / Lọc độ tuổi / Quanh đây / vị trí | Profile actions: Mở hồ sơ → Public Profile / Mời Soul Match / Mời Voice Match / Quay lại Home / Matching

User actions that mutate this feature's data:
- useMarkNotificationRead -> POST /notifications/{notificationId}/read (apps/web/src/features/notification/components/notification-bell.tsx:40)

## 3. API

| endpoint | auth | idempotency | throttle | FE callers |
|---|---|---|---|---|
| GET /notifications ([notification](../api/notification.md)) | JWT |  |  | 1 |
| GET /notifications/unread-count ([notification](../api/notification.md)) | JWT |  |  | 1 |
| POST /notifications/web-push/subscription ([notification](../api/notification.md)) | JWT |  |  | 1 |
| DELETE /notifications/web-push/subscription ([notification](../api/notification.md)) | JWT |  |  | 1 |
| POST /notifications/{notificationId}/read ([notification](../api/notification.md)) | JWT |  |  | 1 |


## 4. Backend flows (service map)

- [message-push-notification](../flows/message-push-notification.md): Tin nhắn bạn bè: realtime và Web Push (1 endpoints of this feature)

## 5. Data

- tables owned by the module(s): `notifications`, `web_push_subscriptions` (see ../db/notification.md)

## 6. Events

No realtime event is produced by these modules.

## 7. Errors

- error code groups: [NotificationErrors](../errors/notificationerrors.md)
- codes the FE references for this feature: none

## 8. Configuration

env vars read by the module(s): none (see ../config/)

## 9. Tests

- notification: 2 unit spec files, 0 integration spec files
- e2e files that call these endpoints: none
- FE specs: apps/web/src/app/(app)/discovery/page.spec.tsx, apps/web/src/features/discovery/components/discovery-detail-sheet.spec.tsx, apps/web/src/features/notification/api.spec.ts, apps/web/src/features/notification/components/notification-bell.spec.tsx, apps/web/src/features/notification/notification-copy.spec.ts, apps/web/src/features/wallet/components/payos-payment-status.spec.tsx, apps/web/src/features/wallet/components/topup-packages.spec.tsx, apps/web/src/features/wallet/components/vip-plans.spec.tsx, apps/web/src/features/wallet/components/wallet-balance.spec.tsx, apps/web/src/features/feed/api.spec.tsx, apps/web/src/features/feed/components/feed-banners.spec.tsx, apps/web/src/features/feed/components/like-button.spec.tsx

## 10. Known issues (public subset of the ledger)

- P006 [fixed] Swagger thiếu cursor pagination → client sinh kiểu never

See ../issues/index.md.
