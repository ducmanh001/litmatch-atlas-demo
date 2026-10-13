# web feature wallet

id: fe:feature:web:wallet
directory: apps/web/src/features/wallet/ (6 source files, 5 components, 4 spec files)

routes whose data comes from this feature: [/discovery](route-web-discovery.md), [/feed](route-web-feed.md), [/home](route-web-home.md), [/matching](route-web-matching.md), [/messages](route-web-messages.md), [/more](route-web-more.md), [/party](route-web-party.md), [/profile](route-web-profile.md), [/wallet](route-web-wallet.md)

## Hooks (data layer)

| hook | endpoint(s) | query key | polling | stale | invalidates on success | defined at |
|---|---|---|---|---|---|---|
| useWallet | GET /economy/wallet | walletKeys.wallet |  |  |  | apps/web/src/features/wallet/api.ts:19 |
| useIapProducts | GET /economy/iap/products | walletKeys.iapProducts |  | Infinity |  | apps/web/src/features/wallet/api.ts:29 |
| usePayosPackages | GET /economy/payos/packages | walletKeys.payosPackages |  | Infinity |  | apps/web/src/features/wallet/api.ts:41 |
| usePayosOrder | GET /economy/payos/orders/{orderId} | walletKeys.payosOrder(orderId ?? '') |  |  |  | apps/web/src/features/wallet/api.ts:53 |
| useCreatePayosOrder | POST /economy/payos/orders |  |  |  |  | apps/web/src/features/wallet/api.ts:68 |
| useVipPlans | GET /economy/vip/plans | walletKeys.vipPlans |  | Infinity |  | apps/web/src/features/wallet/api.ts:85 |
| usePurchaseVip | POST /economy/vip/purchase |  |  |  | invalidateQueries({ queryKey: walletKeys.wallet }) | apps/web/src/features/wallet/api.ts:96 |
| useVerifyIap | POST /economy/iap/verify |  |  |  | invalidateQueries({ queryKey: walletKeys.wallet }) | apps/web/src/features/wallet/api.ts:114 |

## Components

- apps/web/src/features/wallet/components/payos-payment-status.tsx: 1 interactive elements, uses useSearchParams, usePayosOrder, useQueryClient, useEffect
- apps/web/src/features/wallet/components/topup-packages.tsx: 2 interactive elements, uses useCapabilities, usePayosPackages, useCreatePayosOrder, useIdempotencyKey
- apps/web/src/features/wallet/components/vip-plans.tsx: 1 interactive elements, uses useWallet, useVipPlans, usePurchaseVip, useLocale, useTranslation, useIdempotencyKey
- apps/web/src/features/wallet/components/wallet-balance.tsx: 2 interactive elements, uses useWallet, useLocale, useTranslation
- apps/web/src/features/wallet/components/wallet-tabs.tsx: 2 interactive elements, uses useState

## Tests

- apps/web/src/features/wallet/components/payos-payment-status.spec.tsx
- apps/web/src/features/wallet/components/topup-packages.spec.tsx
- apps/web/src/features/wallet/components/vip-plans.spec.tsx
- apps/web/src/features/wallet/components/wallet-balance.spec.tsx
