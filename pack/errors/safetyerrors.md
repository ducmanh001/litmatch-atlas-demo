# Error codes SafetyErrors

id: errors:SafetyErrors
module: module:safety

| code | http status at throw sites | throw sites | meaning | declared |
|---|---|---|---|---|
| SAFETY_SELF_REPORT_FORBIDDEN | BAD_REQUEST | 1 |  | apps/core-api/src/modules/safety/safety.errors.ts:3 |
| SAFETY_SELF_BLOCK_FORBIDDEN | BAD_REQUEST | 1 |  | apps/core-api/src/modules/safety/safety.errors.ts:4 |
| SAFETY_REPORT_NOT_FOUND | NOT_FOUND | 1 |  | apps/core-api/src/modules/safety/safety.errors.ts:5 |
