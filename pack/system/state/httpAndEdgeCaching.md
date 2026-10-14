# Cache and state: Redis, locks, idempotency, client state: httpAndEdgeCaching

id: system:state:httpAndEdgeCaching

Written by an AI agent from the code; every file:line quote was checked automatically (exists within ±3 lines) but NO independent verification pass has run yet. Treat claims as strong leads; open the cited file before editing.

### Chỉ bật helmet() và CORS (origin theo CORS_ORIGINS, credentials true); mã ứng dụng không đặt Cache-…

- layer: core-api Express
- evidence: apps/core-api/src/main.ts:65
- moreEvidence (evidence): apps/core-api/src/main.ts:76

### URL upload hết hạn sau MEDIA_UPLOAD_URL_TTL_SECONDS (mặc định 900 s, khoảng 60-604800); đây là TTL …

- layer: Presigned upload URL (R2/S3)
- evidence: apps/core-api/src/modules/media/ports/s3-image-storage.provider.ts:108
- moreEvidence (evidence): apps/core-api/src/config/env.validation.ts:347, apps/core-api/src/config/env.validation.ts:351
