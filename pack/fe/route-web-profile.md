# web route /profile

id: fe:route:web:/profile
file: apps/web/src/app/(app)/profile/page.tsx
layout: app (login required: AppChromeWithAuth)
title: Hồ sơ
reached source files: 38 (static import closure); data features: feed, friend-chat, notification, profile, wallet

Access: every route in the (app) group renders inside AppChromeWithAuth (AuthGate): no session -> redirected to /login. Guests can log in with a device id but several actions are limited (see "Guest and capability gating").

## UI action map card (human-authored, not verified against code)

PROFILE /profile: Hồ sơ của tôi

Profile actions:
  - Xem nickname / avatar
  - Xem Diamond / bạn bè / posts
  - Chỉnh sửa → Profile Edit
  - Mở Wallet / Privacy / Help
Account actions:
  - Đổi ngôn ngữ
  - Đổi theme
  - Đăng xuất → Login
  - Mở public profile người khác

## Data it reads (GET)

| endpoint | hook | query key | polling | stale time | module | defined at |
|---|---|---|---|---|---|---|
| GET /users/me | useCurrentUser | currentUserKey |  | Infinity | [user](../api/user.md) | apps/web/src/shared/auth/use-current-user.ts:20 |
| GET /economy/wallet | useWallet | walletKeys.wallet |  |  | [economy](../api/economy.md) | apps/web/src/features/wallet/api.ts:23 |
| GET /feed/users/{userId}/posts | useUserTimeline | feedKeys.userTimeline(userId) |  |  | [feed](../api/feed.md) | apps/web/src/features/feed/api.ts:51 |
| GET /friends/connections | useFriendConnections | friendChatKeys.connections |  |  | [friend](../api/friend.md) | apps/web/src/features/friend-chat/api.ts:49 |
| GET /profiles/{profileUserId}/follow-counts | useFollowCounts | profileKeys.followCounts(id) |  |  | [friend](../api/friend.md) | apps/web/src/features/profile/api.ts:38 |
| GET /notifications | useNotifications | notificationKeys.list |  |  | [notification](../api/notification.md) | apps/web/src/features/notification/api.ts:63 |
| GET /notifications/unread-count | useUnreadNotificationCount | notificationKeys.unreadCount | UNREAD_NOTIFICATION_COUNT_REFETCH_INTERVAL_MS |  | [notification](../api/notification.md) | apps/web/src/features/notification/api.ts:51 |
| GET /feed/posts/{postId}/reactions | useReactionStatus | feedKeys.reaction(postId) |  |  | [feed](../api/feed.md) | apps/web/src/features/feed/api.ts:163 |

## Actions that change data (mutations)

