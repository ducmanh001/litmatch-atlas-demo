# web route /matching

id: fe:route:web:/matching
file: apps/web/src/app/(app)/matching/page.tsx
layout: app (login required: AppChromeWithAuth)
title: Ghép đôi
reached source files: 36 (static import closure); data features: matching, notification, wallet

Access: every route in the (app) group renders inside AppChromeWithAuth (AuthGate): no session -> redirected to /login. Guests can log in with a device id but several actions are limited (see "Guest and capability gating").

## UI action map card (human-authored, not verified against code)

MATCHING /matching: Soul Match / Voice Match

Queue actions:
  - Chọn Soul hoặc Voice
  - Chọn giới tính mong muốn
  - Bắt đầu tìm kiếm
  - Huỷ tìm kiếm
Session actions:
  - Soul → Chat ẩn danh
  - Voice → LiveKit / microphone
  - Thích / Bỏ qua
  - Match → Friends / Chat

## Data it reads (GET)

| endpoint | hook | query key | polling | stale time | module | defined at |
|---|---|---|---|---|---|---|
| GET /matching/invites | useReceivedInvites | inviteKeys.received |  |  | [matching](../api/matching.md) | apps/web/src/features/matching/invite-api.ts:35 |
| GET /matching/tickets/current | useCurrentTicket | matchingKeys.current |  |  | [matching](../api/matching.md) | apps/web/src/features/matching/api.ts:67 |
| GET /matching/tickets/{id} | useTicket | matchingKeys.ticket(ticketId ?? 'none') | (query) => isPollingStatus(query.state.data?.status) ? MATCHING_TICKET_REFETCH_INTERVAL_MS : false |  | [matching](../api/matching.md) | apps/web/src/features/matching/api.ts:80 |
| GET /economy/wallet | useWallet | walletKeys.wallet |  |  | [economy](../api/economy.md) | apps/web/src/features/wallet/api.ts:23 |
| GET /notifications | useNotifications | notificationKeys.list |  |  | [notification](../api/notification.md) | apps/web/src/features/notification/api.ts:63 |
| GET /notifications/unread-count | useUnreadNotificationCount | notificationKeys.unreadCount | UNREAD_NOTIFICATION_COUNT_REFETCH_INTERVAL_MS |  | [notification](../api/notification.md) | apps/web/src/features/notification/api.ts:51 |

## Actions that change data (mutations)

