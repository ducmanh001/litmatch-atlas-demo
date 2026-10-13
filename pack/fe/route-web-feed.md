# web route /feed

id: fe:route:web:/feed
file: apps/web/src/app/(app)/feed/page.tsx
layout: app (login required: AppChromeWithAuth)
title: Bảng tin
reached source files: 41 (static import closure); data features: feed, notification, party-room, wallet

Access: every route in the (app) group renders inside AppChromeWithAuth (AuthGate): no session -> redirected to /login. Guests can log in with a device id but several actions are limited (see "Guest and capability gating").

## UI action map card (human-authored, not verified against code)

FEED /feed: Bài viết cộng đồng

Content actions:
  - Xem danh sách bài viết
  - Tạo bài viết
  - Chọn audience
  - Đính kèm media / emoji
Post actions:
  - Like / Unlike
  - Comment
  - Share
  - Mở profile tác giả

## Data it reads (GET)

| endpoint | hook | query key | polling | stale time | module | defined at |
|---|---|---|---|---|---|---|
| GET /feed/posts | useFeed | feedKeys.list |  |  | [feed](../api/feed.md) | apps/web/src/features/feed/api.ts:33 |
| GET /economy/wallet | useWallet | walletKeys.wallet |  |  | [economy](../api/economy.md) | apps/web/src/features/wallet/api.ts:23 |
| GET /party/rooms | useRoomList | [ ...partyRoomKeys.list, filters?.q ?? '', filters?.category ?? 'all', ] |  |  | [party-room](../api/party-room.md) | apps/web/src/features/party-room/api.ts:66 |
| GET /users/{id} | useUserProfiles |  |  |  | [user](../api/user.md) | apps/web/src/features/party-room/api.ts:322 |
| GET /users/me | useCurrentUser | currentUserKey |  | Infinity | [user](../api/user.md) | apps/web/src/shared/auth/use-current-user.ts:20 |
| GET /notifications | useNotifications | notificationKeys.list |  |  | [notification](../api/notification.md) | apps/web/src/features/notification/api.ts:63 |
| GET /notifications/unread-count | useUnreadNotificationCount | notificationKeys.unreadCount | UNREAD_NOTIFICATION_COUNT_REFETCH_INTERVAL_MS |  | [notification](../api/notification.md) | apps/web/src/features/notification/api.ts:51 |
| GET /feed/posts/{postId}/reactions | useReactionStatus | feedKeys.reaction(postId) |  |  | [feed](../api/feed.md) | apps/web/src/features/feed/api.ts:163 |

## Actions that change data (mutations)

