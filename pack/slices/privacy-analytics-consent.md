# Feature slice: privacy-analytics-consent

id: slice:privacy-analytics-consent
status: implemented; owner: web/shared-analytics

Hosted analytics with quota controls and an explicit privacy warning

This file follows ONE feature through every layer. Layers are joined by the actual endpoint calls found in the frontend code, so it shows what the code does, not what a plan says.

## 1. Product contract and docs

- registry contracts: docs/runbooks/posthog-cloud.md
- registry verification tests: apps/web/src/shared/analytics/product-analytics.spec.ts, apps/web/src/shared/analytics/product-analytics.spec.ts, apps/web/src/shared/analytics/product-analytics-components.spec.tsx
- service docs: 

## 2. Frontend

Frontend-only feature owned by web/shared-analytics; see fe/ files for shared analytics and consent code (apps/web/src/shared/analytics/product-analytics.ts, apps/web/src/shared/analytics/product-analytics.ts, apps/web/src/shared/analytics/product-analytics.ts, apps/web/src/shared/analytics/product-analytics-components.tsx).

## 3. API

This feature has no REST endpoint of its own.

## 4. Backend flows (service map)

No service-map flow covers these endpoints (they are confirmed local-only).

## 5. Data

- tables owned by the module(s): none (see )

## 6. Events

No realtime event is produced by these modules.

## 7. Errors

- error code groups: none
- codes the FE references for this feature: none

## 8. Configuration

env vars read by the module(s): none (see ../config/)

## 9. Tests


- e2e files that call these endpoints: none
- FE specs: none

## 10. Known issues (public subset of the ledger)

none linked

See ../issues/index.md.
