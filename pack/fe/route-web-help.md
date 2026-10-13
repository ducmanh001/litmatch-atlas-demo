# web route /help

id: fe:route:web:/help
file: apps/web/src/app/(public)/help/page.tsx
layout: public
reached source files: 6 (static import closure); data features: support

## UI action map card (human-authored, not verified against code)

MORE /help /privacy: Settings, support, account

Navigation actions:
  - Xem/chỉnh sửa Profile
  - Mở Discovery / Video / Party / Feed
  - Premium / Wallet
  - Mời bạn bè
Settings actions:
  - Theme / Language
  - Privacy / Block / Report
  - FAQ / Gửi feedback
  - Logout → Login

## Data it reads (GET)

| endpoint | hook | query key | polling | stale time | module | defined at |
|---|---|---|---|---|---|---|
| GET /support/tickets/me | useMySupportTickets | supportKeys.mine |  |  | [support](../api/support.md) | apps/web/src/features/support/api.ts:19 |

## Actions that change data (mutations)

| UI element and label | handler | hook | endpoint | module | call site |
|---|---|---|---|---|---|
| button.onClick: (no literal label) | () => { createTicket.mutate( { category, message: feedback, idempotencyKey: key }, { onSuccess: () => { setFeedback(''); | useCreateSupportTicket | POST /support/tickets | [support](../api/support.md) | apps/web/src/app/(public)/help/page.tsx:190 |

## Interactive elements (0)

none

## Realtime events listened

none

## Navigation destinations

none

## Error codes handled in the FE

none referenced by code string (errors are shown through generic ApiError message handling)

## Guest and capability gating

- no isGuest check
- no capability check

## Browser storage and cookies

- cookie-write  (apps/web/src/shared/i18n/locale-store.ts:37)

## i18n keys used (0)



## Tests

- apps/web/src/app/(public)/help/page.spec.tsx

## Backend

modules: [support](../modules/support.md)

service-map flows touching these endpoints: [admin-support-tickets](../flows/admin-support-tickets.md)