| UI element and label | handler | hook | endpoint | module | call site |
|---|---|---|---|---|---|
| Button.onClick: (no literal label) | () => declineInvite.mutate(invite.id) | useDeclineInvite | POST /matching/invites/{id}/decline | [matching](../api/matching.md) | apps/web/src/features/matching/components/incoming-invites.tsx:141 |
| Button.onClick: (no literal label) | () => acceptInvite.mutate(invite.id, { onSuccess: (data) => { if (data === undefined) return; router.replace( invite.mat | useAcceptInvite | POST /matching/invites/{id}/accept | [matching](../api/matching.md) | apps/web/src/features/matching/components/incoming-invites.tsx:151 |
| (no matching UI element found; called from QueueStatusPanel) |  | useConfirmTicket | POST /matching/tickets/{id}/confirm | [matching](../api/matching.md) | apps/web/src/features/matching/components/queue-status-panel.tsx:143 |
| Button.onClick: (no literal label) | () => cancelTicket.mutate(undefined, { onSuccess: () => { activeDirectMatch.current = null; setTicketId(null); router.re | useCancelTicket | DELETE /matching/tickets/{id} | [matching](../api/matching.md) | apps/web/src/features/matching/components/queue-status-panel.tsx:158 |
| Button.onClick: (no literal label) | () => cancelTicket.mutate(undefined, { onSuccess: () => { activeDirectMatch.current = null; setTicketId(null); router.re | useCancelTicket | DELETE /matching/tickets/{id} | [matching](../api/matching.md) | apps/web/src/features/matching/components/queue-status-panel.tsx:326 |
| (no matching UI element found; called from MatchTypePicker) |  | useJoinQueue | POST /matching/tickets | [matching](../api/matching.md) | apps/web/src/features/matching/components/match-type-picker.tsx:69 |
| (no matching UI element found; called from DirectQueueStart) |  | useJoinQueue | POST /matching/tickets | [matching](../api/matching.md) | apps/web/src/features/matching/components/direct-queue-start.tsx:30 |
| Button.onClick: (no literal label) | handleSpeedup | useSpeedup | POST /matching/tickets/{id}/speedup | [matching](../api/matching.md) | apps/web/src/features/matching/components/speedup-button.tsx:37 |
| button.onClick: (no literal label) | () => { // Mark-read idempotent phía server — bấm lại item đã đọc chỉ điều hướng. if (unread) markRead.mutate(notificati | useMarkNotificationRead | POST /notifications/{notificationId}/read | [notification](../api/notification.md) | apps/web/src/features/notification/components/notification-bell.tsx:40 |

Other write endpoints reachable (called outside a detected .mutate): POST /notifications/web-push/subscription (registerWebPushSubscription apps/web/src/features/notification/api.ts:34); DELETE /notifications/web-push/subscription (unregisterWebPushSubscription apps/web/src/features/notification/api.ts:42)

## Interactive elements (18)

- button.onClick Thử lại -> () => void invitesQuery.refetch() (apps/web/src/features/matching/components/incoming-invites.tsx:79)
- Button.onClick (no literal label) -> () => declineInvite.mutate(invite.id) (apps/web/src/features/matching/components/incoming-invites.tsx:136)
- Button.onClick (no literal label) -> () => acceptInvite.mutate(invite.id, { onSuccess: (data) => { if (dat… (apps/web/src/features/matching/components/incoming-invites.tsx:145)
- button.onClick Nhắn tin ẩn danh -> () => void select('soul') (apps/web/src/features/matching/components/match-actions.tsx:71)
- button.onClick Kết nối bằng voice -> () => void select('voice') (apps/web/src/features/matching/components/match-actions.tsx:80)
- Button.onClick (no literal label) -> () => void currentTicketQuery.refetch() (apps/web/src/features/matching/components/queue-status-panel.tsx:204)
- Button.onClick Chọn lại kiểu ghép đôi -> () => setTicketId(null) (apps/web/src/features/matching/components/queue-status-panel.tsx:270)
- Button.onClick (no literal label) -> () => cancelTicket.mutate(undefined, { onSuccess: () => { activeDirec… (apps/web/src/features/matching/components/queue-status-panel.tsx:319)
- Button.onClick Tìm lại -> () => setTicketId(null) (apps/web/src/features/matching/components/queue-status-panel.tsx:411)
- form.onSubmit (no literal label) -> onSubmit (apps/web/src/features/matching/components/match-type-picker.tsx:82)
- Button.onClick Thử lại -> () => { started.current = false; start(); } (apps/web/src/features/matching/components/direct-queue-start.tsx:75)
- Button.onClick (no literal label) -> handleSpeedup (apps/web/src/features/matching/components/speedup-button.tsx:44)
- button.onClick (no literal label) -> () => { // Mark-read idempotent phía server — bấm lại item đã đọc chỉ… (apps/web/src/features/notification/components/notification-bell.tsx:36)
- button.onClick Thử lại -> () => void refetch() (apps/web/src/features/notification/components/notification-bell.tsx:116)
- button.onClick Đang tải… (common.loading) -> () => void fetchNextPage() (apps/web/src/features/notification/components/notification-bell.tsx:145)
- button.onClick 🔔 -> () => { void enableWebPush() .then(setStatus) .catch(() => setStatus(… (apps/web/src/features/notification/components/notification-bell.tsx:194)
- button.onClick unreadCount > 0             ? t('notifications.unread', { count: unre -> () => setOpen((value) => !value) (apps/web/src/features/notification/components/notification-bell.tsx:217)
- button.onClick t('notifications.close') -> () => setOpen(false) (apps/web/src/features/notification/components/notification-bell.tsx:240)

## Realtime events listened

- match.confirmed (apps/web/src/features/matching/components/queue-status-panel.tsx:102)
- (dynamic: event) (apps/web/src/shared/realtime/use-realtime-event.ts:17)

## Navigation destinations

- /discovery via link (apps/web/src/app/(app)/matching/page.tsx:97)
- /matching?match={param} via router.replace (apps/web/src/features/matching/components/match-actions.tsx:54)
- /matching?match={param}&start=1 via router.replace (apps/web/src/features/matching/components/match-actions.tsx:65)
- /matching?match=soul#match-queue via link (apps/web/src/features/matching/components/match-actions.tsx:93)
- /matching?match=voice#match-queue via link (apps/web/src/features/matching/components/match-actions.tsx:101)
- /matching via router.replace (apps/web/src/features/matching/components/queue-status-panel.tsx:330)
- /wallet via link (apps/web/src/shared/ui/page-header.tsx:20)

## Error codes handled in the FE

none referenced by code string (errors are shown through generic ApiError message handling)

## Guest and capability gating

- no isGuest check
- no capability check

## Form validation (zod)

- joinQueueSchema (apps/web/src/features/matching/join-queue-schema.ts:9): matchType=z.enum(['soul', 'voice']); useDiamond=z.boolean(); genderPreference=z.enum(['any', 'male', 'female'])

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

- apps/web/src/features/matching/api.spec.ts
- apps/web/src/features/matching/components/incoming-invites.spec.tsx
- apps/web/src/features/matching/components/match-actions.spec.tsx
- apps/web/src/features/matching/components/queue-status-panel.spec.tsx
- apps/web/src/features/matching/components/speedup-button.spec.tsx
- apps/web/src/features/matching/join-queue-schema.spec.ts
- apps/web/src/features/notification/api.spec.ts
- apps/web/src/features/notification/components/notification-bell.spec.tsx
- apps/web/src/features/notification/notification-copy.spec.ts
- apps/web/src/features/wallet/components/payos-payment-status.spec.tsx
- apps/web/src/features/wallet/components/topup-packages.spec.tsx
- apps/web/src/features/wallet/components/vip-plans.spec.tsx
- apps/web/src/features/wallet/components/wallet-balance.spec.tsx

## Backend

modules: [matching](../modules/matching.md), [economy](../modules/economy.md), [notification](../modules/notification.md)

service-map flows touching these endpoints: [matching-ticket-status-cancel-speedup](../flows/matching-ticket-status-cancel-speedup.md), [matching-to-call](../flows/matching-to-call.md), [matching-invite-accept](../flows/matching-invite-accept.md), [matching-invite-create](../flows/matching-invite-create.md), [matching-invite-decline-cancel](../flows/matching-invite-decline-cancel.md), [message-push-notification](../flows/message-push-notification.md)
