# Feature slice: economy-ledger

id: slice:economy-ledger
status: implemented; owner: core-api/economy

Wallet, IAP catalog, VIP and double-entry ledger

This file follows ONE feature through every layer. Layers are joined by the actual endpoint calls found in the frontend code, so it shows what the code does, not what a plan says.

## 1. Product contract and docs

- registry contracts: openapi/core-api.json, docs/services/economy-service.md, docs/06-domain-rules.md
- registry verification tests: apps/core-api/src/modules/economy/economy.integration.spec.ts
- service docs: docs/services/economy-service.md

## 2. Frontend

| route | app | endpoints of this feature it calls | page |
|---|---|---|---|
| [/wallet](../fe/route-web-wallet.md) | web | GET /economy/wallet; GET /economy/payos/packages; GET /economy/payos/orders/{orderId}; GET /economy/vip/plans; POST /economy/payos/orders; POST /economy/vip/purchase | app/(app)/wallet/page.tsx |
| [/discovery](../fe/route-web-discovery.md) | web | GET /economy/wallet | app/(app)/discovery/page.tsx |
| [/feed](../fe/route-web-feed.md) | web | GET /economy/wallet | app/(app)/feed/page.tsx |
| [/home](../fe/route-web-home.md) | web | GET /economy/wallet | app/(app)/home/page.tsx |
| [/matching](../fe/route-web-matching.md) | web | GET /economy/wallet | app/(app)/matching/page.tsx |
| [/messages](../fe/route-web-messages.md) | web | GET /economy/wallet | app/(app)/messages/page.tsx |
| [/more](../fe/route-web-more.md) | web | GET /economy/wallet | app/(app)/more/page.tsx |
| [/party](../fe/route-web-party.md) | web | GET /economy/wallet | app/(app)/party/page.tsx |
| [/profile](../fe/route-web-profile.md) | web | GET /economy/wallet | app/(app)/profile/page.tsx |

FE features (directories): [web/notification](../fe/feature-web-notification.md), [web/wallet](../fe/feature-web-wallet.md), [web/discovery](../fe/feature-web-discovery.md), [web/feed](../fe/feature-web-feed.md), [web/party-room](../fe/feature-web-party-room.md), [web/matching](../fe/feature-web-matching.md), [web/friend-chat](../fe/feature-web-friend-chat.md), [web/profile](../fe/feature-web-profile.md)

UI action map card for /wallet: Wallet actions: Xem balance / Xem VIP tier / expiry / Xem package catalog / Nạp Diamond | Commerce actions: Chọn gói VIP / Tạo payment order / Kiểm tra payment / Diamond → Gift / Matching

User actions that mutate this feature's data:
- useCreatePayosOrder -> POST /economy/payos/orders (apps/web/src/features/wallet/components/topup-packages.tsx:117)
- usePurchaseVip -> POST /economy/vip/purchase (apps/web/src/features/wallet/components/vip-plans.tsx:89)

## 3. API

| endpoint | auth | idempotency | throttle | FE callers |
|---|---|---|---|---|
| GET /economy/wallet ([economy](../api/economy.md)) | JWT |  |  | 1 |
| GET /economy/iap/products ([economy](../api/economy.md)) | JWT |  |  | 1 |
| GET /economy/payos/packages ([economy](../api/economy.md)) | JWT |  |  | 1 |
| POST /economy/payos/orders ([economy](../api/economy.md)) | JWT | required | { default: { limit: 10, ttl: minutes(1) } } | 1 |
| GET /economy/payos/orders/{orderId} ([economy](../api/economy.md)) | JWT |  |  | 0 |
| GET /economy/vip/plans ([economy](../api/economy.md)) | JWT |  |  | 1 |
| POST /economy/iap/verify ([economy](../api/economy.md)) | JWT |  | { default: { limit: 10, ttl: minutes(1) } } | 1 |
| POST /economy/vip/purchase ([economy](../api/economy.md)) | JWT | required |  | 1 |
| GET /economy/transactions ([economy](../api/economy.md)) | JWT |  |  | 0 |
| POST /economy/webhooks/apple ([economy](../api/economy.md)) | public |  | { default: { limit: 60, ttl: minutes(1) } } | 0 |
| POST /economy/webhooks/google/rtdn ([economy](../api/economy.md)) | public |  | { default: { limit: 60, ttl: minutes(1) } } | 0 |
| POST /economy/webhooks/payos ([economy](../api/economy.md)) | public |  | { default: { limit: 120, ttl: minutes(1) } } | 0 |


## 4. Backend flows (service map)

