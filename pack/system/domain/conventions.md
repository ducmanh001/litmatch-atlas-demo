# Domain rules, error handling, glossary, conventions: conventions

id: system:domain:conventions

Written by an AI agent from the code; every file:line quote was checked automatically (exists within ±3 lines) but NO independent verification pass has run yet. Treat claims as strong leads; open the cited file before editing.

### #1

- area: DB: tên bảng và cột
- convention: Bảng snake_case số nhiều, cột snake_case; TypeORM ánh xạ camelCase sang snake_case bằng SnakeNamingStrategy (cấu hình ở app.module.ts và data-source.ts).
- evidence (evidence): docs/05-coding-standards.md:94, apps/core-api/src/app/app.module.ts:82, apps/core-api/src/database/data-source.ts:24

### #2

- area: DB: constraint và index
- convention: Index `idx_<table>_<query-shape>`, unique `uq_<table>_<invariant>`, check `chk_<table>_<rule>`, FK `fk_<child>_<parent>`; tên constraint dùng trong code khai thành hằng (vd UQ_ACTIVE_USER).
- evidence (evidence): docs/17-naming-conventions.md:137, apps/core-api/src/modules/matching/matching.constants.ts:7

### #3

- area: Kiểu chữ theo bề mặt
- convention: File/folder kebab-case, biến và property camelCase, class/enum PascalCase, hằng/env/mã lỗi UPPER_SNAKE, JSON field camelCase; giá trị serialized của enum theo contract (`iap_purchase`, `queued`).
- evidence (evidence): docs/17-naming-conventions.md:48, docs/17-naming-conventions.md:62

### #4

- area: Hậu tố vai trò
- convention: `*Dto` cho transport HTTP, `*Input` cho input service/port, `*Result`/`*View` cho output, `*Event` cho event, `*Config` cho cấu hình đã validate; class NestJS đặt tên `<Domain><Role>`.
- evidence (evidence): docs/17-naming-conventions.md:117, apps/core-api/src/modules/auth/dto/auth-request.dtos.ts:18

### #5

- area: Bố cục module
- convention: Mỗi module trong apps/core-api/src/modules có `<module>.module.ts`, facade service, controller, `<module>.errors.ts`, `index.ts` và `<module>.constants.ts` tuỳ chọn; dto/ entities/ services/ ports/ clients/ jobs/ webhooks/ redis/ events/ chỉ tạo khi có thành phần thật.
- evidence (evidence): docs/16-module-blueprint.md:25, docs/16-module-blueprint.md:54

### #6

- area: Public API (index.ts)
- convention: `index.ts` là cổng public duy nhất; module khác chỉ import qua `modules/<name>`; không export entity, repository, job, client cho module mới.
- evidence (evidence): docs/16-module-blueprint.md:128, docs/16-module-blueprint.md:137

### #7

- area: Ports và adapters
- convention: `ports/` giữ abstract class/interface kèm token, implementation chọn qua DI/config; `clients/` chỉ gọi API bên thứ ba. Vd MatchInteractionPolicy do SafetyService thoả mãn bằng structural typing.
- evidence (evidence): docs/16-module-blueprint.md:68, apps/core-api/src/modules/matching/ports/interaction-policy.ts:14, apps/core-api/src/modules/matching/matching.module.ts:69

### #8

- area: Layering
- convention: Controller gọi Service, Service gọi Repository; controller, webhook và `*.handler.ts` không inject Repository/DataSource.
- evidence (evidence): docs/05-coding-standards.md:20, docs/16-module-blueprint.md:94

### #9

- area: Response envelope
- convention: Thành công `{ data, meta? }`, lỗi `{ error: { code, message, traceId, details? } }`; không endpoint nào trả object trần.
- evidence (evidence): apps/core-api/src/common/interceptors/response-envelope.interceptor.ts:13, libs/common-dtos/src/lib/api-response.ts:7

### #10

- area: Phân trang cursor
- convention: List lớn dần dùng cursor: query `limit` (1-100, mặc định 20) và `cursor` (chuỗi base64url opaque), trả `meta.nextCursor`; helper buildCursorPage nhận limit+1 hàng. Offset chỉ cho danh sách admin nhỏ.
- evidence (evidence): docs/05-coding-standards.md:73, libs/common-dtos/src/lib/cursor-pagination.ts:11, libs/common-dtos/src/lib/cursor-pagination.ts:75

### #11

- area: Idempotency-Key
- convention: Truyền qua header `Idempotency-Key` (không nhét body), rút bằng `@IdempotencyKey()`, service prefix theo domain (vd speedupIdempotencyKey) và dựa vào unique constraint.
- evidence (evidence): docs/05-coding-standards.md:74, apps/core-api/src/common/decorators/idempotency-key.decorator.ts:11, apps/core-api/src/modules/matching/matching.service.ts:446

### #12

- area: i18n của lỗi
- convention: API đọc `Accept-Language` (vi|en), mặc định vi; filter chọn message theo mã lỗi; service chỉ tạo DomainException(code, message, ...). Client gửi header theo `getLocale()`.
- evidence (evidence): docs/05-coding-standards.md:85, apps/core-api/src/common/i18n/error-message.localizer.ts:4, libs/api-client/src/lib/client.ts:172

