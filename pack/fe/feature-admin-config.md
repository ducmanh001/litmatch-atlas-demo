# admin feature config

id: fe:feature:admin:config
directory: apps/admin/src/features/config/ (2 source files, 1 components, 1 spec files)

routes whose data comes from this feature: [/config](route-admin-config.md)

## Hooks (data layer)

| hook | endpoint(s) | query key | polling | stale | invalidates on success | defined at |
|---|---|---|---|---|---|---|
| useEconomyCatalog | GET /admin/config/economy-catalog | configKeys.catalog |  |  |  | apps/admin/src/features/config/api.ts:17 |
| useSetIapProductActive | PATCH /admin/config/iap-products/{productId} |  |  |  | invalidateQueries({ queryKey: configKeys.catalog }) | apps/admin/src/features/config/api.ts:29 |
| useSetVipPlanActive | PATCH /admin/config/vip-plans/{id} |  |  |  | invalidateQueries({ queryKey: configKeys.catalog }) | apps/admin/src/features/config/api.ts:47 |
| useBroadcastNotification | POST /admin/notifications/broadcast |  |  |  |  | apps/admin/src/features/config/api.ts:65 |

## Components

- apps/admin/src/features/config/pages/config-page.tsx: 1 interactive elements, uses useT, useEconomyCatalog, useSetIapProductActive, useSetVipPlanActive, useState, useBroadcastNotification

## Tests

- apps/admin/src/features/config/pages/config-page.spec.tsx
