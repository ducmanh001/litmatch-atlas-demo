# web route /users/:id

id: fe:route:web:/users/:id
file: apps/web/src/app/(app)/users/[id]/page.tsx
layout: app (login required: AppChromeWithAuth)
title: Hồ sơ người dùng
reached source files: 22 (static import closure); data features: feed, profile

Access: every route in the (app) group renders inside AppChromeWithAuth (AuthGate): no session -> redirected to /login. Guests can log in with a device id but several actions are limited (see "Guest and capability gating").

## UI action map card (human-authored, not verified against code)

PUBLIC PROFILE /users/:id: Từ Discovery / Feed / Video / Profile

:
  - Profile data / presence / posts
  - Follow / Unfollow
  - Nhắn tin / mở conversation
  - Tặng quà để mở chat
  - Quay lại Friends / Chat

## Data it reads (GET)

| endpoint | hook | query key | polling | stale time | module | defined at |
|---|---|---|---|---|---|---|
| GET /users/{id} | usePublicProfile | profileKeys.public(id) |  |  | [user](../api/user.md) | apps/web/src/features/profile/api.ts:74 |
| GET /users/{id}/presence | usePublicPresence | profileKeys.presence(id) |  | 30_000 | [user](../api/user.md) | apps/web/src/features/profile/api.ts:177 |
| GET /profiles/{profileUserId}/actions | useProfileActions | profileKeys.actions(id) |  |  | [friend](../api/friend.md) | apps/web/src/features/profile/api.ts:86 |
| GET /gifts | useProfileGiftCatalog | ['gifts', 'catalog'] as const |  |  | [gift](../api/gift.md) | apps/web/src/features/profile/api.ts:100 |
| GET /feed/users/{userId}/posts | useUserTimeline | feedKeys.userTimeline(userId) |  |  | [feed](../api/feed.md) | apps/web/src/features/feed/api.ts:51 |
| GET /users/me | useCurrentUser | currentUserKey |  | Infinity | [user](../api/user.md) | apps/web/src/shared/auth/use-current-user.ts:20 |
| GET /feed/posts/{postId}/reactions | useReactionStatus | feedKeys.reaction(postId) |  |  | [feed](../api/feed.md) | apps/web/src/features/feed/api.ts:163 |

## Actions that change data (mutations)

