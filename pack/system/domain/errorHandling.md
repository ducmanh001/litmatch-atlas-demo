# Domain rules, error handling, glossary, conventions: errorHandling

id: system:domain:errorHandling

Written by an AI agent from the code; every file:line quote was checked automatically (exists within ±3 lines) but NO independent verification pass has run yet. Treat claims as strong leads; open the cited file before editing.

### envelope

- success: Response thành công được bọc thành `{ data, meta? }` bởi ResponseEnvelopeInterceptor; controller đã trả object có khoá `data` (vd list kèm `meta.nextCursor`) thì được giữ nguyên.

- errorShape: Lỗi luôn có dạng `{ error: { code, message, traceId, details? } }` (ApiErrorBody), do GlobalExceptionFilter ghi bằng `res.status(status).json(payload)`. `traceId` là request-id (header `x-request-id` hoặc id sinh mới, cũng được trả lại ở response header).

- wiring: Bộ lọc và interceptor đăng ký toàn cục qua APP_FILTER và APP_INTERCEPTOR trong app.module.ts.

### evidence

### #1

- file: apps/core-api/src/common/interceptors/response-envelope.interceptor.ts
- line: 13
- quote: Bọc mọi response thành công vào envelope { data, meta? }

### #2

- file: apps/core-api/src/common/interceptors/response-envelope.interceptor.ts
- line: 31
- quote: if (value && typeof value === 'object' && 'data' in (value as object)) {

### #3

- file: libs/common-dtos/src/lib/api-response.ts
- line: 7
- quote: export interface ApiErrorBody {

### #4

- file: libs/common-dtos/src/lib/api-response.ts
- line: 12
- quote: details?: Record<string, unknown>;

### #5

- file: apps/core-api/src/common/filters/global-exception.filter.ts
- line: 86
- quote: res.status(status).json(payload);

### #6

- file: apps/core-api/src/app/app.module.ts
- line: 158
- quote: { provide: APP_FILTER, useClass: GlobalExceptionFilter },

### #7

- file: apps/core-api/src/app/app.module.ts
- line: 157
- quote: { provide: APP_INTERCEPTOR, useClass: ResponseEnvelopeInterceptor },

### #8

- file: libs/logger/src/lib/build-logger-options.ts
- line: 42
- quote: res.setHeader('x-request-id', id);

### #9

- file: docs/05-coding-standards.md
- line: 72
- quote: Không endpoint nào trả object trần một kiểu riêng

### domainExceptionToHttp

- rule: DomainException giữ nguyên code, message, details và dùng httpStatus của nó (mặc định 400; ném Error thường nếu ngoài 400-499). HttpException của framework map theo status: 401 COMMON_UNAUTHORIZED, 403 COMMON_FORBIDDEN, 404 COMMON_ROUTE_NOT_FOUND, 429 COMMON_RATE_LIMITED, 400 COMMON_VALIDATION_FAILED, còn lại (kể cả 5xx) COMMON_INTERNAL_ERROR.

- gotcha: HttpException 5xx tự đặt (vd `new ServiceUnavailableException(code, message)`) mất mã riêng: theo mã, client nhận status 5xx với `error.code = COMMON_INTERNAL_ERROR` và `error.message` là chính chuỗi mã (locale vi) hoặc câu chung (locale en). Nhánh HttpException cũng không log và không gửi Sentry.

### evidence

### #1

- file: libs/common-exceptions/src/lib/domain-exception.ts
- line: 6
- quote: DomainException không bao giờ map ra 500

### #2

- file: libs/common-exceptions/src/lib/domain-exception.ts
- line: 23
- quote: DomainException chỉ được dùng status 4xx

### #3

- file: apps/core-api/src/common/filters/global-exception.filter.ts
- line: 42
- quote: if (exception instanceof DomainException) {

### #4

- file: apps/core-api/src/common/filters/global-exception.filter.ts
- line: 55
- quote: code = this.mapHttpStatusToCode(status);

### #5

- file: apps/core-api/src/common/filters/global-exception.filter.ts
- line: 97
- quote: case HttpStatus.TOO_MANY_REQUESTS:

### #6

- file: apps/core-api/src/common/filters/global-exception.filter.ts
- line: 102
- quote: return CommonErrors.INTERNAL_ERROR;

### #7

- file: docs/05-coding-standards.md
- line: 81
- quote: `DomainException` không bao giờ map ra 500

### #8

- file: apps/core-api/src/modules/party-room/party-room.service.ts
- line: 219
- quote: Lỗi hạ tầng (5xx) — không phải lỗi domain của client nên không dùng DomainException (4xx-only)

### #9

- file: apps/core-api/src/modules/party-room/party-room.service.ts
- line: 221
- quote: PartyRoomErrors.MEDIA_ROOM_CREATE_FAILED,

### #10

- file: apps/core-api/src/modules/party-room/party-room.integration.spec.ts
- line: 317
- quote: message: 'PARTY_MEDIA_ROOM_CREATE_FAILED',

### #11

- file: node_modules/@nestjs/common/exceptions/http.exception.js
- line: 91
- quote: static createBody(arg0, arg1, statusCode) {

### validationErrors

- how: ValidationPipe toàn cục (whitelist, forbidNonWhitelisted, transform) ném BadRequestException có message là mảng; filter đổi thành 400 COMMON_VALIDATION_FAILED, message cố định 'Dữ liệu đầu vào không hợp lệ' và details.violations là mảng thông điệp gốc của class-validator (không localize).

- stringMessage: 400 có message dạng chuỗi vẫn ra COMMON_VALIDATION_FAILED nhưng giữ message gốc.

- businessValidation: Lỗi đúng hình thức nhưng sai nghiệp vụ dùng DomainException 422 (vd USER_SEEKING_AGE_RANGE_INVALID).

### evidence

### #1

- file: apps/core-api/src/main.ts
- line: 82
- quote: forbidNonWhitelisted: true,

### #2

- file: apps/core-api/src/main.ts
- line: 83
- quote: transform: true,

### #3

- file: apps/core-api/src/common/filters/global-exception.filter.ts
- line: 59
- quote: code = CommonErrors.VALIDATION_FAILED;

### #4

- file: apps/core-api/src/common/filters/global-exception.filter.ts
- line: 61
- quote: details = { violations: raw };

### #5

- file: apps/core-api/src/common/filters/global-exception.filter.ts
- line: 60
- quote: message = 'Dữ liệu đầu vào không hợp lệ';

### #6

- file: apps/core-api/src/modules/user/user.service.ts
- line: 287
- quote: HttpStatus.UNPROCESSABLE_ENTITY,

### databaseErrors

- how: Không có ánh xạ chung cho lỗi Postgres (không có xử lý QueryFailedError): lỗi driver không bị bắt rơi vào nhánh 500 COMMON_INTERNAL_ERROR.

- uniqueViolation: Unique violation (23505) được từng service bắt cục bộ bằng isUniqueViolation/violatedConstraint rồi đọc lại hoặc ném DomainException (vd uq_match_tickets_active_user thành 409 MATCHING_TICKET_ALREADY_QUEUED). Ledger dùng INSERT ... ON CONFLICT DO NOTHING để không làm abort transaction của caller.

- deadlockAndSerialization: Theo mã (grep 40P01, 40001, SERIALIZABLE trong apps/core-api/src và libs, không tính file spec, không có kết quả): không bắt hay retry deadlock/serialization failure và không dùng isolation SERIALIZABLE. Giảm deadlock bằng thứ tự khoá cố định (ORDER BY id/keyHash) cùng khoá `pessimistic_write`; nếu vẫn xảy ra thì ra 500.

- rule: Insert-rồi-đọc-lại khi unique violation không được nằm trong transaction explicit nếu bước đọc chỉ chạy khi insert lỗi, vì Postgres abort cả transaction (docs/06).

### evidence

### #1

- file: apps/core-api/src/database/postgres-errors.ts
- line: 7
- quote: export const PG_UNIQUE_VIOLATION = '23505';

### #2

- file: apps/core-api/src/database/postgres-errors.ts
- line: 16
- quote: export function violatedConstraint(err: unknown, constraint: string): boolean {

### #3

- file: apps/core-api/src/modules/matching/matching.service.ts
- line: 188
- quote: if (violatedConstraint(err, UQ_ACTIVE_USER)) {

### #4

- file: apps/core-api/src/modules/matching/matching.constants.ts
- line: 7
- quote: export const UQ_ACTIVE_USER = 'uq_match_tickets_active_user';

### #5

- file: apps/core-api/src/common/filters/global-exception.filter.ts
- line: 24
- quote: Còn lại → 500 COMMON_INTERNAL_ERROR, log đầy đủ, KHÔNG lộ chi tiết nội bộ ra ngoài

### #6

- file: apps/core-api/src/modules/matching/jobs/matcher-worker.service.ts
- line: 236
- quote: thứ tự lock cố định — tránh deadlock với các writer khác

### #7

- file: apps/core-api/src/modules/matching/services/guest-match-quota.service.ts
- line: 83
- quote: Insert-before-lock + thứ tự key ổn định tránh lost update/deadlock khi nhiều key giao nhau.

### #8

- file: docs/06-domain-rules.md
- line: 122
- quote: Insert-rồi-đọc-lại khi unique violation PHẢI KHÔNG nằm trong 1 transaction Postgres explicit

### #9

- file: apps/core-api/src/modules/economy/services/ledger.service.ts
- line: 117
- quote: ON CONFLICT DO NOTHING avoids aborting the caller-owned transaction on a

### unexpectedErrors

- status: 500 COMMON_INTERNAL_ERROR cho mọi exception không phải DomainException/HttpException.

- response: Message cố định 'Lỗi hệ thống, thử lại sau' (locale en: 'Something went wrong. Please try again later.'); không trả stack hay message gốc.

- whatIsLoggedAndMasked: Filter log mức error kèm traceId và stack rồi gọi captureSentryException; log đi qua pino REDACT_PATHS (thay bằng [REDACTED]) và Sentry bỏ cookies/data/headers (sendDefaultPii=false).

- redactedFields: REDACT_PATHS gồm authorization, cookie, set-cookie, body.password/otp/code/token/refreshToken/accessToken/idToken/receipt/phone/lat/lon và các biến thể *.field.

- redactLimit: REDACT_PATHS che theo đường dẫn field, không quét nội dung chuỗi stack.

### evidence

### #1

- file: apps/core-api/src/common/filters/global-exception.filter.ts
- line: 67
- quote: message = 'Lỗi hệ thống, thử lại sau';

### #2

- file: apps/core-api/src/common/filters/global-exception.filter.ts
- line: 71
- quote: err: exception instanceof Error ? exception.stack : String(exception),

### #3

- file: apps/core-api/src/common/filters/global-exception.filter.ts
- line: 75
- quote: captureSentryException(exception, traceId);

### #4

- file: libs/observability/src/lib/sentry.ts
- line: 30
- quote: sendDefaultPii: false,

### #5

- file: libs/observability/src/lib/sentry.ts
- line: 35
- quote: delete event.request.data;

### #6

- file: libs/logger/src/lib/redact.ts
- line: 11
- quote: 'req.body.otp',

### #7

- file: libs/logger/src/lib/build-logger-options.ts
- line: 28
- quote: censor: '[REDACTED]',

### #8

- file: apps/core-api/src/common/i18n/error-message.localizer.ts
- line: 12
- quote: COMMON_INTERNAL_ERROR: 'Something went wrong. Please try again later.',

### #9

- file: docs/05-coding-standards.md
- line: 102
- quote: Cấm log PII/secret

### rateLimit

- status: 429; filter map sang COMMON_RATE_LIMITED (message localize). ThrottlerGuard là APP_GUARD toàn cục.

- retryHeaders: Repo không tự đặt header giới hạn: Retry-After do ThrottlerGuard của @nestjs/throttler đặt khi chặn, thư viện dùng tiền tố X-RateLimit cho header hạn mức. Body lỗi không có trường retry.

- quotaRateLimit: 429 do quota nghiệp vụ (MATCHING_DAILY_QUOTA_EXCEEDED, MATCHING_GUEST_DAILY_QUOTA_EXCEEDED) là DomainException ném từ service, không qua ThrottlerGuard; MATCHING_DAILY_QUOTA_EXCEEDED kèm details { limit, quotaDate, matchType }.

- config: Mặc định toàn cục THROTTLE_LIMIT=100 trên THROTTLE_TTL_SECONDS=60; THROTTLE_STORAGE=memory (mặc định) hoặc redis để dùng chung giữa các pod. `@Throttle` theo route khai tại 14 controller (auth, economy, gift, matching, safety...).

- caveat: Bộ đếm khoá theo req.ip: sau ingress/LB mà HTTP_TRUST_PROXY_HOPS=0 thì mọi người dùng chung một bucket (app.module.ts cảnh báo khi dùng redis).

### evidence

### #1

- file: apps/core-api/src/common/filters/global-exception.filter.ts
- line: 98
- quote: return CommonErrors.RATE_LIMITED;

### #2

- file: apps/core-api/src/app/app.module.ts
- line: 154
- quote: { provide: APP_GUARD, useClass: ThrottlerGuard },

### #3

- file: apps/core-api/src/config/env.validation.ts
- line: 754
- quote: THROTTLE_LIMIT: Joi.number().integer().min(1).default(100),

### #4

- file: apps/core-api/src/config/env.validation.ts
- line: 757
- quote: THROTTLE_STORAGE: Joi.string().valid('memory', 'redis').default('memory'),

### #5

- file: apps/core-api/src/app/app.module.ts
- line: 110
- quote: // Nhiều replica: hạn mức phải dùng chung qua Redis, không đếm riêng từng pod.

### #6

- file: apps/core-api/src/app/app.module.ts
- line: 95
- quote: Bộ đếm dùng chung nhưng khoá theo req.ip: sau ingress/LB mà không tin proxy thì req.ip là IP

### #7

- file: apps/core-api/src/modules/auth/auth.controller.ts
- line: 48
- quote: @Throttle({ default: { limit: 5, ttl: minutes(1) } })

### #8

- file: apps/core-api/src/modules/matching/services/guest-match-quota.service.ts
- line: 115
- quote: HttpStatus.TOO_MANY_REQUESTS,

### #9

- file: node_modules/@nestjs/throttler/dist/throttler.guard.js
- line: 28
- quote: this.headerPrefix = 'X-RateLimit';

### #10

- file: node_modules/@nestjs/throttler/dist/throttler.guard.js
- line: 121
- quote: res.header(`Retry-After${getThrottlerSuffix(throttler.name)}`, timeToBlockExpire);

### #11

- file: docs/05-coding-standards.md
- line: 112
- quote: Limit per-route khai ngay tại `@Throttle`

### idempotencyErrors

- header: Header `Idempotency-Key` (tối đa 128 ký tự) được rút bằng decorator `@IdempotencyKey()`.

- missing: Thiếu hoặc rỗng: 400 COMMON_IDEMPOTENCY_KEY_MISSING.

- invalid: Dài hơn 128 ký tự: 400 COMMON_IDEMPOTENCY_KEY_INVALID.

- conflict: Cùng key nhưng request_hash khác: 409 với mã `<DOMAIN>_..._IDEMPOTENCY_CONFLICT` riêng của từng module (vd ECONOMY_TRANSACTION_IDEMPOTENCY_CONFLICT).

- replay: Cùng key và cùng nội dung: trả lại giao dịch đã có (`replayed: true`) thay vì tạo mới; đua giữa hai request cùng key được chốt bởi unique constraint `uq_transactions_idempotency_key`.

- externalId: Giao dịch có ID ngoài bất biến (IAP) dùng chính ID đó làm key, server tự derive, không cần header (docs/05 §5.10).

### evidence

### #1

- file: apps/core-api/src/common/decorators/idempotency-key.decorator.ts
- line: 11
- quote: export const IDEMPOTENCY_KEY_HEADER = 'idempotency-key';

### #2

- file: apps/core-api/src/common/decorators/idempotency-key.decorator.ts
- line: 23
- quote: if (typeof key !== 'string' || key.trim() === '') {

### #3

- file: apps/core-api/src/common/decorators/idempotency-key.decorator.ts
- line: 30
- quote: if (key.length > MAX_KEY_LENGTH) {

### #4

- file: apps/core-api/src/modules/economy/services/ledger.service.ts
- line: 367
- quote: if (existing.requestHash !== requestHash) {

### #5

- file: apps/core-api/src/modules/economy/services/ledger.service.ts
- line: 113
- quote: replayed: true,

### #6

- file: apps/core-api/src/database/migrations/1752000000000-economy-ledger.ts
- line: 43
- quote: CONSTRAINT uq_transactions_idempotency_key UNIQUE (idempotency_key)

### #7

- file: docs/05-coding-standards.md
- line: 142
- quote: Cùng key nhưng payload khác

### #8

- file: docs/05-coding-standards.md
- line: 140
- quote: dùng chính ID đó làm key, server tự derive, KHÔNG cần header

### clientSideHandling

- parse: libs/api-client biến mọi non-2xx thành `ApiError { status, code, traceId, details }`; body sai envelope cho code CLIENT_MALFORMED_ERROR, mất mạng cho CLIENT_NETWORK_ERROR (status 0). UI được hướng dẫn switch theo `code`, không parse message.

- refreshOn401: Khi request thường nhận 401 và token store đang authenticated: gọi `/api/v1/auth/refresh` đúng một lần (single-flight trong tab, Web Locks `navigator.locks` giữa các tab) rồi retry bằng bản clone của request; vẫn 401 hoặc refresh lỗi thì xoá session và gọi `onSessionExpired`.

- csrf: Header `x-csrf-token` chỉ được gắn vào lời gọi refresh (kèm cookie httpOnly, credentials include) và không có logic retry riêng cho COMMON_CSRF_TOKEN_INVALID. CsrfGuard (route refresh và logout) trả 401 cho lỗi CSRF, nên CSRF hỏng ở refresh dẫn tới kết thúc session.

- headers: Mọi request gắn `accept-language` theo `getLocale()` (vi|en) để server localize message; request tới /matching/tickets kèm `X-Guest-Device-Token` khi có.

- webCodes: Web chỉ tham chiếu 3 trong 182 mã lỗi: CALLING_CALL_ENDED, CALLING_SESSION_NOT_CALLABLE, CALLING_SESSION_NOT_FOUND (voice-call-room.tsx); phần còn lại hiển thị `error.message` hoặc chuỗi i18n dự phòng (theo atlas fe/errors.md).

### evidence

### #1

- file: libs/api-client/src/lib/client.ts
- line: 72
- quote: 401 → refresh rotation đúng MỘT lần (single-flight giữa các request song song) rồi retry;

### #2

- file: libs/api-client/src/lib/client.ts
- line: 190
- quote: const isRefreshCall = new URL(request.url).pathname === AUTH_REFRESH_PATH;

### #3

- file: libs/api-client/src/lib/client.ts
- line: 118
- quote: return navigator.locks.request(refreshLockName, work);

### #4

- file: libs/api-client/src/lib/client.ts
- line: 133
- quote: ...(csrfToken !== null ? { [CSRF_HEADER_NAME]: csrfToken } : {}),

### #5

- file: libs/api-client/src/lib/client.ts
- line: 214
- quote: if (retried.status === 401) {

### #6

- file: libs/api-client/src/lib/client.ts
- line: 172
- quote: request.headers.set('accept-language', getLocale?.() ?? 'vi');

### #7

- file: libs/api-client/src/lib/client.ts
- line: 182
- quote: request.headers.set('X-Guest-Device-Token', guestDeviceToken);

### #8

- file: libs/api-client/src/lib/client.ts
- line: 101
- quote: throw ApiError.network(cause);

### #9

- file: libs/api-client/src/lib/api-error.ts
- line: 10
- quote: export const CLIENT_MALFORMED_ERROR = 'CLIENT_MALFORMED_ERROR';

### #10

- file: libs/api-client/src/lib/api-error.ts
- line: 13
- quote: switch theo `code`, không parse

### #11

- file: apps/core-api/src/common/guards/csrf.guard.ts
- line: 26
- quote: CommonErrors.CSRF_TOKEN_INVALID,

### #12

- file: apps/core-api/src/common/guards/csrf.guard.ts
- line: 28
- quote: HttpStatus.UNAUTHORIZED,

### #13

- file: apps/core-api/src/modules/auth/auth.controller.ts
- line: 108
- quote: @Post('refresh')

### #14

- file: apps/core-api/src/modules/auth/auth.controller.ts
- line: 127
- quote: @Post('logout')

### #15

- file: apps/web/src/features/voice-match/components/voice-call-room.tsx
- line: 91
- quote: (error.code === 'CALLING_CALL_ENDED' ||

### conventionsForNewCode

### Khai mã lỗi trong `<module>.errors.ts` dạng DOMAIN_SUBJECT_REASON; không sinh mã rải rác trong serv…

- evidence (evidence): docs/05-coding-standards.md:80, docs/16-module-blueprint.md:25

### Lỗi client/nghiệp vụ sửa được: `throw new DomainException(code, message, HttpStatus.X, details?)` v…

- evidence (evidence): libs/common-exceptions/src/lib/domain-exception.ts:7, docs/05-coding-standards.md:83

### Bug hoặc bất biến nội bộ bị vi phạm: ném `Error` thường (thành 500), không bọc DomainException.

- evidence (evidence): docs/05-coding-standards.md:82, apps/core-api/src/modules/economy/services/ledger.service.ts:324

### Lỗi hạ tầng 5xx hiện không dùng DomainException; HttpException 5xx luôn ra COMMON_INTERNAL_ERROR (m…

- evidence (evidence): apps/core-api/src/modules/party-room/party-room.service.ts:219, apps/core-api/src/common/filters/global-exception.filter.ts:55

### Service không đọc request/locale hay tự dịch message; bản dịch en nằm ở EN_ERROR_MESSAGES, mã chưa …

- evidence (evidence): docs/05-coding-standards.md:87, apps/core-api/src/common/i18n/error-message.localizer.ts:6, apps/core-api/src/common/i18n/error-message.localizer.ts:59

### Endpoint có tác dụng phụ không được lặp: dùng `@IdempotencyKey()` cùng `@ApiIdempotencyKeyHeader()`…

- evidence (evidence): apps/core-api/src/common/decorators/idempotency-key.decorator.ts:41, apps/core-api/src/common/decorators/idempotency-key.decorator.ts:50

### Bắt unique violation bằng `isUniqueViolation`/`violatedConstraint` ở database/postgres-errors.ts; k…

- evidence (evidence): apps/core-api/src/database/postgres-errors.ts:5

### Không log PII/secret: thêm field nhạy cảm vào REDACT_PATHS của libs/logger thay vì tự che từng chỗ.

- evidence (evidence): libs/logger/src/lib/redact.ts:2, docs/05-coding-standards.md:102

### Rate limit theo route khai bằng `@Throttle` tại controller (không đưa vào env); chỉ default toàn cụ…

- evidence (evidence): docs/05-coding-standards.md:112, apps/core-api/src/modules/auth/auth.controller.ts:48
