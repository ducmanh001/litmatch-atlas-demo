# Error codes SupportErrors

id: errors:SupportErrors
module: module:support

| code | http status at throw sites | throw sites | meaning | declared |
|---|---|---|---|---|
| SUPPORT_CURSOR_INVALID | BAD_REQUEST | 1 |  | apps/core-api/src/modules/support/support.errors.ts:2 |
| SUPPORT_TICKET_NOT_FOUND | NOT_FOUND | 1 |  | apps/core-api/src/modules/support/support.errors.ts:3 |
| SUPPORT_IDEMPOTENCY_CONFLICT | CONFLICT | 1 |  | apps/core-api/src/modules/support/support.errors.ts:4 |
| SUPPORT_INVALID_TRANSITION | CONFLICT | 1 |  | apps/core-api/src/modules/support/support.errors.ts:5 |
