# REST API of mood

id: api:mood
module: module:mood
prefix: /api/v1; success body {data, meta?}; errors are DomainException codes (see ../errors/)
see: ../modules/mood.md

### GET /mood/presets

Liệt kê preset mood đang active (sortOrder, code) để client dựng UI chọn; chỉ cần JWT.

- handler: MoodController.listPresets (apps/core-api/src/modules/mood/mood.controller.ts:39); operationId: MoodController_listPresets
- auth: Bearer JWT
- response 200 array<MoodPresetDto>: [].code:string, [].label:string, [].emoji:string
- FE callers: none in this repo (mobile/third party or dead)

### GET /mood/status/me

Mood hiện tại của chính mình, suy ra từ dòng event mới nhất; không qua kiểm tra block.

- handler: MoodController.getMyStatus (apps/core-api/src/modules/mood/mood.controller.ts:47); operationId: MoodController_getMyStatus
- auth: Bearer JWT
- response 200 MoodStatusResponseDto: mood:CurrentMoodDto, mood.preset:MoodPresetDto, mood.preset.code:string, mood.preset.label:string, mood.preset.emoji:string, mood.setAt:string(date-time), mood.expiresAt:string(date-time)
- FE callers: none in this repo (mobile/third party or dead)

### GET /mood/status/{userId}

Mood công khai của userId khác theo góc nhìn người xem; trả rỗng nếu giữa hai người có block active 2 chiều (SafetyService.getBlockedUserIds).

- handler: MoodController.getStatus (apps/core-api/src/modules/mood/mood.controller.ts:58); operationId: MoodController_getStatus
- auth: Bearer JWT
- params: path:userId*:string
- response 200 MoodStatusResponseDto: mood:CurrentMoodDto, mood.preset:MoodPresetDto, mood.preset.code:string, mood.preset.label:string, mood.preset.emoji:string, mood.setAt:string(date-time), mood.expiresAt:string(date-time)
- FE callers: none in this repo (mobile/third party or dead)

### POST /mood/status

Đặt mood theo presetCode đang active, bắt buộc header Idempotency-Key; mỗi lần gọi (kể cả cùng preset) là 1 dòng event mới và refresh TTL MOOD_STATUS_TTL_HOURS.

- handler: MoodController.setStatus (apps/core-api/src/modules/mood/mood.controller.ts:73); operationId: MoodController_setStatus
- auth: Bearer JWT; requires Idempotency-Key header
- params: header:Idempotency-Key*:string
- body SetMoodDto: presetCode*:string
- response 200 MoodStatusResponseDto: mood:CurrentMoodDto, mood.preset:MoodPresetDto, mood.preset.code:string, mood.preset.label:string, mood.preset.emoji:string, mood.setAt:string(date-time), mood.expiresAt:string(date-time)
- FE callers: none in this repo (mobile/third party or dead)

### DELETE /mood/status

Tắt mood bằng cách ghi dòng kind='clear' (không xoá dòng cũ), bắt buộc Idempotency-Key, trả 204.

- handler: MoodController.clearStatus (apps/core-api/src/modules/mood/mood.controller.ts:93); operationId: MoodController_clearStatus
- auth: Bearer JWT; requires Idempotency-Key header
- params: header:Idempotency-Key*:string
- response 204: 
- FE callers: none in this repo (mobile/third party or dead)

