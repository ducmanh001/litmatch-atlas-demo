# Module auth

id: module:auth
kind: core-api module
code: apps/core-api/src/modules/auth

Sở hữu danh tính đăng nhập (auth_identities), OTP điện thoại, refresh-token family và việc ký access token JWT / guest device token. Không sở hữu hồ sơ user (module user), không gửi SMS thật và không tự verify token ở các route khác (JwtAuthGuard global làm việc đó).

## Facts

- tables owned: auth_identities, phone_otps, refresh_tokens
- touches tables of other modules: users (owner user; read 1, write 3)
- API: 9 endpoints, 6 public, 0 admin-only, 0 require Idempotency-Key, 7 with own throttle
- env vars read: NODE_ENV, AUTH_CROSS_ORIGIN_DEV, AUTH_COOKIE_SAME_SITE, AUTH_REFRESH_TTL_DAYS, JWT_SECRET, AUTH_PHONE_OTP_ENABLED, AUTH_GUEST_DEVICE_TOKEN_TTL_DAYS, AUTH_GUEST_DEVICE_TOKEN_SECRET, AUTH_OTP_REQUESTS_PER_HOUR, AUTH_OTP_TTL_SECONDS, AUTH_OTP_MAX_ATTEMPTS, AUTH_OTP_PEPPER, AUTH_FACEBOOK_APP_ID, AUTH_FACEBOOK_APP_SECRET, JWT_ACCESS_TTL_SECONDS
- error codes: 14 in AuthErrors
- tests: 7 unit spec files, 0 integration spec files
- history: 18 commits (1 fixes), last 2026-08-12
- features: auth-session

## Where to read next

- tables: ../db/auth.md
- API contract: ../api/auth.md
- why it talks to other services: ../service-map/auth.md and ../service-map/auth-techniques.md
- error codes: ../errors/autherrors.md
- flows involving it: [auth-guest-login-and-socket](../flows/auth-guest-login-and-socket.md), [auth-guest-upgrade](../flows/auth-guest-upgrade.md), [auth-otp-login](../flows/auth-otp-login.md), [auth-refresh-and-logout](../flows/auth-refresh-and-logout.md), [auth-search-by-phone](../flows/auth-search-by-phone.md), [auth-social-login](../flows/auth-social-login.md)

## Linked issues (heuristic, public subset)

- P018 [fixed] JWT access token trùng nhau khi cấp trong cùng giây (LRN-010)

See ../issues/index.md.