| UI element and label | handler | hook | endpoint | module | call site |
|---|---|---|---|---|---|
| button.onClick: (no literal label) | () => { // Mark-read idempotent phía server — bấm lại item đã đọc chỉ điều hướng. if (unread) markRead.mutate(notificati | useMarkNotificationRead | POST /notifications/{notificationId}/read | [notification](../api/notification.md) | apps/web/src/features/notification/components/notification-bell.tsx:40 |
| (no matching UI element found; called from onDelete) |  | useDeletePost | DELETE /feed/posts/{postId} | [feed](../api/feed.md) | apps/web/src/features/feed/components/post-card.tsx:166 |
| button.onClick: guest           ? `Tài khoản khách không thể thích bài viết, ${likeCoun | () => (liked ? unlike.mutate() : like.mutate()) | useUnlike |  | ? | apps/web/src/features/feed/components/like-button.tsx:55 |
| button.onClick: guest           ? `Tài khoản khách không thể thích bài viết, ${likeCoun | () => (liked ? unlike.mutate() : like.mutate()) | useLike |  | ? | apps/web/src/features/feed/components/like-button.tsx:55 |

Other write endpoints reachable (called outside a detected .mutate): POST /auth/logout (useLogout apps/web/src/shared/auth/use-logout.ts:22); POST /feed/posts/{postId}/reactions (useSetReaction apps/web/src/features/feed/api.ts:176); DELETE /feed/posts/{postId}/reactions (useSetReaction apps/web/src/features/feed/api.ts:179); POST /notifications/web-push/subscription (registerWebPushSubscription apps/web/src/features/notification/api.ts:34); DELETE /notifications/web-push/subscription (unregisterWebPushSubscription apps/web/src/features/notification/api.ts:42)

## Interactive elements (13)

- button.onClick Đăng xuất (profile.logout) -> logout (apps/web/src/features/profile/components/profile-menu.tsx:84)
- button.onClick (no literal label) -> () => { // Mark-read idempotent phía server — bấm lại item đã đọc chỉ… (apps/web/src/features/notification/components/notification-bell.tsx:36)
- button.onClick Thử lại -> () => void refetch() (apps/web/src/features/notification/components/notification-bell.tsx:116)
- button.onClick Đang tải… (common.loading) -> () => void fetchNextPage() (apps/web/src/features/notification/components/notification-bell.tsx:145)
- button.onClick 🔔 -> () => { void enableWebPush() .then(setStatus) .catch(() => setStatus(… (apps/web/src/features/notification/components/notification-bell.tsx:194)
- button.onClick unreadCount > 0             ? t('notifications.unread', { count: unre -> () => setOpen((value) => !value) (apps/web/src/features/notification/components/notification-bell.tsx:217)
- button.onClick t('notifications.close') -> () => setOpen(false) (apps/web/src/features/notification/components/notification-bell.tsx:240)
- button.onClick Tuỳ chọn bài viết -> () => setOpen((v) => !v) (apps/web/src/features/feed/components/post-card.tsx:182)
- button.onClick Đóng menu -> () => setOpen(false) (apps/web/src/features/feed/components/post-card.tsx:195)
- button.onClick Xoá bài viết -> () => void onDelete() (apps/web/src/features/feed/components/post-card.tsx:206)
- button.onClick (no literal label) -> () => setContentExpanded((value) => !value) (apps/web/src/features/feed/components/post-card.tsx:332)
- button.onClick Chia sẻ bài viết -> () => void onShare() (apps/web/src/features/feed/components/post-card.tsx:373)
- button.onClick guest           ? `Tài khoản khách không thể thích bài viết, ${likeCoun -> () => (liked ? unlike.mutate() : like.mutate()) (apps/web/src/features/feed/components/like-button.tsx:46)

## Realtime events listened

none

## Navigation destinations

- /profile/edit via link (apps/web/src/features/profile/components/profile-menu.tsx:49)
- /wallet via link (apps/web/src/features/profile/components/profile-stats.tsx:69)
- /privacy via link (apps/web/src/features/profile/components/profile-menu.tsx:64)
- /help via link (apps/web/src/features/profile/components/profile-menu.tsx:73)
- /feed via link (apps/web/src/features/profile/components/profile-posts-grid.tsx:62)
- /feed/${post.id via link (apps/web/src/features/profile/components/profile-posts-grid.tsx:70)
- /friends via link (apps/web/src/features/profile/components/profile-stats.tsx:77)
- /users/:param/followers via link (apps/web/src/features/profile/components/profile-stats.tsx:84)
- /users/:param/following via link (apps/web/src/features/profile/components/profile-stats.tsx:91)
- /login via router.replace (apps/web/src/shared/auth/use-logout.ts:20)
- /users/${author.id via link (apps/web/src/features/feed/components/post-card.tsx:300)

## Error codes handled in the FE

none referenced by code string (errors are shown through generic ApiError message handling)

## Guest and capability gating

- guest checks (isGuest) in: apps/web/src/features/profile/components/profile-view.tsx, apps/web/src/features/feed/components/like-button.tsx
- no capability check

## Browser storage and cookies

- staleTime Infinity (apps/web/src/shared/auth/use-current-user.ts:25)
- staleTime Infinity (apps/web/src/features/wallet/api.ts:37)
- staleTime Infinity (apps/web/src/features/wallet/api.ts:48)
- staleTime Infinity (apps/web/src/features/wallet/api.ts:92)
- cookie-write  (apps/web/src/shared/i18n/locale-store.ts:37)
- localStorage LAST_DARK_MOOD_KEY (apps/web/src/shared/ui/theme-toggle-button.tsx:36)
- localStorage LAST_DARK_MOOD_KEY (apps/web/src/shared/ui/theme-toggle-button.tsx:39)
- staleTime 30_000 (apps/web/src/features/profile/api.ts:182)
- localStorage THEME_STORAGE_KEY (apps/web/src/shared/ui/theme-switcher.tsx:25)

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
- apps/web/src/features/friend-chat/api.spec.ts
- apps/web/src/features/friend-chat/components/conversation-inbox.spec.tsx
- apps/web/src/features/friend-chat/components/conversation-thread.spec.tsx
- apps/web/src/features/friend-chat/components/friends-directory.spec.tsx
- apps/web/src/features/friend-chat/components/message-list.spec.tsx
- apps/web/src/features/friend-chat/send-message-schema.spec.ts
- apps/web/src/features/notification/api.spec.ts
- apps/web/src/features/notification/components/notification-bell.spec.tsx
- apps/web/src/features/notification/notification-copy.spec.ts
- apps/web/src/features/profile/components/follow-list-view.spec.tsx
- apps/web/src/features/profile/components/profile-stats.spec.tsx
- apps/web/src/features/profile/components/profile-view.spec.tsx
- apps/web/src/features/profile/components/public-profile-view.spec.tsx
- apps/web/src/features/profile/update-profile-schema.spec.ts
- apps/web/src/features/wallet/components/payos-payment-status.spec.tsx
- apps/web/src/features/wallet/components/topup-packages.spec.tsx
- apps/web/src/features/wallet/components/vip-plans.spec.tsx
- apps/web/src/features/wallet/components/wallet-balance.spec.tsx

## Backend

modules: [user](../modules/user.md), [economy](../modules/economy.md), [feed](../modules/feed.md), [friend](../modules/friend.md), [notification](../modules/notification.md), [auth](../modules/auth.md)

service-map flows touching these endpoints: [feed-reaction-with-notification](../flows/feed-reaction-with-notification.md), [friend-follow-lists-and-connections](../flows/friend-follow-lists-and-connections.md), [auth-refresh-and-logout](../flows/auth-refresh-and-logout.md), [feed-delete-post](../flows/feed-delete-post.md), [feed-read-posts](../flows/feed-read-posts.md), [message-push-notification](../flows/message-push-notification.md)
