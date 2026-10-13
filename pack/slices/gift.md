# Feature slice: gift

id: slice:gift
status: implemented; owner: core-api/gift

Party-room gifts through Economy

This file follows ONE feature through every layer. Layers are joined by the actual endpoint calls found in the frontend code, so it shows what the code does, not what a plan says.

## 1. Product contract and docs

- registry contracts: openapi/core-api.json, docs/services/gift-service.md, docs/services/economy-service.md
- registry verification tests: apps/core-api/src/modules/gift/gift.integration.spec.ts
- service docs: docs/services/gift-service.md

## 2. Frontend

| route | app | endpoints of this feature it calls | page |
|---|---|---|---|
| [/party/:roomId](../fe/route-web-party-roomid.md) | web | GET /gifts; POST /party/rooms/{roomId}/gifts | app/(app)/party/[roomId]/page.tsx |
| [/users/:id](../fe/route-web-users-id.md) | web | GET /gifts; POST /profiles/{profileUserId}/gifts | app/(app)/users/[id]/page.tsx |
| [/video](../fe/route-web-video.md) | web | GET /gifts; POST /videos/{videoId}/gifts | app/(app)/video/page.tsx |

FE features (directories): [web/party-room](../fe/feature-web-party-room.md), [web/feed](../fe/feature-web-feed.md), [web/profile](../fe/feature-web-profile.md), [web/short-video](../fe/feature-web-short-video.md)

User actions that mutate this feature's data:
- useSendGift -> POST /party/rooms/{roomId}/gifts (apps/web/src/features/party-room/components/gift-panel.tsx:76)
- useSendProfileGift -> POST /profiles/{profileUserId}/gifts (apps/web/src/features/profile/components/public-profile-view.tsx:110)
- useSendVideoGift -> POST /videos/{videoId}/gifts (apps/web/src/features/short-video/components/video-gift-sheet.tsx:65)

Realtime events the frontend listens to for this feature:
- gift.sent in /party/:roomId (apps/web/src/features/party-room/components/party-stage.tsx:177)

## 3. API

| endpoint | auth | idempotency | throttle | FE callers |
|---|---|---|---|---|
| GET /gifts ([gift](../api/gift.md)) | JWT |  |  | 2 |
| POST /party/rooms/{roomId}/gifts ([gift](../api/gift.md)) | JWT | required | { default: { limit: 30, ttl: minutes(1) } } | 1 |
| POST /videos/{videoId}/gifts ([gift](../api/gift.md)) | JWT | required | { default: { limit: 30, ttl: minutes(1) } } | 1 |
| POST /profiles/{profileUserId}/gifts ([gift](../api/gift.md)) | JWT | required | { default: { limit: 30, ttl: minutes(1) } } | 0 |


## 4. Backend flows (service map)

- [admin-gift-catalog](../flows/admin-gift-catalog.md): Admin quản lý danh mục quà (1 endpoints of this feature)
- [gift-in-party-room](../flows/gift-in-party-room.md): Tặng quà trong phòng party (1 endpoints of this feature)
- [gift-on-video](../flows/gift-on-video.md): Tặng quà cho tác giả video (1 endpoints of this feature)
- [gift-to-profile-chat-gate](../flows/gift-to-profile-chat-gate.md): Tặng quà từ hồ sơ để mở chat (cổng quà) (1 endpoints of this feature)

## 5. Data

- tables owned by the module(s): `gift_events`, `gifts` (see ../db/gift.md)

## 6. Events

- gift.sent: producers gift.publishGiftSent; FE consumers 1

## 7. Errors

- error code groups: [GiftErrors](../errors/gifterrors.md)
- codes the FE references for this feature: none

## 8. Configuration

env vars read by the module(s): `GIFT_POINTS_RATE_PERCENT`, `REDIS_URL` (see ../config/)

## 9. Tests

- gift: 0 unit spec files, 1 integration spec files
- e2e files that call these endpoints: apps/core-api-e2e/src/core-api/livekit-webhook-content-type.spec.ts
- FE specs: apps/web/src/features/friend-chat/api.spec.ts, apps/web/src/features/friend-chat/components/conversation-inbox.spec.tsx, apps/web/src/features/friend-chat/components/conversation-thread.spec.tsx, apps/web/src/features/friend-chat/components/friends-directory.spec.tsx, apps/web/src/features/friend-chat/components/message-list.spec.tsx, apps/web/src/features/friend-chat/send-message-schema.spec.ts, apps/web/src/features/party-room/api.spec.ts, apps/web/src/features/party-room/components/member-list.spec.tsx, apps/web/src/features/party-room/components/party-chat.spec.tsx, apps/web/src/features/party-room/components/party-stage.spec.tsx, apps/web/src/features/party-room/components/room-list.spec.tsx, apps/web/src/features/party-room/create-room-schema.spec.ts

## 10. Known issues (public subset of the ledger)

none linked

See ../issues/index.md.
