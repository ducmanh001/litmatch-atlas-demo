# admin route /economy

id: fe:route:admin:/economy
file: apps/admin/src/features/economy/pages/economy-page.tsx:62
layout: admin (RequireAuth + role)
reached source files: 15 (static import closure); data features: economy

## UI action map card (human-authored, not verified against code)

ECONOMY /economy: Wallet và giao dịch người dùng

Lookup actions:
  - Nhập user ID / tra wallet
  - Xem balance / earnings
  - Xem transaction history
Ledger actions:
  - Xem loại / trạng thái / delta
  - Refund transaction → audit
  - Nhập lý do hoàn tiền

## Data it reads (GET)

| endpoint | hook | query key | polling | stale time | module | defined at |
|---|---|---|---|---|---|---|
| GET /admin/economy/wallet/{userId} | useAdminWallet | economyKeys.wallet(userId ?? 'none') |  |  | [admin](../api/admin.md) | apps/admin/src/features/economy/api.ts:25 |
| GET /admin/economy/users/{userId}/transactions | useAdminTransactions | economyKeys.transactions(userId ?? 'none') |  |  | [admin](../api/admin.md) | apps/admin/src/features/economy/api.ts:43 |

## Actions that change data (mutations)

| UI element and label | handler | hook | endpoint | module | call site |
|---|---|---|---|---|---|
| (no matching UI element found; called from EconomyPage) |  | useRefundTransaction | POST /admin/economy/transactions/{id}/refund | [admin](../api/admin.md) | apps/admin/src/features/economy/pages/economy-page.tsx:154 |

## Interactive elements (3)

- Button.onClick economy.view (economy.view) -> () => setLookupUserId(userIdInput.trim() \|\| null) (apps/admin/src/features/economy/pages/economy-page.tsx:67)
- RefundForm.onSubmit (no literal label) -> onRefund (apps/admin/src/features/economy/pages/economy-page.tsx:239)
- form.onSubmit (no literal label) -> (e) => { e.preventDefault(); if (reason.trim() === '') return; onSubm… (apps/admin/src/features/economy/pages/economy-page.tsx:256)

## Realtime events listened

none

## Navigation destinations

none

## Error codes handled in the FE

none referenced by code string (errors are shown through generic ApiError message handling)

## Guest and capability gating

- no isGuest check
- no capability check

## Form validation (zod)

- envSchema (apps/admin/src/shared/env.ts:8): VITE_API_URL=z.url(); VITE_AUTH_GOOGLE_CLIENT_ID=z.string().min(1).optional(); VITE_SENTRY_DSN=z.url().optional(); VITE_SENTRY_ENVIRONMENT=z.string().min(1).default('production'); VITE_SENTRY_RELEASE=z.string().max(200).optional(); VITE_PHONE_OTP_ENABLED=z .enum(['true', 'false']) .default('true') .transform((value) => value === 'tr…

## Browser storage and cookies

- cookie-write  (apps/admin/src/shared/i18n/locale-store.ts:28)

## i18n keys used (30)

- auth.changePhone: ← Đổi số điện thoại
- auth.guestAvailable: Có thể dùng tài khoản khách.
- common.loading: Đang tải…
- common.tryAgain: 
- common.unknownError: Sự cố không xác định. Vui lòng tải lại.
- economy.balance: 
- economy.completed: 
- economy.delta: 
- economy.earnings: 
- economy.enterUserId: 
- economy.loadingTransactions: 
- economy.loadingWallet: 
- economy.noTransactions: 
- economy.refund: 
- economy.refundReason: 
- economy.refundSuccess: 
- economy.refunded: 
- economy.status: 
- economy.time: 
- economy.transactionsHint: 
- economy.type: 
- economy.userId: 
- economy.userIdPlaceholder: 
- economy.view: 
- language.english: English
- … 5 more, see i18n-*.md

## Tests

- apps/admin/src/features/economy/pages/economy-page.spec.tsx

## Backend

modules: [admin](../modules/admin.md)

service-map flows touching these endpoints: [admin-economy-inspect-and-config](../flows/admin-economy-inspect-and-config.md), [admin-ban-and-refund](../flows/admin-ban-and-refund.md)
