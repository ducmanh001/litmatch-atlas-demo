# Error codes EconomyErrors

id: errors:EconomyErrors
module: module:economy

| code | http status at throw sites | throw sites | meaning | declared |
|---|---|---|---|---|
| ECONOMY_IAP_DISABLED | FORBIDDEN | 1 |  | apps/core-api/src/modules/economy/economy.errors.ts:3 |
| ECONOMY_WALLET_INSUFFICIENT_BALANCE | UNPROCESSABLE_ENTITY | 2 |  | apps/core-api/src/modules/economy/economy.errors.ts:4 |
| ECONOMY_TRANSACTION_IDEMPOTENCY_CONFLICT | CONFLICT | 1 |  | apps/core-api/src/modules/economy/economy.errors.ts:5 |
| ECONOMY_TRANSACTION_NOT_FOUND | NOT_FOUND | 1 |  | apps/core-api/src/modules/economy/economy.errors.ts:6 |
| ECONOMY_TRANSACTION_ALREADY_REVERSED | CONFLICT | 2 |  | apps/core-api/src/modules/economy/economy.errors.ts:7 |
| ECONOMY_IAP_RECEIPT_INVALID | BAD_REQUEST | 8 |  | apps/core-api/src/modules/economy/economy.errors.ts:8 |
| ECONOMY_IAP_PROVIDER_UNAVAILABLE |  | 0 |  | apps/core-api/src/modules/economy/economy.errors.ts:9 |
| ECONOMY_IAP_PRODUCT_UNKNOWN | NOT_FOUND,BAD_REQUEST | 2 |  | apps/core-api/src/modules/economy/economy.errors.ts:10 |
| ECONOMY_VIP_PLAN_UNKNOWN | NOT_FOUND | 2 |  | apps/core-api/src/modules/economy/economy.errors.ts:11 |
| ECONOMY_WEBHOOK_SIGNATURE_INVALID | UNAUTHORIZED | 5 |  | apps/core-api/src/modules/economy/economy.errors.ts:12 |
| ECONOMY_PAYOS_DISABLED |  | 0 |  | apps/core-api/src/modules/economy/economy.errors.ts:13 |
| ECONOMY_PAYOS_PACKAGE_UNKNOWN | NOT_FOUND | 1 |  | apps/core-api/src/modules/economy/economy.errors.ts:14 |
| ECONOMY_PAYOS_ORDER_NOT_FOUND | NOT_FOUND | 1 |  | apps/core-api/src/modules/economy/economy.errors.ts:15 |
| ECONOMY_PAYOS_ORDER_IDEMPOTENCY_CONFLICT | CONFLICT | 1 |  | apps/core-api/src/modules/economy/economy.errors.ts:16 |
| ECONOMY_PAYOS_PROVIDER_UNAVAILABLE |  | 0 |  | apps/core-api/src/modules/economy/economy.errors.ts:17 |
| ECONOMY_PAYOS_WEBHOOK_INVALID | BAD_REQUEST | 1 |  | apps/core-api/src/modules/economy/economy.errors.ts:18 |
| ECONOMY_PAYOS_WEBHOOK_MISMATCH | BAD_REQUEST | 1 |  | apps/core-api/src/modules/economy/economy.errors.ts:19 |
| ECONOMY_PAYOS_GUEST_FORBIDDEN | FORBIDDEN | 1 |  | apps/core-api/src/modules/economy/economy.errors.ts:20 |
| ECONOMY_TRANSACTION_CURSOR_INVALID | BAD_REQUEST | 1 |  | apps/core-api/src/modules/economy/economy.errors.ts:21 |
