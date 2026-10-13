# web route /wallet

id: fe:route:web:/wallet
file: apps/web/src/app/(app)/wallet/page.tsx
layout: app (login required: AppChromeWithAuth)
title: Ví & VIP
reached source files: 28 (static import closure); data features: notification, wallet

Access: every route in the (app) group renders inside AppChromeWithAuth (AuthGate): no session -> redirected to /login. Guests can log in with a device id but several actions are limited (see "Guest and capability gating").

## UI action map card (human-authored, not verified against code)

WALLET /wallet: Diamond và VIP

Wallet actions:
  - Xem balance
  - Xem VIP tier / expiry
  - Xem package catalog
  - Nạp Diamond
Commerce actions:
  - Chọn gói VIP
  - Tạo payment order
  - Kiểm tra payment
  - Diamond → Gift / Matching

## Data it reads (GET)

| endpoint | hook | query key | polling | stale time | module | defined at |
|---|---|---|---|---|---|---|
| GET /economy/wallet | useWallet | walletKeys.wallet |  |  | [economy](../api/economy.md) | apps/web/src/features/wallet/api.ts:23 |
| GET /capabilities | useCapabilities | capabilityKeys.runtime |  | Infinity | [app-shell](../api/app-shell.md) | apps/web/src/shared/capabilities/api.ts:24 |
| GET /economy/payos/packages | usePayosPackages | walletKeys.payosPackages |  | Infinity | [economy](../api/economy.md) | apps/web/src/features/wallet/api.ts:45 |
| GET /economy/payos/orders/{orderId} | usePayosOrder | walletKeys.payosOrder(orderId ?? '') |  |  | [economy](../api/economy.md) | apps/web/src/features/wallet/api.ts:58 |
| GET /economy/vip/plans | useVipPlans | walletKeys.vipPlans |  | Infinity | [economy](../api/economy.md) | apps/web/src/features/wallet/api.ts:89 |
| GET /notifications | useNotifications | notificationKeys.list |  |  | [notification](../api/notification.md) | apps/web/src/features/notification/api.ts:63 |
| GET /notifications/unread-count | useUnreadNotificationCount | notificationKeys.unreadCount | UNREAD_NOTIFICATION_COUNT_REFETCH_INTERVAL_MS |  | [notification](../api/notification.md) | apps/web/src/features/notification/api.ts:51 |

## Actions that change data (mutations)

