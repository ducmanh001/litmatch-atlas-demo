# Cache and state: Redis, locks, idempotency, client state: serverSessionState

id: system:state:serverSessionState

Written by an AI agent from the code; every file:line quote was checked automatically (exists within ±3 lines) but NO independent verification pass has run yet. Treat claims as strong leads; open the cited file before editing.

### accessToken

- format: JWT ký bằng JWT_SECRET qua JwtService.signAsync; stateless, không lưu phía server; JwtAuthGuard global chỉ verify chữ ký và hạn.

- ttlEnvAndDefault: JWT_ACCESS_TTL_SECONDS, mặc định 900 s (tối thiểu 60)

- claims: sub (userId), isGuest, role, jti (UUID mới mỗi token)

- revocation: Không có denylist theo jti trong guard; token hết hạn theo TTL, banned kiểm lại ở refresh/login.

- evidence: apps/core-api/src/modules/auth/services/token.service.ts:45

### moreEvidence

### #1

- file: apps/core-api/src/config/env.validation.ts
- line: 290
- quote: JWT_ACCESS_TTL_SECONDS: Joi.number().integer().min(60).default(900),

### #2

- file: apps/core-api/src/common/guards/jwt-auth.guard.ts
- line: 21
- quote: Access token là stateless JWT TTL ngắn

### refreshToken

- storage: Bảng refresh_tokens: token_hash (SHA-256 của chuỗi ngẫu nhiên 48 byte base64url), family_id, expires_at, rotated_at, revoked_at; plaintext chỉ nằm trong cookie.

- rotation: Mỗi /auth/refresh phát refresh token mới cùng family; transaction khoá dòng token (FOR UPDATE), đánh dấu rotated_at bằng UPDATE có điều kiện rồi insert token thay thế. TTL AUTH_REFRESH_TTL_DAYS (mặc định 30).

- reuseDetection: Token đã rotated_at hoặc UPDATE thua race thì revoke toàn bộ family (revoked_at) và trả 401 REFRESH_TOKEN_REUSED.

- cookieAttributes: refresh_token: httpOnly, path /api/v1/auth, maxAge = AUTH_REFRESH_TTL_DAYS ngày; secure khi production hoặc sameSite=none; sameSite production theo AUTH_COOKIE_SAME_SITE (strict mặc định), dev mặc định strict.

- evidence: apps/core-api/src/modules/auth/repositories/typeorm-refresh-session.adapter.ts:47

### moreEvidence

### #1

- file: apps/core-api/src/modules/auth/auth.cookies.ts
- line: 33
- quote: httpOnly: true,

### #2

- file: apps/core-api/src/modules/auth/auth.cookies.ts
- line: 35
- quote: secure: isProduction || sameSite === 'none',

### #3

- file: apps/core-api/src/modules/auth/auth.cookies.ts
- line: 37
- quote: path: REFRESH_TOKEN_COOKIE_PATH,

### #4

- file: apps/core-api/src/modules/auth/auth.constants.ts
- line: 19
- quote: export const REFRESH_TOKEN_COOKIE_PATH = '/api/v1/auth';

### #5

- file: apps/core-api/src/modules/auth/auth.constants.ts
- line: 15
- quote: export const REFRESH_TOKEN_BYTES = 48;

### #6

- file: apps/core-api/src/config/env.validation.ts
- line: 305
- quote: AUTH_COOKIE_SAME_SITE: Joi.string().valid('strict', 'none').default('strict'),

### guestDeviceToken

- format: JWT HS256 ký bằng AUTH_GUEST_DEVICE_TOKEN_SECRET, audience litmatch-matching, issuer litmatch-core-api; không lưu phía server.

- claims: sub (userId), deviceKey (HMAC-SHA256 của deviceId, 64 ký tự hex), purpose='guest-device'

- ttlEnvAndDefault: AUTH_GUEST_DEVICE_TOKEN_TTL_DAYS, mặc định 30 ngày

- binding: verifyForUser yêu cầu sub khớp userId, purpose đúng và deviceKey hex; deviceId thô không vào storage quota.

- clientHandling: Client giữ memory-only, gửi header X-Guest-Device-Token cho các request /matching/tickets.

- evidence: apps/core-api/src/modules/auth/services/guest-device-token.service.ts:36

### moreEvidence

### #1

- file: apps/core-api/src/modules/auth/services/guest-device-token.service.ts
- line: 32
- quote: purpose: 'guest-device',

### #2

