# Feature slice: feed-stories

id: slice:feed-stories
status: implemented; owner: core-api/feed

Feed, reactions, comments and expiring stories

This file follows ONE feature through every layer. Layers are joined by the actual endpoint calls found in the frontend code, so it shows what the code does, not what a plan says.

## 1. Product contract and docs

- registry contracts: openapi/core-api.json, docs/services/feed-service.md
- registry verification tests: apps/core-api/src/modules/feed/feed.integration.spec.ts
- service docs: docs/services/feed-service.md

## 2. Frontend

| route | app | endpoints of this feature it calls | page |
|---|---|---|---|
| [/feed/:postId](../fe/route-web-feed-postid.md) | web | GET /feed/posts/{postId}; GET /feed/posts/{postId}/comments; GET /feed/posts/{postId}/reactions; POST /feed/posts/{postId}/comments; DELETE /feed/posts/{postId}; POST /feed/posts/{postId}/reactions … | app/(app)/feed/[postId]/page.tsx |
| [/feed](../fe/route-web-feed.md) | web | GET /feed/posts; GET /feed/posts/{postId}/reactions; DELETE /feed/posts/{postId}; POST /feed/posts; POST /feed/posts/{postId}/reactions; DELETE /feed/posts/{postId}/reactions | app/(app)/feed/page.tsx |
| [/profile](../fe/route-web-profile.md) | web | GET /feed/users/{userId}/posts; GET /feed/posts/{postId}/reactions; DELETE /feed/posts/{postId}; POST /feed/posts/{postId}/reactions; DELETE /feed/posts/{postId}/reactions | app/(app)/profile/page.tsx |
| [/users/:id](../fe/route-web-users-id.md) | web | GET /feed/users/{userId}/posts; GET /feed/posts/{postId}/reactions; DELETE /feed/posts/{postId}; POST /feed/posts/{postId}/reactions; DELETE /feed/posts/{postId}/reactions | app/(app)/users/[id]/page.tsx |

FE features (directories): [web/feed](../fe/feature-web-feed.md), [web/notification](../fe/feature-web-notification.md), [web/party-room](../fe/feature-web-party-room.md), [web/wallet](../fe/feature-web-wallet.md), [web/friend-chat](../fe/feature-web-friend-chat.md), [web/profile](../fe/feature-web-profile.md)

User actions that mutate this feature's data:
- useCreateComment -> POST /feed/posts/{postId}/comments (apps/web/src/features/feed/components/comment-composer.tsx:42)
- useDeletePost -> DELETE /feed/posts/{postId} (apps/web/src/features/feed/components/post-card.tsx:166)
- useCreatePost -> POST /feed/posts (apps/web/src/features/feed/components/post-composer.tsx:121)

## 3. API

| endpoint | auth | idempotency | throttle | FE callers |
|---|---|---|---|---|
| POST /stories ([feed](../api/feed.md)) | JWT | required |  | 0 |
| GET /stories/ring ([feed](../api/feed.md)) | JWT |  |  | 0 |
| GET /stories/{storyId} ([feed](../api/feed.md)) | JWT |  |  | 0 |
| GET /stories/{storyId}/viewers ([feed](../api/feed.md)) | JWT |  |  | 0 |
| POST /stories/{storyId}/reply ([feed](../api/feed.md)) | JWT | required |  | 0 |
| POST /feed/posts ([feed](../api/feed.md)) | JWT | required |  | 1 |
| GET /feed/posts ([feed](../api/feed.md)) | JWT |  |  | 1 |
| GET /feed/users/{userId}/posts ([feed](../api/feed.md)) | JWT |  |  | 1 |
| GET /feed/posts/{postId} ([feed](../api/feed.md)) | JWT |  |  | 1 |
| DELETE /feed/posts/{postId} ([feed](../api/feed.md)) | JWT |  |  | 1 |
| POST /feed/posts/{postId}/comments ([feed](../api/feed.md)) | JWT |  |  | 1 |
| GET /feed/posts/{postId}/comments ([feed](../api/feed.md)) | JWT |  |  | 1 |
| DELETE /feed/comments/{commentId} ([feed](../api/feed.md)) | JWT |  |  | 0 |
| POST /feed/posts/{postId}/reactions ([feed](../api/feed.md)) | JWT |  |  | 1 |
| DELETE /feed/posts/{postId}/reactions ([feed](../api/feed.md)) | JWT |  |  | 1 |
| GET /feed/posts/{postId}/reactions ([feed](../api/feed.md)) | JWT |  |  | 1 |


## 4. Backend flows (service map)

- [feed-reaction-with-notification](../flows/feed-reaction-with-notification.md): Reaction bài kèm thông báo (3 endpoints of this feature)
- [feed-read-posts](../flows/feed-read-posts.md): Đọc feed và bài đăng (3 endpoints of this feature)
- [story-ring-view-and-viewers](../flows/story-ring-view-and-viewers.md): Vòng story, xem story và người đã xem (3 endpoints of this feature)
- [feed-comment-with-notification](../flows/feed-comment-with-notification.md): Bình luận bài kèm thông báo (2 endpoints of this feature)
- [feed-delete-post](../flows/feed-delete-post.md): Xoá bài và bình luận (2 endpoints of this feature)
- [feed-post-with-image](../flows/feed-post-with-image.md): Đăng bài kèm ảnh: upload presigned rồi gắn asset (1 endpoints of this feature)
- [story-create-with-image](../flows/story-create-with-image.md): Đăng story kèm ảnh và dọn story hết hạn (1 endpoints of this feature)
- [story-reply-to-dm](../flows/story-reply-to-dm.md): Trả lời story thành tin nhắn (1 endpoints of this feature)

## 5. Data

- tables owned by the module(s): `comments`, `posts`, `reactions`, `stories`, `story_views` (see ../db/feed.md)
- status vocabularies enforced by CHECK:
  - posts: public | friends | only_me
  - stories: public | friends

## 6. Events

No realtime event is produced by these modules.

## 7. Errors

- error code groups: [FeedErrors](../errors/feederrors.md)
- codes the FE references for this feature: none

## 8. Configuration

env vars read by the module(s): `STORY_SWEEPER_INTERVAL_MS`, `STORY_TTL_HOURS` (see ../config/)

## 9. Tests

- feed: 2 unit spec files, 1 integration spec files
- e2e files that call these endpoints: none
- FE specs: apps/web/src/features/feed/api.spec.tsx, apps/web/src/features/feed/components/feed-banners.spec.tsx, apps/web/src/features/feed/components/like-button.spec.tsx, apps/web/src/features/feed/components/post-composer.spec.tsx, apps/web/src/features/feed/components/post-detail.spec.tsx, apps/web/src/features/feed/components/post-list.spec.tsx, apps/web/src/features/feed/create-comment-schema.spec.ts, apps/web/src/features/feed/create-post-schema.spec.ts, apps/web/src/features/friend-chat/api.spec.ts, apps/web/src/features/friend-chat/components/conversation-inbox.spec.tsx, apps/web/src/features/friend-chat/components/conversation-thread.spec.tsx, apps/web/src/features/friend-chat/components/friends-directory.spec.tsx

## 10. Known issues (public subset of the ledger)

- P006 [fixed] Swagger thiếu cursor pagination → client sinh kiểu never

See ../issues/index.md.
