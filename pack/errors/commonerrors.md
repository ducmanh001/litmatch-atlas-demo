# Error codes CommonErrors

id: errors:CommonErrors
module: module:libs/common-exceptions

| code | http status at throw sites | throw sites | meaning | declared |
|---|---|---|---|---|
| COMMON_VALIDATION_FAILED |  | 0 |  | libs/common-exceptions/src/lib/common.errors.ts:3 |
| COMMON_ROUTE_NOT_FOUND |  | 0 |  | libs/common-exceptions/src/lib/common.errors.ts:4 |
| COMMON_RATE_LIMITED |  | 0 |  | libs/common-exceptions/src/lib/common.errors.ts:5 |
| COMMON_UNAUTHORIZED |  | 0 |  | libs/common-exceptions/src/lib/common.errors.ts:6 |
| COMMON_FORBIDDEN | FORBIDDEN | 1 |  | libs/common-exceptions/src/lib/common.errors.ts:7 |
| COMMON_INTERNAL_ERROR |  | 0 |  | libs/common-exceptions/src/lib/common.errors.ts:8 |
| COMMON_IDEMPOTENCY_KEY_MISSING | BAD_REQUEST | 1 |  | libs/common-exceptions/src/lib/common.errors.ts:9 |
| COMMON_IDEMPOTENCY_KEY_INVALID | BAD_REQUEST | 1 |  | libs/common-exceptions/src/lib/common.errors.ts:10 |
| COMMON_CSRF_TOKEN_INVALID | UNAUTHORIZED | 1 |  | libs/common-exceptions/src/lib/common.errors.ts:11 |
