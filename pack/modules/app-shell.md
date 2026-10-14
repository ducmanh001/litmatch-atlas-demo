# Module app-shell

id: module:app-shell
kind: core-api shell
code: apps/core-api/src/app

Cung cấp /health, /health/live, /health/ready (probe cho Docker/k8s) và GET /api/v1/capabilities (hợp đồng tính năng runtime cho web/admin). Không có bảng riêng, không chứa business logic; chỉ kiểm tra Postgres/Redis và phản chiếu cấu hình env của các module khác.

## Facts

- API: 4 endpoints, 4 public, 0 admin-only, 0 require Idempotency-Key, 1 with own throttle
- env vars read: LOG_LEVEL, NODE_ENV, DATABASE_URL, DATABASE_POOL_MAX, DATABASE_POOL_ACQUIRE_TIMEOUT_MS, THROTTLE_STORAGE, HTTP_TRUST_PROXY_HOPS, THROTTLE_TTL_SECONDS, THROTTLE_LIMIT, REDIS_URL, CAPABILITY_MAINTENANCE_FEATURES, ECONOMY_IAP_VERIFIER, VIDEO_UPLOAD_ENABLED, NOTIFICATION_PUSH_PROVIDER, AUTH_PHONE_OTP_ENABLED
- tests: 3 unit spec files, 0 integration spec files
- history: 26 commits (4 fixes), last 2026-10-09
- features: runtime-capabilities

## Where to read next

- API contract: ../api/app-shell.md
- why it talks to other services: ../service-map/app-shell.md and ../service-map/app-shell-techniques.md
- flows involving it: [app-shell-health-and-readiness](../flows/app-shell-health-and-readiness.md)

## Linked issues (heuristic)

- P065 [open] Production chạy bản cũ: route profile 404, thông báo Facebook cũ
- P076 [fixed] Rate limit trong bộ nhớ từng pod + trần kết nối Postgres

See ../issues/index.md.