| UI element and label | handler | hook | endpoint | module | call site |
|---|---|---|---|---|---|
| button.onClick: (no literal label) | () => createOrder.mutate( { packageId: item.packageId, idempotencyKey: key, }, { onSuccess: (order) => { resetKey(); if | useCreatePayosOrder | POST /economy/payos/orders | [economy](../api/economy.md) | apps/web/src/features/wallet/components/topup-packages.tsx:117 |
| (no matching UI element found; called from purchase) |  | usePurchaseVip | POST /economy/vip/purchase | [economy](../api/economy.md) | apps/web/src/features/wallet/components/vip-plans.tsx:89 |
| button.onClick: (no literal label) | () => { // Mark-read idempotent phía server — bấm lại item đã đọc chỉ điều hướng. if (unread) markRead.mutate(notificati | useMarkNotificationRead | POST /notifications/{notificationId}/read | [notification](../api/notification.md) | apps/web/src/features/notification/components/notification-bell.tsx:40 |

Other write endpoints reachable (called outside a detected .mutate): POST /notifications/web-push/subscription (registerWebPushSubscription apps/web/src/features/notification/api.ts:34); DELETE /notifications/web-push/subscription (unregisterWebPushSubscription apps/web/src/features/notification/api.ts:42)

## Interactive elements (14)

- button.onClick Diamond -> () => setTab('diamond') (apps/web/src/features/wallet/components/wallet-tabs.tsx:34)
- button.onClick VIP -> () => setTab('vip') (apps/web/src/features/wallet/components/wallet-tabs.tsx:43)
- button.onClick (no literal label) -> () => showToast(webTopUp.message, 'warn') (apps/web/src/features/wallet/components/topup-packages.tsx:53)
- button.onClick (no literal label) -> () => createOrder.mutate( { packageId: item.packageId, idempotencyKey… (apps/web/src/features/wallet/components/topup-packages.tsx:113)
- button.onClick (no literal label) -> () => void order.refetch() (apps/web/src/features/wallet/components/payos-payment-status.tsx:64)
- button.onClick t('wallet.buyPlan', {                   tier: plan.tier.toUpperCase(), -> () => purchase(plan) (apps/web/src/features/wallet/components/vip-plans.tsx:174)
- button.onClick Nạp Diamond (wallet.topUp) -> onTopUp (apps/web/src/features/wallet/components/wallet-balance.tsx:74)
- button.onClick Nâng cấp VIP (wallet.upgradeVip) -> onUpgradeVip (apps/web/src/features/wallet/components/wallet-balance.tsx:86)
- button.onClick (no literal label) -> () => { // Mark-read idempotent phía server — bấm lại item đã đọc chỉ… (apps/web/src/features/notification/components/notification-bell.tsx:36)
- button.onClick Thử lại -> () => void refetch() (apps/web/src/features/notification/components/notification-bell.tsx:116)
- button.onClick Đang tải… (common.loading) -> () => void fetchNextPage() (apps/web/src/features/notification/components/notification-bell.tsx:145)
- button.onClick 🔔 -> () => { void enableWebPush() .then(setStatus) .catch(() => setStatus(… (apps/web/src/features/notification/components/notification-bell.tsx:194)
- button.onClick unreadCount > 0             ? t('notifications.unread', { count: unre -> () => setOpen((value) => !value) (apps/web/src/features/notification/components/notification-bell.tsx:217)
- button.onClick t('notifications.close') -> () => setOpen(false) (apps/web/src/features/notification/components/notification-bell.tsx:240)

## Realtime events listened

none

## Navigation destinations

- /wallet via link (apps/web/src/shared/ui/page-header.tsx:20)

## Error codes handled in the FE

none referenced by code string (errors are shown through generic ApiError message handling)

## Guest and capability gating

- no isGuest check
- capability checks: useCapabilities() apps/web/src/features/wallet/components/topup-packages.tsx:21; isCapabilityUsable(webTopUp) apps/web/src/features/wallet/components/topup-packages.tsx:31; isCapabilityUsable(webTopUp) apps/web/src/features/wallet/components/topup-packages.tsx:51

## Browser storage and cookies

- staleTime Infinity (apps/web/src/features/wallet/api.ts:37)
- staleTime Infinity (apps/web/src/features/wallet/api.ts:48)
- staleTime Infinity (apps/web/src/features/wallet/api.ts:92)
- localStorage LAST_DARK_MOOD_KEY (apps/web/src/shared/ui/theme-toggle-button.tsx:36)
- localStorage LAST_DARK_MOOD_KEY (apps/web/src/shared/ui/theme-toggle-button.tsx:39)
- staleTime Infinity (apps/web/src/shared/capabilities/api.ts:32)
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

- apps/web/src/features/notification/api.spec.ts
- apps/web/src/features/notification/components/notification-bell.spec.tsx
- apps/web/src/features/notification/notification-copy.spec.ts
- apps/web/src/features/wallet/components/payos-payment-status.spec.tsx
- apps/web/src/features/wallet/components/topup-packages.spec.tsx
- apps/web/src/features/wallet/components/vip-plans.spec.tsx
- apps/web/src/features/wallet/components/wallet-balance.spec.tsx

## Backend

modules: [economy](../modules/economy.md), [app-shell](../modules/app-shell.md), [notification](../modules/notification.md)

service-map flows touching these endpoints: [payos-topup-webhook](../flows/payos-topup-webhook.md), [message-push-notification](../flows/message-push-notification.md), [vip-purchase-iap](../flows/vip-purchase-iap.md)
