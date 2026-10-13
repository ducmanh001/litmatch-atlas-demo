# web route /feed/:postId

id: fe:route:web:/feed/:postId
file: apps/web/src/app/(app)/feed/[postId]/page.tsx
layout: app (login required: AppChromeWithAuth)
title: Bài viết
reached source files: 23 (static import closure); data features: feed

Access: every route in the (app) group renders inside AppChromeWithAuth (AuthGate): no session -> redirected to /login. Guests can log in with a device id but several actions are limited (see "Guest and capability gating").

## Data it reads (GET)

| endpoint | hook | query key | polling | stale time | module | defined at |
|---|---|---|---|---|---|---|
| GET /feed/posts/{postId} | usePost | feedKeys.detail(postId) |  |  | [feed](../api/feed.md) | apps/web/src/features/feed/api.ts:112 |
| GET /users/me | useCurrentUser | currentUserKey |  | Infinity | [user](../api/user.md) | apps/web/src/shared/auth/use-current-user.ts:20 |
| GET /feed/posts/{postId}/comments | useComments | feedKeys.comments(postId) |  |  | [feed](../api/feed.md) | apps/web/src/features/feed/api.ts:124 |
| GET /feed/posts/{postId}/reactions | useReactionStatus | feedKeys.reaction(postId) |  |  | [feed](../api/feed.md) | apps/web/src/features/feed/api.ts:163 |

## Actions that change data (mutations)

| UI element and label | handler | hook | endpoint | module | call site |
|---|---|---|---|---|---|
| (no matching UI element found; called from CommentComposer) |  | useCreateComment | POST /feed/posts/{postId}/comments | [feed](../api/feed.md) | apps/web/src/features/feed/components/comment-composer.tsx:42 |
| (no matching UI element found; called from onDelete) |  | useDeletePost | DELETE /feed/posts/{postId} | [feed](../api/feed.md) | apps/web/src/features/feed/components/post-card.tsx:166 |
| button.onClick: guest           ? `Tài khoản khách không thể thích bài viết, ${likeCoun | () => (liked ? unlike.mutate() : like.mutate()) | useUnlike |  | ? | apps/web/src/features/feed/components/like-button.tsx:55 |
| button.onClick: guest           ? `Tài khoản khách không thể thích bài viết, ${likeCoun | () => (liked ? unlike.mutate() : like.mutate()) | useLike |  | ? | apps/web/src/features/feed/components/like-button.tsx:55 |

Other write endpoints reachable (called outside a detected .mutate): POST /feed/posts/{postId}/reactions (useSetReaction apps/web/src/features/feed/api.ts:176); DELETE /feed/posts/{postId}/reactions (useSetReaction apps/web/src/features/feed/api.ts:179)

## Interactive elements (7)

- form.onSubmit (no literal label) -> onSubmit (apps/web/src/features/feed/components/comment-composer.tsx:52)
- button.onClick Tuỳ chọn bài viết -> () => setOpen((v) => !v) (apps/web/src/features/feed/components/post-card.tsx:182)
- button.onClick Đóng menu -> () => setOpen(false) (apps/web/src/features/feed/components/post-card.tsx:195)
- button.onClick Xoá bài viết -> () => void onDelete() (apps/web/src/features/feed/components/post-card.tsx:206)
- button.onClick (no literal label) -> () => setContentExpanded((value) => !value) (apps/web/src/features/feed/components/post-card.tsx:332)
- button.onClick Chia sẻ bài viết -> () => void onShare() (apps/web/src/features/feed/components/post-card.tsx:373)
- button.onClick guest           ? `Tài khoản khách không thể thích bài viết, ${likeCoun -> () => (liked ? unlike.mutate() : like.mutate()) (apps/web/src/features/feed/components/like-button.tsx:46)

## Realtime events listened

none

## Navigation destinations

- /users/${author.id via link (apps/web/src/shared/ui/content-comment-list.tsx:95)

## Error codes handled in the FE

none referenced by code string (errors are shown through generic ApiError message handling)

## Guest and capability gating

- guest checks (isGuest) in: apps/web/src/features/feed/components/comment-composer.tsx, apps/web/src/features/feed/components/like-button.tsx
- no capability check

## Form validation (zod)

- createCommentSchema (apps/web/src/features/feed/create-comment-schema.ts:4): content=z.string().trim().min(1, 'Nhập nội dung bình luận').max(1000)

## Browser storage and cookies

- staleTime Infinity (apps/web/src/shared/auth/use-current-user.ts:25)
- cookie-write  (apps/web/src/shared/i18n/locale-store.ts:37)

## i18n keys used (315)

- analytics.accept: Đồng ý
- analytics.consentDescription: Litmatch dùng phân tích thao tác và bản phát lại đã che toàn bộ nội dung chữ/input để tìm lỗi và cải thiện sản phẩm. Bạn có thể từ chối mà vẫn dùng đầy đủ tính năng.
- analytics.consentTitle: Cho phép phân tích trải nghiệm?
- analytics.decline: Từ chối
- analytics.settingsSection: Cải thiện trải nghiệm
- app.back: Quay lại
- app.conversation: Trò chuyện
- app.conversationError: Không tải được trò chuyện.
- app.conversationLoading: Đang tải trò chuyện…
- app.feed: Bảng tin
- app.matching: Ghép đôi
- app.messages: Tin nhắn
- app.more: Thêm
- app.partyEyebrow: Trò chuyện nhóm
- app.post: Bài viết
- app.profile: Hồ sơ
- app.publicProfile: Hồ sơ người dùng
- app.room: Phòng nhóm
- app.videoShort: Video ngắn
- app.wallet: Ví & VIP
- app.walletEyebrow: Số dư & gói VIP
- app.watchTogether: Đang xem chung
- auth.changePhone: ← Đổi số điện thoại
- auth.checkingProviders: Đang kiểm tra phương thức đăng nhập…
- auth.checkingSession: Đang kiểm tra phiên đăng nhập…
- … 290 more, see i18n-*.md

## Tests

- apps/web/src/features/feed/api.spec.tsx
- apps/web/src/features/feed/components/feed-banners.spec.tsx
- apps/web/src/features/feed/components/like-button.spec.tsx
- apps/web/src/features/feed/components/post-composer.spec.tsx
- apps/web/src/features/feed/components/post-detail.spec.tsx
- apps/web/src/features/feed/components/post-list.spec.tsx
- apps/web/src/features/feed/create-comment-schema.spec.ts
- apps/web/src/features/feed/create-post-schema.spec.ts

## Backend

modules: [feed](../modules/feed.md), [user](../modules/user.md)

service-map flows touching these endpoints: [feed-reaction-with-notification](../flows/feed-reaction-with-notification.md), [feed-comment-with-notification](../flows/feed-comment-with-notification.md), [feed-delete-post](../flows/feed-delete-post.md), [feed-read-posts](../flows/feed-read-posts.md)
