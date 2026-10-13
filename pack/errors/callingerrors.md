# Error codes CallingErrors

id: errors:CallingErrors
module: module:calling

| code | http status at throw sites | throw sites | meaning | declared |
|---|---|---|---|---|
| CALLING_SESSION_NOT_FOUND | NOT_FOUND | 3 | MatchSession/call không tồn tại HOẶC caller không phải thành viên — gộp, chống oracle (docs/10 § 10.1.D). | apps/core-api/src/modules/calling/calling.errors.ts:4 |
| CALLING_CALL_NOT_FOUND | NOT_FOUND | 3 |  | apps/core-api/src/modules/calling/calling.errors.ts:5 |
| CALLING_SESSION_NOT_CALLABLE | CONFLICT | 1 | Session không phải voice đã confirmed — chưa có quyền mở phòng. | apps/core-api/src/modules/calling/calling.errors.ts:7 |
| CALLING_CALL_ENDED | CONFLICT | 3 | Call đã kết thúc — không mint token/join lại được. | apps/core-api/src/modules/calling/calling.errors.ts:9 |
| CALLING_CALL_NOT_ENDED | CONFLICT | 1 | Like chỉ hợp lệ sau khi call đã terminal; tránh lộ danh tính trong lúc đang nói. | apps/core-api/src/modules/calling/calling.errors.ts:11 |
| CALLING_WEBHOOK_INVALID | UNAUTHORIZED | 2 | Webhook LiveKit không verify được chữ ký. | apps/core-api/src/modules/calling/calling.errors.ts:13 |
