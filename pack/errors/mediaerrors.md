# Error codes MediaErrors

id: errors:MediaErrors
module: module:media

| code | http status at throw sites | throw sites | meaning | declared |
|---|---|---|---|---|
| MEDIA_GUEST_FORBIDDEN | FORBIDDEN | 1 |  | apps/core-api/src/modules/media/media.errors.ts:2 |
| MEDIA_INVALID_CONTENT_TYPE | UNPROCESSABLE_ENTITY | 1 |  | apps/core-api/src/modules/media/media.errors.ts:3 |
| MEDIA_FILE_TOO_LARGE | UNPROCESSABLE_ENTITY | 1 |  | apps/core-api/src/modules/media/media.errors.ts:4 |
| MEDIA_ASSET_NOT_FOUND | NOT_FOUND | 2 |  | apps/core-api/src/modules/media/media.errors.ts:5 |
| MEDIA_ASSET_NOT_READY | UNPROCESSABLE_ENTITY | 2 |  | apps/core-api/src/modules/media/media.errors.ts:6 |
