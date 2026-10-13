# Module short-video

id: module:short-video
kind: core-api module
code: apps/core-api/src/modules/short-video

Sở hữu vòng đời Video (uploading -> processing -> pending_review|published -> removed|rejected|failed), view/reaction/comment và ranking v1. Upload đi qua VideoStoragePort/VideoTranscodePort RIÊNG của module (KHÔNG dùng module Media); không sở hữu graph bạn bè, report/trust (Safety), hồ sơ tác giả (User) hay tiền quà tặng (Gift/Economy).

## Facts

- tables owned: video_comments, video_reactions, video_views, videos
- API: 11 endpoints, 0 public, 0 admin-only, 1 require Idempotency-Key, 0 with own throttle
- jobs: VideoRankingService, VideoSweeperService
- env vars read: VIDEO_RANKING_JOB_INTERVAL_MS, VIDEO_RANK_WEIGHT_VIEW, VIDEO_RANK_WEIGHT_LIKE, VIDEO_RANK_WEIGHT_COMMENT, VIDEO_RANK_TIME_DECAY_HOURS, VIDEO_SWEEPER_INTERVAL_MS, VIDEO_UPLOAD_TIMEOUT_SECONDS, NODE_ENV, VIDEO_UPLOAD_ENABLED, VIDEO_CAPTION_MAX_LENGTH, VIDEO_MODERATION_MODE, VIDEO_QUALIFIED_VIEW_MIN_MS, VIDEO_REPORT_AUTOHIDE_THRESHOLD
- error codes: 7 in ShortVideoErrors
- tests: 3 unit spec files, 1 integration spec files
- history: 10 commits (3 fixes), last 2026-10-09
- docs: docs/services/short-video-service.md (last changed 2026-10-09)
- features: short-video

## Where to read next

- tables: ../db/short-video.md
- API contract: ../api/short-video.md
- why it talks to other services: ../service-map/short-video.md and ../service-map/short-video-techniques.md
- error codes: ../errors/shortvideoerrors.md
- flows involving it: [admin-video-moderation-queue](../flows/admin-video-moderation-queue.md), [gift-on-video](../flows/gift-on-video.md), [short-video-browse-and-detail](../flows/short-video-browse-and-detail.md), [short-video-comments](../flows/short-video-comments.md), [short-video-publish-moderation](../flows/short-video-publish-moderation.md), [short-video-ranking-and-sweeper-jobs](../flows/short-video-ranking-and-sweeper-jobs.md), [short-video-report](../flows/short-video-report.md)

## Linked issues (heuristic, public subset)

- P088 [fixed] View count video cộng đôi khi đồng thời
- P089 [fixed] Sweeper video lỗi bị đói (lặp 200 hàng cũ nhất)
- P108 [fixed] Replay upload-intent cấp lại URL sau khi video đã published/failed

See ../issues/index.md.