| UI element and label | handler | hook | endpoint | module | call site |
|---|---|---|---|---|---|
| (no matching UI element found; called from onDelete) |  | useDeletePost | DELETE /feed/posts/{postId} | [feed](../api/feed.md) | apps/web/src/features/feed/components/post-card.tsx:166 |
| (no matching UI element found; called from PostComposer) |  | useCreatePost | POST /feed/posts | [feed](../api/feed.md) | apps/web/src/features/feed/components/post-composer.tsx:121 |
| button.onClick: (no literal label) | () => { // Mark-read idempotent phía server — bấm lại item đã đọc chỉ điều hướng. if (unread) markRead.mutate(notificati | useMarkNotificationRead | POST /notifications/{notificationId}/read | [notification](../api/notification.md) | apps/web/src/features/notification/components/notification-bell.tsx:40 |
| button.onClick: guest           ? `Tài khoản khách không thể thích bài viết, ${likeCoun | () => (liked ? unlike.mutate() : like.mutate()) | useUnlike |  | ? | apps/web/src/features/feed/components/like-button.tsx:55 |
| button.onClick: guest           ? `Tài khoản khách không thể thích bài viết, ${likeCoun | () => (liked ? unlike.mutate() : like.mutate()) | useLike |  | ? | apps/web/src/features/feed/components/like-button.tsx:55 |

Other write endpoints reachable (called outside a detected .mutate): POST /feed/posts/{postId}/reactions (useSetReaction apps/web/src/features/feed/api.ts:176); DELETE /feed/posts/{postId}/reactions (useSetReaction apps/web/src/features/feed/api.ts:179); POST /media/images/upload-intent (uploadImage apps/web/src/shared/media/image-upload.ts:31); POST /notifications/web-push/subscription (registerWebPushSubscription apps/web/src/features/notification/api.ts:34); DELETE /notifications/web-push/subscription (unregisterWebPushSubscription apps/web/src/features/notification/api.ts:42)

## Interactive elements (18)

- button.onClick Xem mục trước -> () => moveCarousel(-1) (apps/web/src/features/feed/components/feed-banners.tsx:114)
- button.onClick Xem mục tiếp theo -> () => moveCarousel(1) (apps/web/src/features/feed/components/feed-banners.tsx:123)
- button.onClick Tuỳ chọn bài viết -> () => setOpen((v) => !v) (apps/web/src/features/feed/components/post-card.tsx:182)
- button.onClick Đóng menu -> () => setOpen(false) (apps/web/src/features/feed/components/post-card.tsx:195)
- button.onClick Xoá bài viết -> () => void onDelete() (apps/web/src/features/feed/components/post-card.tsx:206)
- button.onClick (no literal label) -> () => setContentExpanded((value) => !value) (apps/web/src/features/feed/components/post-card.tsx:332)
- button.onClick Chia sẻ bài viết -> () => void onShare() (apps/web/src/features/feed/components/post-card.tsx:373)
- form.onSubmit (no literal label) -> onSubmit (apps/web/src/features/feed/components/post-composer.tsx:150)
- button.onClick Đính kèm ảnh -> () => setShowImageInput((shown) => { if (shown) { setImageFile(null);… (apps/web/src/features/feed/components/post-composer.tsx:190)
- button.onClick Chèn emoji -> () => setShowEmojiPicker((shown) => !shown) (apps/web/src/features/feed/components/post-composer.tsx:210)
- button.onClick `Chèn ${emoji}` -> () => form.setValue( 'content', `${form.getValues('content') ?? ''}${… (apps/web/src/features/feed/components/post-composer.tsx:252)
- button.onClick (no literal label) -> () => { // Mark-read idempotent phía server — bấm lại item đã đọc chỉ… (apps/web/src/features/notification/components/notification-bell.tsx:36)
- button.onClick Thử lại -> () => void refetch() (apps/web/src/features/notification/components/notification-bell.tsx:116)
- button.onClick Đang tải… (common.loading) -> () => void fetchNextPage() (apps/web/src/features/notification/components/notification-bell.tsx:145)
- button.onClick 🔔 -> () => { void enableWebPush() .then(setStatus) .catch(() => setStatus(… (apps/web/src/features/notification/components/notification-bell.tsx:194)
- button.onClick unreadCount > 0             ? t('notifications.unread', { count: unre -> () => setOpen((value) => !value) (apps/web/src/features/notification/components/notification-bell.tsx:217)
- button.onClick t('notifications.close') -> () => setOpen(false) (apps/web/src/features/notification/components/notification-bell.tsx:240)
- button.onClick guest           ? `Tài khoản khách không thể thích bài viết, ${likeCoun -> () => (liked ? unlike.mutate() : like.mutate()) (apps/web/src/features/feed/components/like-button.tsx:46)

## Realtime events listened

none

## Navigation destinations

- /wallet via link (apps/web/src/shared/ui/page-header.tsx:20)
- /party via link (apps/web/src/app/(app)/feed/feed-sidebar.tsx:35)
- /party/${room.id via link (apps/web/src/app/(app)/feed/feed-sidebar.tsx:46)
- /discovery via link (apps/web/src/app/(app)/feed/feed-sidebar.tsx:82)
- /users/${author.id via link (apps/web/src/features/feed/components/post-card.tsx:300)

## Error codes handled in the FE

none referenced by code string (errors are shown through generic ApiError message handling)

## Guest and capability gating

- guest checks (isGuest) in: apps/web/src/features/feed/components/post-composer.tsx, apps/web/src/features/feed/components/like-button.tsx
- no capability check

## Browser storage and cookies

- staleTime Infinity (apps/web/src/features/wallet/api.ts:37)
- staleTime Infinity (apps/web/src/features/wallet/api.ts:48)
- staleTime Infinity (apps/web/src/features/wallet/api.ts:92)
- localStorage LAST_DARK_MOOD_KEY (apps/web/src/shared/ui/theme-toggle-button.tsx:36)
- localStorage LAST_DARK_MOOD_KEY (apps/web/src/shared/ui/theme-toggle-button.tsx:39)
- staleTime Infinity (apps/web/src/shared/auth/use-current-user.ts:25)
- cookie-write  (apps/web/src/shared/i18n/locale-store.ts:37)
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
- apps/web/src/features/party-room/api.spec.ts
- apps/web/src/features/party-room/components/member-list.spec.tsx
- apps/web/src/features/party-room/components/party-chat.spec.tsx
- apps/web/src/features/party-room/components/party-stage.spec.tsx
- apps/web/src/features/party-room/components/room-list.spec.tsx
- apps/web/src/features/party-room/create-room-schema.spec.ts
- apps/web/src/features/party-room/hooks/use-party-room-media.spec.tsx
- apps/web/src/features/wallet/components/payos-payment-status.spec.tsx
- apps/web/src/features/wallet/components/topup-packages.spec.tsx
- apps/web/src/features/wallet/components/vip-plans.spec.tsx
- apps/web/src/features/wallet/components/wallet-balance.spec.tsx

## Backend

modules: [feed](../modules/feed.md), [economy](../modules/economy.md), [party-room](../modules/party-room.md), [user](../modules/user.md), [notification](../modules/notification.md), [media](../modules/media.md)

service-map flows touching these endpoints: [feed-reaction-with-notification](../flows/feed-reaction-with-notification.md), [feed-post-with-image](../flows/feed-post-with-image.md), [feed-delete-post](../flows/feed-delete-post.md), [feed-read-posts](../flows/feed-read-posts.md), [message-push-notification](../flows/message-push-notification.md), [party-room-detail-and-comments](../flows/party-room-detail-and-comments.md)
