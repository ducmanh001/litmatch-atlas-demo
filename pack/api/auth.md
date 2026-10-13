# REST API of auth

id: api:auth
module: module:auth
prefix: /api/v1; success body {data, meta?}; errors are DomainException codes (see ../errors/)
see: ../modules/auth.md

### POST /auth/guest

Đăng nhập khách bằng deviceId: find-or-create user guest + identity trong một transaction, trả access token, set cookie refresh và cấp guestDeviceToken. Public, throttle 5/phút/IP.

- handler: AuthController.guest (apps/core-api/src/modules/auth/auth.controller.ts:46); operationId: AuthController_guest
- auth: PUBLIC; throttle: { default: { limit: 5, ttl: minutes(1) } }
- body GuestLoginDto: deviceId*:string
- response 200 AuthTokensDto: accessToken:string, csrfToken:string, expiresIn:number, userId:string, isGuest:boolean, guestDeviceToken:string
- FE callers: web apps/web/src/shared/auth/login-form.tsx:250

### POST /auth/otp/request

Tạo OTP 6 số cho số điện thoại (rate limit theo số trong DB, vô hiệu mã cũ) (cách trả mã: ẩn khỏi bản công khai). Public, throttle 5/phút/IP; 403 nếu AUTH_PHONE_OTP_ENABLED tắt.

- handler: AuthController.requestOtp (apps/core-api/src/modules/auth/auth.controller.ts:64); operationId: AuthController_requestOtp
- auth: PUBLIC; throttle: { default: { limit: 5, ttl: minutes(1) } }
- body RequestOtpDto: phone*:string
- response 200 OtpRequestedDto: code:string, ttlSeconds:number
- FE callers: web apps/web/src/shared/auth/login-form.tsx:195; admin apps/admin/src/shared/auth/login-page.tsx:90

### POST /auth/otp/verify

Xác minh OTP (đếm lần thử và consume bằng UPDATE có điều kiện) rồi đăng nhập hoặc tạo user mới theo identity phone. Public, throttle 10/phút/IP.

- handler: AuthController.verifyOtp (apps/core-api/src/modules/auth/auth.controller.ts:75); operationId: AuthController_verifyOtp
- auth: PUBLIC; throttle: { default: { limit: 10, ttl: minutes(1) } }
- body VerifyOtpDto: phone*:string, code*:string
- response 200 AuthTokensDto: accessToken:string, csrfToken:string, expiresIn:number, userId:string, isGuest:boolean, guestDeviceToken:string
- FE callers: web apps/web/src/shared/auth/login-form.tsx:235; admin apps/admin/src/shared/auth/login-page.tsx:111

### POST /auth/social

Đăng nhập Google/Apple (ID token JWKS) hoặc Facebook (debug_token) sau khi server tự verify, rồi find-or-create user theo (provider, uid). Public, throttle 10/phút/IP.

- handler: AuthController.social (apps/core-api/src/modules/auth/auth.controller.ts:90); operationId: AuthController_social
- auth: PUBLIC; throttle: { default: { limit: 10, ttl: minutes(1) } }
- body SocialLoginDto: provider*:enum(google|apple|facebook), idToken*:string
- response 200 AuthTokensDto: accessToken:string, csrfToken:string, expiresIn:number, userId:string, isGuest:boolean, guestDeviceToken:string
- FE callers: web apps/web/src/shared/auth/login-form.tsx:279; admin apps/admin/src/shared/auth/login-page.tsx:134

### POST /auth/refresh

Xoay refresh token từ cookie httpOnly (rotation, phát hiện reuse thu hồi cả family), kiểm lại user chưa bị ban rồi cấp access token mới. Cần CsrfGuard (cookie csrf_token khớp header x-csrf-token).

- handler: AuthController.refresh (apps/core-api/src/modules/auth/auth.controller.ts:108); operationId: AuthController_refresh
- auth: PUBLIC (refresh cookie + CSRF); guards: @UseGuards(CsrfGuard)
- response 200 AuthTokensDto: accessToken:string, csrfToken:string, expiresIn:number, userId:string, isGuest:boolean, guestDeviceToken:string
- FE callers: none in this repo (mobile/third party or dead)

### POST /auth/logout

Thu hồi refresh token hiện tại (idempotent) và xoá cookie. Cần CsrfGuard; trả 204.

- handler: AuthController.logout (apps/core-api/src/modules/auth/auth.controller.ts:127); operationId: AuthController_logout
- auth: PUBLIC (refresh cookie + CSRF); guards: @UseGuards(CsrfGuard)
- response 204: 
- FE callers: none in this repo (mobile/third party or dead)

### POST /auth/upgrade/otp

Nâng cấp tài khoản guest đang đăng nhập bằng OTP điện thoại: gắn identity, xoá identity guest, đặt isGuest=false và thu hồi mọi refresh session trong một transaction có khoá hàng user. Cần JWT.

- handler: AuthUpgradeController.upgradeOtp (apps/core-api/src/modules/auth/controllers/auth-upgrade.controller.ts:39); operationId: AuthUpgradeController_upgradeOtp
- auth: Bearer JWT; throttle: { default: { limit: 10, ttl: minutes(1) } }
- body VerifyOtpDto: phone*:string, code*:string
- response 200 AuthTokensDto: accessToken:string, csrfToken:string, expiresIn:number, userId:string, isGuest:boolean, guestDeviceToken:string
- FE callers: none in this repo (mobile/third party or dead)

### POST /auth/upgrade/social

Nâng cấp guest bằng social ID token: cùng luồng linkIdentityAndUpgrade như OTP, 409 nếu identity đã thuộc tài khoản khác (không tự merge). Cần JWT.

- handler: AuthUpgradeController.upgradeSocial (apps/core-api/src/modules/auth/controllers/auth-upgrade.controller.ts:61); operationId: AuthUpgradeController_upgradeSocial
- auth: Bearer JWT; throttle: { default: { limit: 10, ttl: minutes(1) } }
- body SocialLoginDto: provider*:enum(google|apple|facebook), idToken*:string
- response 200 AuthTokensDto: accessToken:string, csrfToken:string, expiresIn:number, userId:string, isGuest:boolean, guestDeviceToken:string
- FE callers: none in this repo (mobile/third party or dead)

### GET /auth/search-by-phone

Tìm hồ sơ công khai theo số điện thoại chỉ khi chủ hồ sơ bật cho phép; trả null cả khi không tồn tại lẫn khi tắt privacy để không tạo oracle. Cần JWT, throttle 10/phút.

- handler: PhoneSearchController.search (apps/core-api/src/modules/auth/controllers/phone-search.controller.ts:20); operationId: PhoneSearchController_search
- auth: Bearer JWT; throttle: { default: { limit: 10, ttl: minutes(1) } }
- params: query:phone*:string
- response 200 PublicProfileDto | null: 
- FE callers: none in this repo (mobile/third party or dead)

