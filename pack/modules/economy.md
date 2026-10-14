# Module economy

id: module:economy
kind: core-api module
code: apps/core-api/src/modules/economy

Sở hữu sổ cái double-entry append-only (ledger_entries), snapshot ví wallets, catalog IAP/VIP/payOS, receipt và order nạp tiền; LedgerService là writer duy nhất vào ledger/wallet cho cả hệ thống. Không sở hữu hiệu ứng nghiệp vụ của nơi tiêu diamond (boost hàng đợi, ghi GiftEvent, cấp item avatar) và không phát realtime; các module khác chỉ gọi qua EconomyService trong index.ts.

## Facts

- tables owned: iap_products, iap_receipts, ledger_accounts, ledger_entries, outbox_events, payos_packages, payos_payment_orders, transactions, vip_plans, wallets
- API: 12 endpoints, 3 public, 0 admin-only, 2 require Idempotency-Key, 5 with own throttle
- jobs: IapRefundPollService, OutboxRelayService, ReconciliationService, ReconciliationService
- env vars read: ECONOMY_APPLE_SHARED_SECRET, ECONOMY_APPLE_ISSUER_ID, ECONOMY_APPLE_KEY_ID, ECONOMY_APPLE_BUNDLE_ID, ECONOMY_APPLE_PRIVATE_KEY, ECONOMY_APPLE_SERVER_API_ENV, ECONOMY_GOOGLE_PACKAGE_NAME, ECONOMY_GOOGLE_SA_EMAIL, ECONOMY_GOOGLE_SA_PRIVATE_KEY, ECONOMY_STORE_HTTP_TIMEOUT_MS, ECONOMY_IAP_VERIFIER, ECONOMY_APPLE_WEBHOOK_VERIFIER, ECONOMY_GOOGLE_RTDN_VERIFIER, ECONOMY_REFUND_POLL_ENABLED, ECONOMY_REFUND_POLL_INTERVAL_MS, ECONOMY_REFUND_POLL_WINDOW_DAYS, ECONOMY_OUTBOX_RELAY_ENABLED, ECONOMY_OUTBOX_RELAY_INTERVAL_MS, ECONOMY_OUTBOX_MAX_ATTEMPTS, ECONOMY_RECONCILIATION_ENABLED, ECONOMY_RECONCILIATION_FAST_INTERVAL_MS, ECONOMY_RECONCILIATION_INTERVAL_MS, NODE_ENV, ECONOMY_APPLE_ROOT_CA_PEM, ECONOMY_GOOGLE_RTDN_AUDIENCE, ECONOMY_GOOGLE_RTDN_SERVICE_ACCOUNT_EMAIL, PAYOS_API_BASE_URL, PAYOS_HTTP_TIMEOUT_MS, PAYOS_CLIENT_ID, PAYOS_API_KEY, PAYOS_CHECKSUM_KEY, PAYOS_ORDER_EXPIRES_SECONDS, PAYOS_WEB_WALLET_URL
- error codes: 19 in EconomyErrors (3 used indirectly, not via new DomainException)
- tests: 7 unit spec files, 1 integration spec files
- history: 30 commits (4 fixes), last 2026-08-13
- docs: docs/services/economy-service.md (last changed 2026-10-09)
- features: economy-ledger

## Where to read next

- tables: ../db/economy.md
- API contract: ../api/economy.md
- why it talks to other services: ../service-map/economy.md and ../service-map/economy-techniques.md
- error codes: ../errors/economyerrors.md
- flows involving it: [admin-ban-and-refund](../flows/admin-ban-and-refund.md), [admin-broadcast-notification](../flows/admin-broadcast-notification.md), [admin-dashboard](../flows/admin-dashboard.md), [admin-economy-inspect-and-config](../flows/admin-economy-inspect-and-config.md), [avatar-buy-item](../flows/avatar-buy-item.md), [economy-outbox-relay](../flows/economy-outbox-relay.md), [economy-reconciliation-and-startup-guards](../flows/economy-reconciliation-and-startup-guards.md), [gift-in-party-room](../flows/gift-in-party-room.md), [gift-on-video](../flows/gift-on-video.md), [gift-to-profile-chat-gate](../flows/gift-to-profile-chat-gate.md), [iap-store-notifications-and-refund-poll](../flows/iap-store-notifications-and-refund-poll.md), [matching-ticket-status-cancel-speedup](../flows/matching-ticket-status-cancel-speedup.md), [matching-to-call](../flows/matching-to-call.md), [payos-topup-webhook](../flows/payos-topup-webhook.md), [vip-purchase-iap](../flows/vip-purchase-iap.md)

## Linked issues (heuristic)

- P094 [open] Giới hạn đã biết: advisory lock Palm/Movie Match, outbox chưa có consumer/retention
- P001 [fixed] Guard ledger chặn nhầm bút toán đảo (refund/chargeback)
- P006 [fixed] Swagger thiếu cursor pagination → client sinh kiểu never
- P087 [fixed] Bỏ qua kiểm bundleId Apple khi env rỗng

See ../issues/index.md.
