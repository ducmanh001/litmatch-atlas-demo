# admin route /config

id: fe:route:admin:/config
file: apps/admin/src/features/config/pages/config-page.tsx:78
layout: admin (RequireAuth + role)
reached source files: 13 (static import closure); data features: config

## UI action map card (human-authored, not verified against code)

CONFIG /config: Catalog commerce và broadcast

Config actions:
  - Bật / ẩn IAP product
  - Bật / ẩn VIP plan
  - Xem price / diamonds / days
  - Kiểm tra catalog user-facing
Notification actions:
  - Soạn title / body / audience
  - Broadcast → user Notifications

## Data it reads (GET)

| endpoint | hook | query key | polling | stale time | module | defined at |
|---|---|---|---|---|---|---|
| GET /admin/config/economy-catalog | useEconomyCatalog | configKeys.catalog |  |  | [admin](../api/admin.md) | apps/admin/src/features/config/api.ts:21 |

## Actions that change data (mutations)

| UI element and label | handler | hook | endpoint | module | call site |
|---|---|---|---|---|---|
| (no matching UI element found; called from togglePackage) |  | useSetIapProductActive | PATCH /admin/config/iap-products/{productId} | [admin](../api/admin.md) | apps/admin/src/features/config/pages/config-page.tsx:48 |
| (no matching UI element found; called from togglePackage) |  | useSetVipPlanActive | PATCH /admin/config/vip-plans/{id} | [admin](../api/admin.md) | apps/admin/src/features/config/pages/config-page.tsx:49 |
| form.onSubmit: (no literal label) | (event) => { event.preventDefault(); const normalizedTitle = title.trim(); const normalizedBody = body.trim(); if (norma | useBroadcastNotification | POST /admin/notifications/broadcast | [admin](../api/admin.md) | apps/admin/src/features/config/pages/config-page.tsx:206 |

## Interactive elements (1)

- form.onSubmit (no literal label) -> (event) => { event.preventDefault(); const normalizedTitle = title.tr… (apps/admin/src/features/config/pages/config-page.tsx:196)

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

## i18n keys used (38)

- auth.changePhone: ← Đổi số điện thoại
- auth.guestAvailable: Có thể dùng tài khoản khách.
- common.enabled: 
- common.hidden: 
- common.loading: Đang tải…
- common.unknownError: Sự cố không xác định. Vui lòng tải lại.
- config.allUsers: 
- config.audience: 
- config.body: 
- config.bodyPlaceholder: 
- config.catalogTitle: 
- config.composeNotification: 
- config.days: 
- config.freeUsers: 
- config.loadingCatalog: 
- config.notificationHint: 
- config.notificationSent: 
- config.onSale: 
- config.packageName: 
- config.packageType: 
- config.packageUpdated: 
- config.price: 
- config.requiredNotification: 
- config.sendNotification: 
- config.sending: 
- … 13 more, see i18n-*.md

## Tests

- apps/admin/src/features/config/pages/config-page.spec.tsx

## Backend

modules: [admin](../modules/admin.md)

service-map flows touching these endpoints: [admin-economy-inspect-and-config](../flows/admin-economy-inspect-and-config.md), [admin-broadcast-notification](../flows/admin-broadcast-notification.md)
