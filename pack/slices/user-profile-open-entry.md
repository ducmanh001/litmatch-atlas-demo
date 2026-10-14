# Feature slice: user-profile-open-entry

id: slice:user-profile-open-entry
status: implemented; owner: core-api/user

Optional profile birth date without an age-based access gate

This file follows ONE feature through every layer. Layers are joined by the actual endpoint calls found in the frontend code, so it shows what the code does, not what a plan says.

## 1. Product contract and docs

- registry contracts: openapi/core-api.json, docs/01-product-features.md, docs/06-domain-rules.md
- registry verification tests: apps/core-api/src/modules/user/user.service.spec.ts
- service docs: docs/services/user-service.md

## 2. Frontend

| route | app | endpoints of this feature it calls | page |
|---|---|---|---|
| [/privacy](../fe/route-web-privacy.md) | web | GET /users/me/privacy; GET /users/me; PUT /users/me/privacy | app/(public)/privacy/page.tsx |
| [/users/:id](../fe/route-web-users-id.md) | web | GET /users/{id}; GET /users/{id}/presence; GET /users/me | app/(app)/users/[id]/page.tsx |
| [/chat/:friendUserId](../fe/route-web-chat-frienduserid.md) | web | GET /users/{id}; GET /users/me | app/(app)/chat/[friendUserId]/page.tsx |
| [/feed](../fe/route-web-feed.md) | web | GET /users/{id}; GET /users/me | app/(app)/feed/page.tsx |
| [/movie-match/:sessionId](../fe/route-web-movie-match-sessionid.md) | web | GET /users/{id}; GET /users/me | app/(app)/movie-match/[sessionId]/page.tsx |
| [/profile/edit](../fe/route-web-profile-edit.md) | web | GET /users/me; PATCH /users/me | app/(app)/profile/edit/page.tsx |
| [/chat/:friendUserId/call](../fe/route-web-chat-frienduserid-call.md) | web | GET /users/{id} | app/(app)/chat/[friendUserId]/call/page.tsx |
| [/feed/:postId](../fe/route-web-feed-postid.md) | web | GET /users/me | app/(app)/feed/[postId]/page.tsx |
| [/friends](../fe/route-web-friends.md) | web | GET /users/me | app/(app)/friends/page.tsx |
| [/home](../fe/route-web-home.md) | web | GET /users/me | app/(app)/home/page.tsx |
| [/more](../fe/route-web-more.md) | web | GET /users/me | app/(app)/more/page.tsx |
| [/movie-match](../fe/route-web-movie-match.md) | web | GET /users/me | app/(app)/movie-match/page.tsx |

FE features (directories): [web/privacy](../fe/feature-web-privacy.md), [web/feed](../fe/feature-web-feed.md), [web/profile](../fe/feature-web-profile.md), [web/friend-chat](../fe/feature-web-friend-chat.md), [web/voice-match](../fe/feature-web-voice-match.md), [web/notification](../fe/feature-web-notification.md), [web/party-room](../fe/feature-web-party-room.md), [web/wallet](../fe/feature-web-wallet.md), [web/movie-match](../fe/feature-web-movie-match.md), [web/avatar](../fe/feature-web-avatar.md)

User actions that mutate this feature's data:
- useUpdatePrivacySettings -> PUT /users/me/privacy (apps/web/src/app/(public)/privacy/page.tsx:110)

## 3. API

| endpoint | auth | idempotency | throttle | FE callers |
|---|---|---|---|---|
| GET /users/me ([user](../api/user.md)) | JWT |  |  | 1 |
| PATCH /users/me ([user](../api/user.md)) | JWT |  |  | 1 |
| GET /users/me/privacy ([user](../api/user.md)) | JWT |  |  | 1 |
| PUT /users/me/privacy ([user](../api/user.md)) | JWT |  |  | 1 |
| GET /users/{id}/presence ([user](../api/user.md)) | JWT |  |  | 1 |
| GET /users/{id} ([user](../api/user.md)) | JWT |  |  | 3 |


## 4. Backend flows (service map)

- [user-presence-read](../flows/user-presence-read.md): Xem trạng thái online của người dùng (1 endpoints of this feature)

## 5. Data

- tables owned by the module(s): `user_privacy_settings`, `users` (see ../db/user.md)

## 6. Events

No realtime event is produced by these modules.

## 7. Errors

- error code groups: [UserErrors](../errors/usererrors.md)
- codes the FE references for this feature: none

## 8. Configuration

env vars read by the module(s): `REDIS_URL`, `USER_DEFAULT_AVATAR_ID` (see ../config/)

## 9. Tests

- user: 4 unit spec files, 0 integration spec files
- e2e files that call these endpoints: none
- FE specs: apps/web/src/app/(public)/privacy/page.spec.tsx, apps/web/src/features/feed/api.spec.tsx, apps/web/src/features/feed/components/feed-banners.spec.tsx, apps/web/src/features/feed/components/like-button.spec.tsx, apps/web/src/features/feed/components/post-composer.spec.tsx, apps/web/src/features/feed/components/post-detail.spec.tsx, apps/web/src/features/feed/components/post-list.spec.tsx, apps/web/src/features/feed/create-comment-schema.spec.ts, apps/web/src/features/feed/create-post-schema.spec.ts, apps/web/src/features/profile/components/follow-list-view.spec.tsx, apps/web/src/features/profile/components/profile-stats.spec.tsx, apps/web/src/features/profile/components/profile-view.spec.tsx

## 10. Known issues (linked from the ledger)

none linked

See ../issues/index.md.
