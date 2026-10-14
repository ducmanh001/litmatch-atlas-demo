# Domain rules, error handling, glossary, conventions: unusedErrorCodes

id: system:domain:unusedErrorCodes

Written by an AI agent from the code; every file:line quote was checked automatically (exists within ±3 lines) but NO independent verification pass has run yet. Treat claims as strong leads; open the cited file before editing.

### ECONOMY_IAP_PROVIDER_UNAVAILABLE

- file: apps/core-api/src/modules/economy/economy.errors.ts
- line: 9
- quote: IAP_PROVIDER_UNAVAILABLE: 'ECONOMY_IAP_PROVIDER_UNAVAILABLE'
- kind: không phải mã chết (dùng ngoài DomainException)
- note: Dùng làm tiền tố message của ServiceUnavailableException ở hai adapter gọi API refund của Apple/Google; không bao giờ là `error.code` của API.
- usedAt (evidence): apps/core-api/src/modules/economy/clients/apple-refund-api.adapter.ts:52, apps/core-api/src/modules/economy/clients/google-refund-api.adapter.ts:63

### ECONOMY_PAYOS_DISABLED

- file: apps/core-api/src/modules/economy/economy.errors.ts
- line: 13
- quote: PAYOS_DISABLED: 'ECONOMY_PAYOS_DISABLED'
- kind: không phải mã chết (dùng ngoài DomainException)
- note: Truyền làm tham số đầu của ServiceUnavailableException khi thiếu cấu hình payOS và làm tiền tố message ở payos.service.ts. Theo mã, client nhận 503 với code COMMON_INTERNAL_ERROR, mã riêng chỉ nằm trong message (locale vi).
- usedAt (evidence): apps/core-api/src/modules/economy/ports/payos-client.ts:188, apps/core-api/src/modules/economy/services/payos.service.ts:271

### ECONOMY_PAYOS_PROVIDER_UNAVAILABLE

- file: apps/core-api/src/modules/economy/economy.errors.ts
- line: 17
- quote: PAYOS_PROVIDER_UNAVAILABLE: 'ECONOMY_PAYOS_PROVIDER_UNAVAILABLE'
- kind: không phải mã chết (dùng ngoài DomainException)
- note: Truyền làm tham số đầu của ServiceUnavailableException khi payOS không tạo được liên kết thanh toán; cùng hệ quả COMMON_INTERNAL_ERROR như trên.
- usedAt (evidence): apps/core-api/src/modules/economy/ports/payos-client.ts:220

### PARTY_MEDIA_ROOM_CREATE_FAILED

- file: apps/core-api/src/modules/party-room/party-room.errors.ts
- line: 30
- quote: MEDIA_ROOM_CREATE_FAILED: 'PARTY_MEDIA_ROOM_CREATE_FAILED'
- kind: không phải mã chết (dùng ngoài DomainException)
- note: Truyền cho ServiceUnavailableException khi tạo room LiveKit thất bại (lỗi hạ tầng 5xx, cố ý không dùng DomainException); test tích hợp khẳng định message và status 503.
- usedAt (evidence): apps/core-api/src/modules/party-room/party-room.service.ts:221, apps/core-api/src/modules/party-room/party-room.integration.spec.ts:317

### COMMON_VALIDATION_FAILED

- file: libs/common-exceptions/src/lib/common.errors.ts
- line: 3
- quote: VALIDATION_FAILED: 'COMMON_VALIDATION_FAILED'
- kind: dự phòng, do GlobalExceptionFilter phát
- note: Không có throw DomainException nào dùng; filter gán mã này cho 400 có message dạng mảng và làm mã mặc định của status 400.
- usedAt (evidence): apps/core-api/src/common/filters/global-exception.filter.ts:59

### COMMON_ROUTE_NOT_FOUND

- file: libs/common-exceptions/src/lib/common.errors.ts
- line: 4
- quote: ROUTE_NOT_FOUND: 'COMMON_ROUTE_NOT_FOUND'
- kind: dự phòng, do GlobalExceptionFilter phát
- note: Filter gán mã này cho mọi HttpException 404 (vd route không tồn tại).
- usedAt (evidence): apps/core-api/src/common/filters/global-exception.filter.ts:96

### COMMON_RATE_LIMITED

- file: libs/common-exceptions/src/lib/common.errors.ts
- line: 5
- quote: RATE_LIMITED: 'COMMON_RATE_LIMITED'
- kind: dự phòng, do GlobalExceptionFilter phát
- note: Filter gán mã này cho HttpException 429 do ThrottlerGuard ném.
- usedAt (evidence): apps/core-api/src/common/filters/global-exception.filter.ts:98

### COMMON_UNAUTHORIZED

- file: libs/common-exceptions/src/lib/common.errors.ts
- line: 6
- quote: UNAUTHORIZED: 'COMMON_UNAUTHORIZED'
- kind: dự phòng, do GlobalExceptionFilter phát
- note: Filter gán mã này cho HttpException 401 (vd UnauthorizedException của JwtAuthGuard).
- usedAt (evidence): apps/core-api/src/common/filters/global-exception.filter.ts:92, apps/core-api/src/common/guards/jwt-auth.guard.ts:50

### COMMON_INTERNAL_ERROR

- file: libs/common-exceptions/src/lib/common.errors.ts
- line: 8
- quote: INTERNAL_ERROR: 'COMMON_INTERNAL_ERROR'
- kind: dự phòng, do GlobalExceptionFilter phát
- note: Filter gán mã này cho exception bất ngờ và làm mã mặc định cho mọi HttpException status không có nhánh riêng.
- usedAt (evidence): apps/core-api/src/common/filters/global-exception.filter.ts:66
