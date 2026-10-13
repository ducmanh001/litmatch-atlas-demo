# Feature slice: auth-session

id: slice:auth-session
status: implemented; owner: core-api/auth

Guest, OTP, social login, identity upgrade and rotating browser sessions

This file follows ONE feature through every layer. Layers are joined by the actual endpoint calls found in the frontend code, so it shows what the code does, not what a plan says.

## 1. Product contract and docs

- registry contracts: openapi/core-api.json, docs/06-domain-rules.md, docs/adr/0007-httponly-cookie-refresh-token.md
- registry verification tests: apps/core-api/src/modules/auth/auth.service.spec.ts
- service docs: docs/services/auth-service.md

## 2. Frontend

| route | app | endpoints of this feature it calls | page |
|---|---|---|---|
| [/login](../fe/route-web-login.md) | web | POST /auth/otp/request; POST /auth/otp/verify; POST /auth/guest; POST /auth/social | app/login/page.tsx |
| [/login](../fe/route-admin-login.md) | admin | POST /auth/otp/request; POST /auth/otp/verify; POST /auth/social | shared/auth/login-page.tsx |
| [/more](../fe/route-web-more.md) | web | POST /auth/logout | app/(app)/more/page.tsx |
| [/profile](../fe/route-web-profile.md) | web | POST /auth/logout | app/(app)/profile/page.tsx |
| [/](../fe/route-admin-root.md) | admin | POST /auth/logout | app/app-shell.tsx |

FE features (directories): [web/notification](../fe/feature-web-notification.md), [web/wallet](../fe/feature-web-wallet.md), [web/feed](../fe/feature-web-feed.md), [web/friend-chat](../fe/feature-web-friend-chat.md), [web/profile](../fe/feature-web-profile.md), [admin/moderation](../fe/feature-admin-moderation.md)

## 3. API

| endpoint | auth | idempotency | throttle | FE callers |
|---|---|---|---|---|
| POST /auth/guest ([auth](../api/auth.md)) | public |  | { default: { limit: 5, ttl: minutes(1) } } | 1 |
| POST /auth/otp/request ([auth](../api/auth.md)) | public |  | { default: { limit: 5, ttl: minutes(1) } } | 2 |
| POST /auth/otp/verify ([auth](../api/auth.md)) | public |  | { default: { limit: 10, ttl: minutes(1) } } | 2 |
| POST /auth/social ([auth](../api/auth.md)) | public |  | { default: { limit: 10, ttl: minutes(1) } } | 2 |
| POST /auth/refresh ([auth](../api/auth.md)) | public + @UseGuards(CsrfGuard) |  |  | 0 |
| POST /auth/logout ([auth](../api/auth.md)) | public + @UseGuards(CsrfGuard) |  |  | 0 |
| POST /auth/upgrade/otp ([auth](../api/auth.md)) | JWT |  | { default: { limit: 10, ttl: minutes(1) } } | 0 |
| POST /auth/upgrade/social ([auth](../api/auth.md)) | JWT |  | { default: { limit: 10, ttl: minutes(1) } } | 0 |
| GET /auth/search-by-phone ([auth](../api/auth.md)) | JWT |  | { default: { limit: 10, ttl: minutes(1) } } | 0 |


## 4. Backend flows (service map)

- [auth-guest-upgrade](../flows/auth-guest-upgrade.md): Nâng cấp tài khoản khách thành tài khoản thật (2 endpoints of this feature)
- [auth-otp-login](../flows/auth-otp-login.md): Đăng nhập bằng OTP điện thoại (2 endpoints of this feature)
- [auth-refresh-and-logout](../flows/auth-refresh-and-logout.md): Làm mới phiên và đăng xuất (cookie httpOnly + CSRF) (2 endpoints of this feature)
- [auth-guest-login-and-socket](../flows/auth-guest-login-and-socket.md): Đăng nhập khách rồi mở kết nối realtime (1 endpoints of this feature)
- [auth-search-by-phone](../flows/auth-search-by-phone.md): Tìm người theo số điện thoại (1 endpoints of this feature)
- [auth-social-login](../flows/auth-social-login.md): Đăng nhập bằng Apple, Google hoặc Facebook (1 endpoints of this feature)

## 5. Data

- tables owned by the module(s): `auth_identities`, `phone_otps`, `refresh_tokens` (see ../db/auth.md)

## 6. Events

No realtime event is produced by these modules.

## 7. Errors

- error code groups: [AuthErrors](../errors/autherrors.md)
- codes the FE references for this feature: none

## 8. Configuration

env vars read by the module(s): `NODE_ENV`, `AUTH_CROSS_ORIGIN_DEV`, `AUTH_COOKIE_SAME_SITE`, `AUTH_REFRESH_TTL_DAYS`, `JWT_SECRET`, `AUTH_PHONE_OTP_ENABLED`, `AUTH_GUEST_DEVICE_TOKEN_TTL_DAYS`, `AUTH_GUEST_DEVICE_TOKEN_SECRET`, `AUTH_OTP_REQUESTS_PER_HOUR`, `AUTH_OTP_TTL_SECONDS`, `AUTH_OTP_MAX_ATTEMPTS`, `AUTH_OTP_PEPPER`, `AUTH_FACEBOOK_APP_ID`, `AUTH_FACEBOOK_APP_SECRET`, `JWT_ACCESS_TTL_SECONDS` (see ../config/)

## 9. Tests

- auth: 7 unit spec files, 0 integration spec files
- e2e files that call these endpoints: apps/core-api-e2e/src/core-api/auth-cookie-flow.spec.ts, apps/web/e2e/login-and-join-matching-queue.spec.ts
- FE specs: apps/web/src/features/more/components/more-view.spec.tsx, apps/web/src/features/notification/api.spec.ts, apps/web/src/features/notification/components/notification-bell.spec.tsx, apps/web/src/features/notification/notification-copy.spec.ts, apps/web/src/features/wallet/components/payos-payment-status.spec.tsx, apps/web/src/features/wallet/components/topup-packages.spec.tsx, apps/web/src/features/wallet/components/vip-plans.spec.tsx, apps/web/src/features/wallet/components/wallet-balance.spec.tsx, apps/web/src/features/feed/api.spec.tsx, apps/web/src/features/feed/components/feed-banners.spec.tsx, apps/web/src/features/feed/components/like-button.spec.tsx, apps/web/src/features/feed/components/post-composer.spec.tsx

## 10. Known issues (public subset of the ledger)

- P018 [fixed] JWT access token trùng nhau khi cấp trong cùng giây (LRN-010)

See ../issues/index.md.