- [payos-topup-webhook](../flows/payos-topup-webhook.md): Nạp kim cương qua PayOS và webhook (3 endpoints of this feature)
- [iap-store-notifications-and-refund-poll](../flows/iap-store-notifications-and-refund-poll.md): Hoàn tiền IAP: webhook Apple/Google và job quét refund (2 endpoints of this feature)
- [vip-purchase-iap](../flows/vip-purchase-iap.md): Mua VIP và xác minh biên nhận IAP (2 endpoints of this feature)

## 5. Data

- tables owned by the module(s): `iap_products`, `iap_receipts`, `ledger_accounts`, `ledger_entries`, `outbox_events`, `payos_packages`, `payos_payment_orders`, `transactions`, `vip_plans`, `wallets` (see ../db/economy.md)
- status vocabularies enforced by CHECK:
  - ledger_entries: debit | credit
  - payos_payment_orders: pending | paid | cancelled | expired

## 6. Events

No realtime event is produced by these modules.

## 7. Errors

- error code groups: [EconomyErrors](../errors/economyerrors.md)
- codes the FE references for this feature: none

## 8. Configuration

env vars read by the module(s): `ECONOMY_APPLE_SHARED_SECRET`, `ECONOMY_APPLE_ISSUER_ID`, `ECONOMY_APPLE_KEY_ID`, `ECONOMY_APPLE_BUNDLE_ID`, `ECONOMY_APPLE_PRIVATE_KEY`, `ECONOMY_APPLE_SERVER_API_ENV`, `ECONOMY_GOOGLE_PACKAGE_NAME`, `ECONOMY_GOOGLE_SA_EMAIL`, `ECONOMY_GOOGLE_SA_PRIVATE_KEY`, `ECONOMY_STORE_HTTP_TIMEOUT_MS`, `ECONOMY_IAP_VERIFIER`, `ECONOMY_APPLE_WEBHOOK_VERIFIER`, `ECONOMY_GOOGLE_RTDN_VERIFIER`, `ECONOMY_REFUND_POLL_ENABLED`, `ECONOMY_REFUND_POLL_INTERVAL_MS`, `ECONOMY_REFUND_POLL_WINDOW_DAYS`, `ECONOMY_OUTBOX_RELAY_ENABLED`, `ECONOMY_OUTBOX_RELAY_INTERVAL_MS`, `ECONOMY_OUTBOX_MAX_ATTEMPTS`, `ECONOMY_RECONCILIATION_ENABLED`, `ECONOMY_RECONCILIATION_FAST_INTERVAL_MS`, `ECONOMY_RECONCILIATION_INTERVAL_MS`, `NODE_ENV`, `ECONOMY_APPLE_ROOT_CA_PEM`, `ECONOMY_GOOGLE_RTDN_AUDIENCE`, `ECONOMY_GOOGLE_RTDN_SERVICE_ACCOUNT_EMAIL`, `PAYOS_API_BASE_URL`, `PAYOS_HTTP_TIMEOUT_MS`, `PAYOS_CLIENT_ID`, `PAYOS_API_KEY`, `PAYOS_CHECKSUM_KEY`, `PAYOS_ORDER_EXPIRES_SECONDS`, `PAYOS_WEB_WALLET_URL` (see ../config/)

## 9. Tests

- economy: 7 unit spec files, 1 integration spec files
- e2e files that call these endpoints: apps/core-api-e2e/src/core-api/economy-flow.spec.ts
- FE specs: apps/web/src/features/notification/api.spec.ts, apps/web/src/features/notification/components/notification-bell.spec.tsx, apps/web/src/features/notification/notification-copy.spec.ts, apps/web/src/features/wallet/components/payos-payment-status.spec.tsx, apps/web/src/features/wallet/components/topup-packages.spec.tsx, apps/web/src/features/wallet/components/vip-plans.spec.tsx, apps/web/src/features/wallet/components/wallet-balance.spec.tsx, apps/web/src/app/(app)/discovery/page.spec.tsx, apps/web/src/features/discovery/components/discovery-detail-sheet.spec.tsx, apps/web/src/features/feed/api.spec.tsx, apps/web/src/features/feed/components/feed-banners.spec.tsx, apps/web/src/features/feed/components/like-button.spec.tsx

## 10. Known issues (linked from the ledger)

- P094 [open] Giới hạn đã biết: advisory lock Palm/Movie Match, outbox chưa có consumer/retention
- P001 [fixed] Guard ledger chặn nhầm bút toán đảo (refund/chargeback)
- P006 [fixed] Swagger thiếu cursor pagination → client sinh kiểu never
- P087 [fixed] Bỏ qua kiểm bundleId Apple khi env rỗng

See ../issues/index.md.
