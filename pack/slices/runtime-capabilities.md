# Feature slice: runtime-capabilities

id: slice:runtime-capabilities
status: implemented; owner: core-api/app with admin/web consumers

Server-derived runtime availability contract for frontend providers

This file follows ONE feature through every layer. Layers are joined by the actual endpoint calls found in the frontend code, so it shows what the code does, not what a plan says.

## 1. Product contract and docs

- registry contracts: openapi/core-api.json, docs/12-frontend-architecture.md, .env.example
- registry verification tests: apps/core-api/src/app/capabilities.service.spec.ts, apps/web/src/shared/capabilities/api.spec.tsx
- service docs: 

## 2. Frontend

| route | app | endpoints of this feature it calls | page |
|---|---|---|---|
| [/login](../fe/route-web-login.md) | web | GET /capabilities | app/login/page.tsx |
| [/wallet](../fe/route-web-wallet.md) | web | GET /capabilities | app/(app)/wallet/page.tsx |
| [/login](../fe/route-admin-login.md) | admin | GET /capabilities | shared/auth/login-page.tsx |

FE features (directories): [web/notification](../fe/feature-web-notification.md), [web/wallet](../fe/feature-web-wallet.md)

## 3. API

| endpoint | auth | idempotency | throttle | FE callers |
|---|---|---|---|---|
| GET /capabilities ([app-shell](../api/app-shell.md)) | public |  |  | 2 |
| GET /health ([app-shell](../api/app-shell.md)) | public |  |  | 0 |
| GET /health/live ([app-shell](../api/app-shell.md)) | public |  |  | 0 |
| GET /health/ready ([app-shell](../api/app-shell.md)) | public |  | { default: { limit: 30, ttl: minutes(1) } } | 0 |


## 4. Backend flows (service map)

- [app-shell-health-and-readiness](../flows/app-shell-health-and-readiness.md): Kiểm tra sẵn sàng của core-api (readiness) (1 endpoints of this feature)

## 5. Data

- tables owned by the module(s): none 

## 6. Events

No realtime event is produced by these modules.

## 7. Errors

- error code groups: none
- codes the FE references for this feature: none

## 8. Configuration

env vars read by the module(s): `LOG_LEVEL`, `NODE_ENV`, `DATABASE_URL`, `DATABASE_POOL_MAX`, `DATABASE_POOL_ACQUIRE_TIMEOUT_MS`, `THROTTLE_STORAGE`, `HTTP_TRUST_PROXY_HOPS`, `THROTTLE_TTL_SECONDS`, `THROTTLE_LIMIT`, `REDIS_URL`, `CAPABILITY_MAINTENANCE_FEATURES`, `ECONOMY_IAP_VERIFIER`, `VIDEO_UPLOAD_ENABLED`, `NOTIFICATION_PUSH_PROVIDER`, `AUTH_PHONE_OTP_ENABLED` (see ../config/)

## 9. Tests

- app-shell: 3 unit spec files, 0 integration spec files
- e2e files that call these endpoints: apps/core-api-e2e/src/core-api/core-api.spec.ts
- FE specs: apps/web/src/features/notification/api.spec.ts, apps/web/src/features/notification/components/notification-bell.spec.tsx, apps/web/src/features/notification/notification-copy.spec.ts, apps/web/src/features/wallet/components/payos-payment-status.spec.tsx, apps/web/src/features/wallet/components/topup-packages.spec.tsx, apps/web/src/features/wallet/components/vip-plans.spec.tsx, apps/web/src/features/wallet/components/wallet-balance.spec.tsx

## 10. Known issues (linked from the ledger)

- P065 [open] Production chạy bản cũ: route profile 404, thông báo Facebook cũ
- P076 [fixed] Rate limit trong bộ nhớ từng pod + trần kết nối Postgres

See ../issues/index.md.