### #13

- area: Mã lỗi
- convention: `DOMAIN_SUBJECT_REASON` UPPER_SNAKE, hằng nằm trong object `XxxErrors` (PascalCase) với key UPPER_SNAKE, vd `MatchingErrors.TICKET_ALREADY_QUEUED`.
- evidence (evidence): docs/17-naming-conventions.md:140, docs/05-coding-standards.md:79

### #14

- area: Hằng số và định danh nội bộ
- convention: Hằng có ngữ nghĩa vượt một file vào `<module>.constants.ts` ngay từ đầu; Redis key, tên job, tên constraint, prefix idempotency key là hằng hoặc hàm builder ở file chủ quản.
- evidence (evidence): docs/05-coding-standards.md:15, apps/core-api/src/modules/matching/matching.constants.ts:7

### #15

- area: Cấu hình (env)
- convention: Env có prefix domain (vd MATCHING_SPEEDUP_PRICE_DIAMOND), khai default một lần trong Joi schema và kiểu trong CoreApiEnv; code đọc bằng `ConfigService<CoreApiEnv, true>.getOrThrow(KEY, { infer: true })`.
- evidence (evidence): docs/05-coding-standards.md:35, apps/core-api/src/config/env.validation.ts:471, apps/core-api/src/modules/matching/matching.service.ts:258

### #16

- area: Event và Kafka
- convention: Event `<domain>.<subject>.<động-từ-quá-khứ>` (vd `economy.diamond.deducted`); topic Kafka `litmatch.<domain>.events`; event schema có field `version`.
- evidence (evidence): docs/05-coding-standards.md:95, docs/05-coding-standards.md:96, docs/05-coding-standards.md:130

### #17

- area: Metric
- convention: Metric `<domain>_<subject>_<đơn_vị>` ghi bằng OTel Meter (vd matching_ticket_wait_seconds, call_ended_total, economy_transaction_total).
- evidence (evidence): docs/05-coding-standards.md:103

### #18

- area: Tên hàm theo hợp đồng
- convention: `findX` có thể null, `getXOrThrow` ném lỗi đặt tên, `listX` trả collection/page, `ensureX` idempotent, `assertX` ném, `recordX` cho bản ghi append-only; Promise không cần hậu tố Async.
- evidence (evidence): apps/core-api/src/modules/feed/feed.service.ts:195, apps/core-api/src/modules/calling/calling.service.ts:404, apps/core-api/src/modules/economy/economy.controller.ts:167, docs/17-naming-conventions.md:89

### #19

- area: DTO và entity
- convention: DTO input dùng class-validator, DTO output chỉ cần type và @ApiProperty, không trả entity trần; file mới ưu tiên một DTO/entity mỗi file (`*.dto.ts`, `*.entity.ts`).
- evidence (evidence): docs/05-coding-standards.md:21, docs/16-module-blueprint.md:149

### #20

- area: Test
- convention: Unit test `*.spec.ts` đặt cạnh source; integration test cả module đặt ở gốc module với hậu tố `.integration.spec.ts`; Economy/Matching bắt buộc có test race-condition.
- evidence (evidence): docs/16-module-blueprint.md:53, docs/05-coding-standards.md:122

### #21

- area: Migration
- convention: File `<timestamp>-<mô-tả-kebab>.ts`, luôn là file mới; không sửa migration đã commit.
- evidence (evidence): docs/17-naming-conventions.md:50, docs/16-module-blueprint.md:167

### #22

- area: Audit và bảng append-only
- convention: Audit cho hành động nhạy cảm là bảng DB append-only, không phải log text; ledger_entries dùng trigger chặn UPDATE/DELETE và admin_audit_log theo cùng pattern.
- evidence (evidence): docs/05-coding-standards.md:108, apps/core-api/src/database/migrations/1752000000000-economy-ledger.ts:77, apps/core-api/src/database/migrations/1753700000000-admin-audit-log.ts:6

### #23

- area: Thời gian (UTC)
- convention: Quy tắc theo ngày dùng ngày UTC của server (todayUtc), không dùng múi giờ client.
- evidence (evidence): docs/06-domain-rules.md:61, apps/core-api/src/common/date/utc-date.ts:2

### #24

- area: Transaction và khoá
- convention: Thao tác trừ/cộng diamond và check-then-act chạy trong một transaction với `SELECT ... FOR UPDATE` (TypeORM `pessimistic_write`) hoặc UPDATE có điều kiện, xác minh lại điều kiện tại thời điểm hành động.
- evidence (evidence): docs/05-coding-standards.md:143, apps/core-api/src/modules/matching/services/guest-match-quota.service.ts:55, apps/core-api/src/modules/short-video/short-video.service.ts:534

### #25

- area: Logging
- convention: Structured JSON qua pino (libs/logger), cấm console.log; traceId là request-id lấy từ `x-request-id`.
- evidence (evidence): docs/05-coding-standards.md:101, docs/05-coding-standards.md:84
