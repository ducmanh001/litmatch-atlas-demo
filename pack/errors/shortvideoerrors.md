# Error codes ShortVideoErrors

id: errors:ShortVideoErrors
module: module:short-video

| code | http status at throw sites | throw sites | meaning | declared |
|---|---|---|---|---|
| SHORT_VIDEO_UPLOAD_DISABLED | FORBIDDEN | 1 |  | apps/core-api/src/modules/short-video/short-video.errors.ts:3 |
| SHORT_VIDEO_NOT_FOUND | NOT_FOUND | 3 |  | apps/core-api/src/modules/short-video/short-video.errors.ts:4 |
| SHORT_VIDEO_FORBIDDEN | FORBIDDEN | 2 |  | apps/core-api/src/modules/short-video/short-video.errors.ts:5 |
| SHORT_VIDEO_INVALID_TRANSITION | CONFLICT,video ? CONFLICT : HttpStatus.NOT_FOUND | 3 |  | apps/core-api/src/modules/short-video/short-video.errors.ts:6 |
| SHORT_VIDEO_UPLOAD_IDEMPOTENCY_CONFLICT | CONFLICT | 1 |  | apps/core-api/src/modules/short-video/short-video.errors.ts:7 |
| SHORT_VIDEO_CAPTION_TOO_LONG | UNPROCESSABLE_ENTITY | 1 |  | apps/core-api/src/modules/short-video/short-video.errors.ts:8 |
| SHORT_VIDEO_CURSOR_INVALID | BAD_REQUEST | 2 |  | apps/core-api/src/modules/short-video/short-video.errors.ts:9 |
