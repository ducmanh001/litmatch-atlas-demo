# web route /party

id: fe:route:web:/party
file: apps/web/src/app/(app)/party/page.tsx
layout: app (login required: AppChromeWithAuth)
title: Party Room
reached source files: 28 (static import closure); data features: notification, party-room, wallet

Access: every route in the (app) group renders inside AppChromeWithAuth (AuthGate): no session -> redirected to /login. Guests can log in with a device id but several actions are limited (see "Guest and capability gating").

## UI action map card (human-authored, not verified against code)

PARTY /party: Phòng trò chuyện nhiều người

List actions:
  - Tìm kiếm phòng
  - Lọc chủ đề
  - Xem thêm phòng
  - Tạo phòng mới
Room actions:
  - Join → Party Room
  - Chat realtime
  - Bật / tắt mic
  - Tặng quà / Rời phòng

## Data it reads (GET)

| endpoint | hook | query key | polling | stale time | module | defined at |
|---|---|---|---|---|---|---|
| GET /party/rooms | useRoomList | [ ...partyRoomKeys.list, filters?.q ?? '', filters?.category ?? 'all', ] |  |  | [party-room](../api/party-room.md) | apps/web/src/features/party-room/api.ts:66 |
| GET /users/{id} | useUserProfiles |  |  |  | [user](../api/user.md) | apps/web/src/features/party-room/api.ts:322 |
| GET /economy/wallet | useWallet | walletKeys.wallet |  |  | [economy](../api/economy.md) | apps/web/src/features/wallet/api.ts:23 |
| GET /notifications | useNotifications | notificationKeys.list |  |  | [notification](../api/notification.md) | apps/web/src/features/notification/api.ts:63 |
| GET /notifications/unread-count | useUnreadNotificationCount | notificationKeys.unreadCount | UNREAD_NOTIFICATION_COUNT_REFETCH_INTERVAL_MS |  | [notification](../api/notification.md) | apps/web/src/features/notification/api.ts:51 |

## Actions that change data (mutations)

| UI element and label | handler | hook | endpoint | module | call site |
|---|---|---|---|---|---|
| (no matching UI element found; called from CreateRoomForm) |  | useCreateRoom | POST /party/rooms | [party-room](../api/party-room.md) | apps/web/src/features/party-room/components/create-room-form.tsx:30 |
| button.onClick: (no literal label) | () => { // Mark-read idempotent phía server — bấm lại item đã đọc chỉ điều hướng. if (unread) markRead.mutate(notificati | useMarkNotificationRead | POST /notifications/{notificationId}/read | [notification](../api/notification.md) | apps/web/src/features/notification/components/notification-bell.tsx:40 |

Other write endpoints reachable (called outside a detected .mutate): POST /notifications/web-push/subscription (registerWebPushSubscription apps/web/src/features/notification/api.ts:34); DELETE /notifications/web-push/subscription (unregisterWebPushSubscription apps/web/src/features/notification/api.ts:42)

## Interactive elements (11)

- button.onClick (no literal label) -> () => setActiveCategory(chip.key) (apps/web/src/features/party-room/components/room-list.tsx:68)
- button.onClick (no literal label) -> () => void rooms.fetchNextPage() (apps/web/src/features/party-room/components/room-list.tsx:178)
- button.onClick ＋ Tạo phòng -> () => setIsCreateOpen(true) (apps/web/src/features/party-room/components/room-list.tsx:188)
- button.onClick Đóng -> () => setIsCreateOpen(false) (apps/web/src/features/party-room/components/room-list.tsx:198)
- form.onSubmit (no literal label) -> onSubmit (apps/web/src/features/party-room/components/create-room-form.tsx:39)
- button.onClick (no literal label) -> () => { // Mark-read idempotent phía server — bấm lại item đã đọc chỉ… (apps/web/src/features/notification/components/notification-bell.tsx:36)
- button.onClick Thử lại -> () => void refetch() (apps/web/src/features/notification/components/notification-bell.tsx:116)
- button.onClick Đang tải… (common.loading) -> () => void fetchNextPage() (apps/web/src/features/notification/components/notification-bell.tsx:145)
- button.onClick 🔔 -> () => { void enableWebPush() .then(setStatus) .catch(() => setStatus(… (apps/web/src/features/notification/components/notification-bell.tsx:194)
- button.onClick unreadCount > 0             ? t('notifications.unread', { count: unre -> () => setOpen((value) => !value) (apps/web/src/features/notification/components/notification-bell.tsx:217)
- button.onClick t('notifications.close') -> () => setOpen(false) (apps/web/src/features/notification/components/notification-bell.tsx:240)

## Realtime events listened

none

## Navigation destinations

- /party/${room.id via link (apps/web/src/features/party-room/components/room-list.tsx:143)
- /wallet via link (apps/web/src/shared/ui/page-header.tsx:20)
- /party/{param} via router.push (apps/web/src/features/party-room/components/create-room-form.tsx:33)

## Error codes handled in the FE

none referenced by code string (errors are shown through generic ApiError message handling)

## Guest and capability gating

- no isGuest check
- no capability check

## Form validation (zod)

- createRoomSchema (apps/web/src/features/party-room/create-room-schema.ts:4): title=z.string().trim().min(1, 'Nhập tên phòng').max(100); category=z.enum(['talk', 'sing', 'friend', 'study', 'other'])

## Browser storage and cookies

- staleTime Infinity (apps/web/src/features/wallet/api.ts:37)
- staleTime Infinity (apps/web/src/features/wallet/api.ts:48)
- staleTime Infinity (apps/web/src/features/wallet/api.ts:92)
- localStorage LAST_DARK_MOOD_KEY (apps/web/src/shared/ui/theme-toggle-button.tsx:36)
- localStorage LAST_DARK_MOOD_KEY (apps/web/src/shared/ui/theme-toggle-button.tsx:39)
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

modules: [party-room](../modules/party-room.md), [user](../modules/user.md), [economy](../modules/economy.md), [notification](../modules/notification.md)

service-map flows touching these endpoints: [message-push-notification](../flows/message-push-notification.md), [party-room-detail-and-comments](../flows/party-room-detail-and-comments.md), [party-room-lifecycle](../flows/party-room-lifecycle.md)
