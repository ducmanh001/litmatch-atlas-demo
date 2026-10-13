# Module media

id: module:media
kind: core-api module
code: apps/core-api/src/modules/media

Cấp presigned PUT URL để client upload ảnh thẳng lên object storage và là nơi DUY NHẤT xác nhận asset hợp lệ (đúng chủ, đúng purpose, đã upload, MIME/size khớp magic bytes) trước khi Feed/Friend lưu URL ảnh. Không nhận body binary, không phải LiveKit media-server, không xử lý video (Short Video có VideoStoragePort riêng).

## Facts

- tables owned: image_assets
- API: 1 endpoints, 0 public, 0 admin-only, 0 require Idempotency-Key, 0 with own throttle
- env vars read: MEDIA_STORAGE_PROVIDER, MEDIA_IMAGE_MAX_BYTES, MEDIA_ALLOWED_IMAGE_TYPES, NODE_ENV, MEDIA_R2_ACCOUNT_ID, MEDIA_R2_BUCKET, MEDIA_R2_ACCESS_KEY_ID, MEDIA_R2_SECRET_ACCESS_KEY, MEDIA_PUBLIC_BASE_URL, MEDIA_UPLOAD_URL_TTL_SECONDS, AWS_S3_ENDPOINT, AWS_REGION, AWS_S3_BUCKET, AWS_ACCESS_KEY_ID, AWS_SECRET_ACCESS_KEY, AWS_S3_FORCE_PATH_STYLE
- error codes: 5 in MediaErrors
- tests: 2 unit spec files, 0 integration spec files
- history: 1 commits (0 fixes), last 2026-08-12
- docs: docs/services/media-service.md (last changed 2026-08-12)

## Where to read next

- tables: ../db/media.md
- API contract: ../api/media.md
- why it talks to other services: ../service-map/media.md and ../service-map/media-techniques.md
- error codes: ../errors/mediaerrors.md
- flows involving it: [feed-post-with-image](../flows/feed-post-with-image.md), [story-create-with-image](../flows/story-create-with-image.md)