- file: apps/core-api/src/modules/auth/services/guest-device-token.service.ts
- line: 72
- quote: return createHmac('sha256', this.secret())

### #3

- file: apps/core-api/src/config/env.validation.ts
- line: 298
- quote: AUTH_GUEST_DEVICE_TOKEN_TTL_DAYS: Joi.number().integer().min(1).default(30),

### #4

- file: libs/api-client/src/lib/client.ts
- line: 182
- quote: request.headers.set('X-Guest-Device-Token', guestDeviceToken);

### csrf

- pattern: Double-submit: cookie csrf_token (httpOnly, cùng path/TTL với refresh) phải khớp header x-csrf-token, so sánh timingSafeEqual.

- token: 32 byte ngẫu nhiên base64url, trả qua JSON body lúc login/refresh; frontend persist ở localStorage để lần refresh đầu sau reload còn header hợp lệ.

- scope: Chỉ áp thủ công bằng CsrfGuard trên /auth/refresh và /auth/logout, không đăng ký global (các API khác dùng Authorization Bearer).

- evidence: apps/core-api/src/common/csrf/csrf-token.ts:32

### moreEvidence

### #1

- file: apps/core-api/src/common/csrf/csrf.constants.ts
- line: 8
- quote: export const CSRF_TOKEN_BYTES = 32;

### #2

- file: apps/core-api/src/common/guards/csrf.guard.ts
- line: 12
- quote: KHÔNG đăng ký global vì phần lớn API xác thực

### #3

- file: apps/core-api/src/modules/auth/auth.cookies.ts
- line: 60
- quote: res.cookie(CSRF_COOKIE_NAME, input.csrfToken, options);

### otp

- storage: Bảng phone_otps: code_hash = HMAC-SHA256(AUTH_OTP_PEPPER, phone:code), expires_at, attempt_count, consumed_at; không dùng Redis.

- ttlEnvAndDefault: AUTH_OTP_TTL_SECONDS, mặc định 300 s; mã 6 chữ số

- limits: Tối đa AUTH_OTP_MAX_ATTEMPTS (mặc định 5) lần thử bằng UPDATE có điều kiện; AUTH_OTP_REQUESTS_PER_HOUR (mặc định 5) yêu cầu mỗi số điện thoại mỗi giờ đếm trong bảng; thêm @Throttle 5 lần/phút theo IP.

- singleUse: Yêu cầu mã mới vô hiệu mã cũ chưa dùng; consume bằng UPDATE kèm consumed_at IS NULL nên một mã chỉ dùng một lần.

- deliveryNote: Không gửi SMS: mã được trả trong response requestOtp ({ code, ttlSeconds }) để client hiển thị và tự điền.

- evidence: apps/core-api/src/modules/auth/services/otp.service.ts:64

### moreEvidence

### #1

- file: apps/core-api/src/modules/auth/services/otp.service.ts
- line: 135
- quote: return createHmac('sha256', pepper)

### #2

- file: apps/core-api/src/config/env.validation.ts
- line: 294
- quote: AUTH_OTP_MAX_ATTEMPTS: Joi.number().integer().min(1).default(5),

### #3

- file: apps/core-api/src/config/env.validation.ts
- line: 295
- quote: AUTH_OTP_REQUESTS_PER_HOUR: Joi.number().integer().min(1).default(5),

### #4

- file: apps/core-api/src/config/env.validation.ts
- line: 293
- quote: AUTH_OTP_TTL_SECONDS: Joi.number().integer().min(60).default(300),

### #5

- file: apps/core-api/src/modules/auth/auth.controller.ts
- line: 66
- quote: limit theo số điện thoại nằm trong OtpService

### jwtSecretRotation

- current: JWT_SECRET (tối thiểu 32 ký tự); token luôn được ký bằng khoá hiện tại.

- previous: JWT_SECRET_PREVIOUS: chỉ thử khi chữ ký sai với khoá hiện tại; token hết hạn, sai định dạng hoặc sai claim không được thử lại.

- duplication: Logic xác thực xoay khoá có bản sao ở apps/signaling-gateway/src/app/jwt-rotation.ts (hai app deploy độc lập); sửa một nơi phải sửa nơi kia.

- evidence: apps/core-api/src/common/guards/jwt-rotation.ts:4

### moreEvidence

### #1

- file: apps/core-api/src/common/guards/jwt-rotation.ts
- line: 8
- quote: Bản sao cùng logic nằm ở

### #2

- file: apps/core-api/src/config/env.validation.ts
- line: 282
- quote: JWT_SECRET: Joi.string().min(32).required(),
