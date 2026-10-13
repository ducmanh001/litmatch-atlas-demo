# REST API of app-shell

id: api:app-shell
module: module:app-shell
prefix: /api/v1; success body {data, meta?}; errors are DomainException codes (see ../errors/)
see: ../modules/app-shell.md

### GET /capabilities

Trả CapabilitiesDto (auth.phoneOtp/google/apple/facebook/guest, topUp.web/native, video, notifications.push) suy ra từ env thật và danh sách bảo trì CAPABILITY_MAINTENANCE_FEATURES; @Public, @SkipThrottle.

- handler: CapabilitiesController.getCapabilities (apps/core-api/src/app/capabilities.controller.ts:14); operationId: CapabilitiesController_getCapabilities
- auth: PUBLIC; throttle: skipped
- response 200 CapabilitiesDto: auth:AuthCapabilitiesDto, auth.phoneOtp:AuthProviderCapabilityDto, auth.phoneOtp.status:CapabilityStatus, auth.phoneOtp.message:string, auth.phoneOtp.clientId:string, auth.google:AuthProviderCapabilityDto, auth.google.status:CapabilityStatus, auth.google.message:string, auth.google.clientId:string, auth.apple:AuthProviderCapabilityDto, auth.apple.status:CapabilityStatus, auth.apple.message:string, auth.apple.clientId:string, auth.facebook:AuthProviderCapabilityDto, auth.facebook.status:CapabilityStatus, auth.facebook.message:string, auth.facebook.clientId:string, auth.guest:CapabilityStateDto, auth.guest.status:CapabilityStatus, auth.guest.message:string, topUp:TopUpCapabilitiesDto, topUp.web:CapabilityStateDto, topUp.web.status:CapabilityStatus, topUp.web.message:string, topUp.native:CapabilityStateDto, topUp.native.status:CapabilityStatus, topUp.native.message:string, topUp.nativeApple:CapabilityStateDto, topUp.nativeApple.status:CapabilityStatus, topUp.nativeApple.message:string, topUp.nativeGoogle:CapabilityStateDto, topUp.nativeGoogle.status:CapabilityStatus, topUp.nativeGoogle.message:string, video:VideoCapabilitiesDto, video.upload:CapabilityStateDto, video.upload.status:CapabilityStatus, video.upload.message:string, video.transcode:CapabilityStateDto, video.transcode.status:CapabilityStatus, video.transcode.message:string, notifications:NotificationCapabilitiesDto, notifications.push:CapabilityStateDto, notifications.push.status:CapabilityStatus, notifications.push.message:string
- FE callers: web apps/web/src/shared/capabilities/api.ts:24; admin apps/admin/src/shared/capabilities/api.ts:24

### GET /health

Liveness tương thích ngược, trả status ok + uptimeSeconds; không kiểm tra Redis.

- handler: HealthController.liveCompatibility (apps/core-api/src/app/health.controller.ts:16); operationId: HealthController_liveCompatibility
- auth: PUBLIC; throttle: skipped
- response 200: 
- FE callers: none in this repo (mobile/third party or dead)

### GET /health/live

Liveness probe của K8s: chỉ cho biết process còn sống, không phụ thuộc Redis.

- handler: HealthController.live (apps/core-api/src/app/health.controller.ts:24); operationId: HealthController_live
- auth: PUBLIC; throttle: skipped
- response 200: 
- FE callers: none in this repo (mobile/third party or dead)

### GET /health/ready

Readiness probe: 503 nếu PSUBSCRIBE realtime:user:* chưa sẵn sàng, quota Redis chưa ready hoặc Redis cluster adapter chưa ready (checks.redisSubscription / redisClusterAdapter).

- handler: HealthController.ready (apps/core-api/src/app/health.controller.ts:34); operationId: HealthController_ready
- auth: PUBLIC; throttle: { default: { limit: 30, ttl: minutes(1) } }
- response 503: 
- FE callers: none in this repo (mobile/third party or dead)