| UI element and label | handler | hook | endpoint | module | call site |
|---|---|---|---|---|---|
| button.onClick: (no literal label) | openChat | useOpenProfileConversation | POST /profiles/{profileUserId}/conversation | [friend](../api/friend.md) | apps/web/src/features/profile/components/public-profile-view.tsx:79 |
| button.onClick: (no literal label) | toggleFollow | useFollowProfile | POST /profiles/{profileUserId}/follow, DELETE /profiles/{profileUserId}/follow | [friend](../api/friend.md) | apps/web/src/features/profile/components/public-profile-view.tsx:94 |
| (no matching UI element found; called from sendGiftToOpenChat) |  | useSendProfileGift | POST /profiles/{profileUserId}/gifts | [gift](../api/gift.md) | apps/web/src/features/profile/components/public-profile-view.tsx:110 |
| (no matching UI element found; called from onDelete) |  | useDeletePost | DELETE /feed/posts/{postId} | [feed](../api/feed.md) | apps/web/src/features/feed/components/post-card.tsx:166 |
| button.onClick: guest           ? `Tài khoản khách không thể thích bài viết, ${likeCoun | () => (liked ? unlike.mutate() : like.mutate()) | useUnlike |  | ? | apps/web/src/features/feed/components/like-button.tsx:55 |
| button.onClick: guest           ? `Tài khoản khách không thể thích bài viết, ${likeCoun | () => (liked ? unlike.mutate() : like.mutate()) | useLike |  | ? | apps/web/src/features/feed/components/like-button.tsx:55 |

Other write endpoints reachable (called outside a detected .mutate): POST /feed/posts/{postId}/reactions (useSetReaction apps/web/src/features/feed/api.ts:176); DELETE /feed/posts/{postId}/reactions (useSetReaction apps/web/src/features/feed/api.ts:179)

## Interactive elements (10)

- button.onClick Quay lại -> goBack (apps/web/src/features/profile/components/public-profile-view.tsx:133)
- button.onClick (no literal label) -> toggleFollow (apps/web/src/features/profile/components/public-profile-view.tsx:194)
- button.onClick (no literal label) -> openChat (apps/web/src/features/profile/components/public-profile-view.tsx:206)
- button.onClick (no literal label) -> () => sendGiftToOpenChat(gift.id, gift.name) (apps/web/src/features/profile/components/public-profile-view.tsx:321)
- button.onClick Tuỳ chọn bài viết -> () => setOpen((v) => !v) (apps/web/src/features/feed/components/post-card.tsx:182)
- button.onClick Đóng menu -> () => setOpen(false) (apps/web/src/features/feed/components/post-card.tsx:195)
- button.onClick Xoá bài viết -> () => void onDelete() (apps/web/src/features/feed/components/post-card.tsx:206)
- button.onClick (no literal label) -> () => setContentExpanded((value) => !value) (apps/web/src/features/feed/components/post-card.tsx:332)
- button.onClick Chia sẻ bài viết -> () => void onShare() (apps/web/src/features/feed/components/post-card.tsx:373)
- button.onClick guest           ? `Tài khoản khách không thể thích bài viết, ${likeCoun -> () => (liked ? unlike.mutate() : like.mutate()) (apps/web/src/features/feed/components/like-button.tsx:46)

## Realtime events listened

none

## Navigation destinations

- /friends via router.push (apps/web/src/features/profile/components/public-profile-view.tsx:73)
- /chat/{param} via router.push (apps/web/src/features/profile/components/public-profile-view.tsx:116)
- /users/:param/followers via link (apps/web/src/features/profile/components/public-profile-view.tsx:239)
- /users/:param/following via link (apps/web/src/features/profile/components/public-profile-view.tsx:244)
- /feed via link (apps/web/src/features/profile/components/profile-posts-grid.tsx:62)
- /feed/${post.id via link (apps/web/src/features/profile/components/profile-posts-grid.tsx:70)
- /users/${author.id via link (apps/web/src/features/feed/components/post-card.tsx:300)

## Error codes handled in the FE

none referenced by code string (errors are shown through generic ApiError message handling)

## Guest and capability gating

- guest checks (isGuest) in: apps/web/src/features/feed/components/like-button.tsx
- no capability check

## Browser storage and cookies

- staleTime 30_000 (apps/web/src/features/profile/api.ts:182)
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
- apps/web/src/features/profile/components/follow-list-view.spec.tsx
- apps/web/src/features/profile/components/profile-stats.spec.tsx
- apps/web/src/features/profile/components/profile-view.spec.tsx
- apps/web/src/features/profile/components/public-profile-view.spec.tsx
- apps/web/src/features/profile/update-profile-schema.spec.ts

## Backend

modules: [user](../modules/user.md), [friend](../modules/friend.md), [gift](../modules/gift.md), [feed](../modules/feed.md)

service-map flows touching these endpoints: [feed-reaction-with-notification](../flows/feed-reaction-with-notification.md), [friend-profile-follow-and-actions](../flows/friend-profile-follow-and-actions.md), [admin-gift-catalog](../flows/admin-gift-catalog.md), [feed-delete-post](../flows/feed-delete-post.md), [feed-read-posts](../flows/feed-read-posts.md), [friend-open-conversation-from-profile](../flows/friend-open-conversation-from-profile.md), [gift-to-profile-chat-gate](../flows/gift-to-profile-chat-gate.md), [user-presence-read](../flows/user-presence-read.md)
