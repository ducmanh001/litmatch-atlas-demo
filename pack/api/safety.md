# REST API of safety

id: api:safety
module: module:safety
prefix: /api/v1; success body {data, meta?}; errors are DomainException codes (see ../errors/)
see: ../modules/safety.md

### POST /safety/reports

Report 1 user: chặn tự report, khoá dòng users của target rồi ghi Report + trừ trust score (1 report hiệu lực/cặp/SAFETY_REPORT_COOLDOWN_DAYS, trần SAFETY_TRUST_PENALTY_DAILY_CAP/ngày) trong CÙNG transaction; throttle 10 report/giờ/tài khoản.

- handler: SafetyController.createReport (apps/core-api/src/modules/safety/safety.controller.ts:33); operationId: SafetyController_createReport
- auth: Bearer JWT; throttle: { default: { limit: 10, ttl: minutes(60) } }
- body CreateReportDto: targetUserId*:string, reason*:enum(harassment|spam|underage|inappropriate_content|other), description:string
- response 201 ReportDto: id:string, targetUserId:string, reason:enum(harassment|spam|underage|inappropriate_content|other), createdAt:string(date-time)
- FE callers: web apps/web/src/features/friend-chat/api.ts:195

### POST /safety/blocks/{targetUserId}

Block target (idempotent): chặn tự block, xác nhận target tồn tại, ghi dòng action=blocked nếu dòng mới nhất chưa là blocked; trả 204.

- handler: SafetyController.block (apps/core-api/src/modules/safety/safety.controller.ts:55); operationId: SafetyController_block
- auth: Bearer JWT
- params: path:targetUserId*:string
- response 204: 
- FE callers: web apps/web/src/features/friend-chat/api.ts:181

### DELETE /safety/blocks/{targetUserId}

Unblock target (idempotent): chỉ ghi dòng action=unblocked khi dòng mới nhất đang là blocked; trả 204.

- handler: SafetyController.unblock (apps/core-api/src/modules/safety/safety.controller.ts:65); operationId: SafetyController_unblock
- auth: Bearer JWT
- params: path:targetUserId*:string
- response 204: 
- FE callers: none in this repo (mobile/third party or dead)

### GET /safety/blocks/{targetUserId}

Cho caller biết MÌNH có đang block target hay không (một chiều, không lộ việc bị block ngược).

- handler: SafetyController.blockStatus (apps/core-api/src/modules/safety/safety.controller.ts:75); operationId: SafetyController_blockStatus
- auth: Bearer JWT
- params: path:targetUserId*:string
- response 200 BlockStatusDto: blocked:boolean
- FE callers: none in this repo (mobile/third party or dead)

