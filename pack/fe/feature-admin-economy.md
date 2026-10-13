# admin feature economy

id: fe:feature:admin:economy
directory: apps/admin/src/features/economy/ (2 source files, 1 components, 1 spec files)

routes whose data comes from this feature: [/economy](route-admin-economy.md)

## Hooks (data layer)

| hook | endpoint(s) | query key | polling | stale | invalidates on success | defined at |
|---|---|---|---|---|---|---|
| useAdminWallet | GET /admin/economy/wallet/{userId} | economyKeys.wallet(userId ?? 'none') |  |  |  | apps/admin/src/features/economy/api.ts:16 |
| useAdminTransactions | GET /admin/economy/users/{userId}/transactions | economyKeys.transactions(userId ?? 'none') |  |  |  | apps/admin/src/features/economy/api.ts:34 |
| useRefundTransaction | POST /admin/economy/transactions/{id}/refund |  |  |  | invalidateQueries({ queryKey: economyKeys.wallet(userId) }); invalidateQueries({ queryKey: economyKeys.transactions(userId), }) | apps/admin/src/features/economy/api.ts:53 |

## Components

- apps/admin/src/features/economy/pages/economy-page.tsx: 3 interactive elements, uses useT, useState, useAdminWallet, useAdminTransactions, useRefundTransaction, useLocale

## Tests

- apps/admin/src/features/economy/pages/economy-page.spec.tsx
