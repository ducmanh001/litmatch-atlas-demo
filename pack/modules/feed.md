# Module feed

id: module:feed
kind: core-api module
code: apps/core-api/src/modules/feed

Sở hữu Post/Comment/Reaction (feed công khai toàn cục, không fanout) và Story/StoryView (ephemeral, hết hạn lọc lúc đọc). KHÔNG sở hữu quan hệ bạn bè/block/privacy/ảnh/notification: mọi quyết định xem được hay không đều hỏi module chủ qua DI; Feed không có inbound caller nào.

## Facts

- tables owned: comments, posts, reactions, stories, story_views
- touches tables of other modules: user_privacy_settings (owner user; read 1, write 0)
- API: 16 endpoints, 0 public, 0 admin-only, 3 require Idempotency-Key, 0 with own throttle
- jobs: StorySweeperService
- env vars read: STORY_SWEEPER_INTERVAL_MS, STORY_TTL_HOURS
- error codes: 9 in FeedErrors
- tests: 2 unit spec files, 1 integration spec files
- history: 13 commits (3 fixes), last 2026-08-14
- docs: docs/services/feed-service.md (last changed 2026-08-12)
- features: feed-stories

## Where to read next

- tables: ../db/feed.md
- API contract: ../api/feed.md
- why it talks to other services: ../service-map/feed.md and ../service-map/feed-techniques.md
- error codes: ../errors/feederrors.md
- flows involving it: [feed-comment-with-notification](../flows/feed-comment-with-notification.md), [feed-delete-post](../flows/feed-delete-post.md), [feed-post-with-image](../flows/feed-post-with-image.md), [feed-reaction-with-notification](../flows/feed-reaction-with-notification.md), [feed-read-posts](../flows/feed-read-posts.md), [story-create-with-image](../flows/story-create-with-image.md), [story-reply-to-dm](../flows/story-reply-to-dm.md), [story-ring-view-and-viewers](../flows/story-ring-view-and-viewers.md)

## Linked issues (heuristic)

- P006 [fixed] Swagger thiếu cursor pagination → client sinh kiểu never

See ../issues/index.md.
