# web feature feed

id: fe:feature:web:feed
directory: apps/web/src/features/feed/ (12 source files, 9 components, 8 spec files)

routes whose data comes from this feature: [/feed](route-web-feed.md), [/feed/:postId](route-web-feed-postid.md), [/profile](route-web-profile.md), [/users/:id](route-web-users-id.md)

## Hooks (data layer)

| hook | endpoint(s) | query key | polling | stale | invalidates on success | defined at |
|---|---|---|---|---|---|---|
| useFeed | GET /feed/posts | feedKeys.list |  |  |  | apps/web/src/features/feed/api.ts:29 |
| useUserTimeline | GET /feed/users/{userId}/posts | feedKeys.userTimeline(userId) |  |  |  | apps/web/src/features/feed/api.ts:44 |
| useCreatePost | POST /feed/posts |  |  |  | invalidateQueries({ queryKey: feedKeys.list }) | apps/web/src/features/feed/api.ts:63 |
| useDeletePost | DELETE /feed/posts/{postId} |  |  |  | removeQueries({ queryKey: feedKeys.detail(postId), exact: true, }); removeQueries({ queryKey: feedKeys.comments(postId), exact: true, }); removeQueries({ queryKey: feedKeys.reaction(postId), exact: true, }); invalidateQueries({ queryKey: feedKeys.list }) | apps/web/src/features/feed/api.ts:82 |
| usePost | GET /feed/posts/{postId} | feedKeys.detail(postId) |  |  |  | apps/web/src/features/feed/api.ts:108 |
| useComments | GET /feed/posts/{postId}/comments | feedKeys.comments(postId) |  |  |  | apps/web/src/features/feed/api.ts:120 |
| useCreateComment | POST /feed/posts/{postId}/comments |  |  |  | invalidateQueries({ queryKey: feedKeys.comments(postId), }); invalidateQueries({ queryKey: feedKeys.detail(postId), }); invalidateQueries({ queryKey: feedKeys.list }) | apps/web/src/features/feed/api.ts:137 |
| useReactionStatus | GET /feed/posts/{postId}/reactions | feedKeys.reaction(postId) |  |  |  | apps/web/src/features/feed/api.ts:159 |
| useSetReaction | POST /feed/posts/{postId}/reactions, DELETE /feed/posts/{postId}/reactions |  |  |  | invalidateQueries({ queryKey: feedKeys.reaction(postId), }); invalidateQueries({ queryKey: feedKeys.list }); invalidateQueries({ queryKey: feedKeys.detail(postId), }) | apps/web/src/features/feed/api.ts:171 |
| useLike | (none directly) |  |  |  |  | apps/web/src/features/feed/api.ts:196 |
| useUnlike | (none directly) |  |  |  |  | apps/web/src/features/feed/api.ts:200 |

## Components

- apps/web/src/features/feed/components/comment-composer.tsx: 1 interactive elements, uses useForm, useCreateComment, useCurrentUser, checks isGuest
- apps/web/src/features/feed/components/comment-list.tsx: 0 interactive elements, uses useComments
- apps/web/src/features/feed/components/feed-banners.tsx: 2 interactive elements, uses useRef
- apps/web/src/features/feed/components/like-button.tsx: 1 interactive elements, uses useReactionStatus, useLike, useUnlike, useCurrentUser, checks isGuest
- apps/web/src/features/feed/components/post-author-avatar.tsx: 0 interactive elements, uses no hooks
- apps/web/src/features/feed/components/post-card.tsx: 5 interactive elements, uses useState, useDeletePost, useId, useRef, useEffect, useCurrentUser, useLocale
- apps/web/src/features/feed/components/post-composer.tsx: 4 interactive elements, uses useForm, useCreatePost, useIdempotencyKey, useCurrentUser, useState, checks isGuest
- apps/web/src/features/feed/components/post-detail.tsx: 0 interactive elements, uses usePost
- apps/web/src/features/feed/components/post-list.tsx: 0 interactive elements, uses useFeed, useRef, useEffect

## Validation schemas

- createCommentSchema (apps/web/src/features/feed/create-comment-schema.ts:4): content=z.string().trim().min(1, 'Nhập nội dung bình luận').max(1000)

## Tests

- apps/web/src/features/feed/api.spec.tsx
- apps/web/src/features/feed/components/feed-banners.spec.tsx
- apps/web/src/features/feed/components/like-button.spec.tsx
- apps/web/src/features/feed/components/post-composer.spec.tsx
- apps/web/src/features/feed/components/post-detail.spec.tsx
- apps/web/src/features/feed/components/post-list.spec.tsx
- apps/web/src/features/feed/create-comment-schema.spec.ts
- apps/web/src/features/feed/create-post-schema.spec.ts
