# Error codes AuthErrors

id: errors:AuthErrors
module: module:auth

| code | http status at throw sites | throw sites | meaning | declared |
|---|---|---|---|---|
| AUTH_PHONE_OTP_DISABLED | FORBIDDEN | 1 |  | apps/core-api/src/modules/auth/auth.errors.ts:3 |
| AUTH_OTP_INVALID | BAD_REQUEST | 2 |  | apps/core-api/src/modules/auth/auth.errors.ts:4 |
| AUTH_OTP_EXPIRED | BAD_REQUEST | 1 |  | apps/core-api/src/modules/auth/auth.errors.ts:5 |
| AUTH_OTP_TOO_MANY_ATTEMPTS | TOO_MANY_REQUESTS | 1 |  | apps/core-api/src/modules/auth/auth.errors.ts:6 |
| AUTH_OTP_REQUEST_RATE_LIMITED | TOO_MANY_REQUESTS | 1 |  | apps/core-api/src/modules/auth/auth.errors.ts:7 |
| AUTH_REFRESH_TOKEN_INVALID | UNAUTHORIZED | 2 |  | apps/core-api/src/modules/auth/auth.errors.ts:8 |
| AUTH_REFRESH_TOKEN_REUSED | UNAUTHORIZED | 1 |  | apps/core-api/src/modules/auth/auth.errors.ts:9 |
| AUTH_SOCIAL_TOKEN_INVALID | UNAUTHORIZED | 2 |  | apps/core-api/src/modules/auth/auth.errors.ts:10 |
| AUTH_SOCIAL_PROVIDER_NOT_SUPPORTED | BAD_REQUEST | 3 |  | apps/core-api/src/modules/auth/auth.errors.ts:11 |
| AUTH_IDENTITY_ALREADY_LINKED | CONFLICT | 3 |  | apps/core-api/src/modules/auth/auth.errors.ts:12 |
| AUTH_GUEST_DEVICE_TOKEN_INVALID | UNAUTHORIZED | 1 |  | apps/core-api/src/modules/auth/auth.errors.ts:13 |
| AUTH_GUEST_IDENTITY_RETIRED | UNAUTHORIZED | 1 |  | apps/core-api/src/modules/auth/auth.errors.ts:14 |
| AUTH_GUEST_UPGRADE_NOT_ALLOWED | CONFLICT | 1 |  | apps/core-api/src/modules/auth/auth.errors.ts:15 |
| AUTH_USER_BANNED | FORBIDDEN | 1 |  | apps/core-api/src/modules/auth/auth.errors.ts:16 |
