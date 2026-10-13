# Error codes FeedErrors

id: errors:FeedErrors
module: module:feed

| code | http status at throw sites | throw sites | meaning | declared |
|---|---|---|---|---|
| FEED_POST_NOT_FOUND | NOT_FOUND,FORBIDDEN | 3 |  | apps/core-api/src/modules/feed/feed.errors.ts:3 |
| FEED_COMMENT_NOT_FOUND | NOT_FOUND,FORBIDDEN | 2 |  | apps/core-api/src/modules/feed/feed.errors.ts:4 |
| FEED_POST_CONTENT_REQUIRED | UNPROCESSABLE_ENTITY | 1 |  | apps/core-api/src/modules/feed/feed.errors.ts:5 |
| FEED_GUEST_FORBIDDEN | FORBIDDEN | 2 |  | apps/core-api/src/modules/feed/feed.errors.ts:6 |
| FEED_CURSOR_INVALID | BAD_REQUEST | 3 |  | apps/core-api/src/modules/feed/feed.errors.ts:7 |
| FEED_POST_IDEMPOTENCY_CONFLICT | CONFLICT | 2 |  | apps/core-api/src/modules/feed/feed.errors.ts:8 |
| FEED_STORY_NOT_FOUND | NOT_FOUND | 3 |  | apps/core-api/src/modules/feed/feed.errors.ts:9 |
| FEED_STORY_MEDIA_URL_REQUIRED | UNPROCESSABLE_ENTITY | 1 |  | apps/core-api/src/modules/feed/feed.errors.ts:10 |
| FEED_STORY_REPLY_REQUIRES_FRIENDSHIP | FORBIDDEN | 1 |  | apps/core-api/src/modules/feed/feed.errors.ts:11 |
