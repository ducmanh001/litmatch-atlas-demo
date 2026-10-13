# Feature slice: short-video

id: slice:short-video
status: implemented; owner: core-api/short-video

Short-video lifecycle, moderation and derived ranking

This file follows ONE feature through every layer. Layers are joined by the actual endpoint calls found in the frontend code, so it shows what the code does, not what a plan says.

## 1. Product contract and docs

- registry contracts: openapi/core-api.json, docs/services/short-video-service.md, docs/services/safety-service.md
- registry verification tests: apps/core-api/src/modules/short-video/short-video.integration.spec.ts
- service docs: docs/services/short-video-service.md

## 2. Frontend

| route | app | endpoints of this feature it calls | page |
|---|---|---|---|
| [/video](../fe/route-web-video.md) | web | GET /videos; GET /videos/{id}/comments; POST /videos/{id}/comments; POST /videos/{id}/views; POST /videos/{id}/reactions; DELETE /videos/{id}/reactions | app/(app)/video/page.tsx |

FE features (directories): [web/party-room](../fe/feature-web-party-room.md), [web/short-video](../fe/feature-web-short-video.md)

UI action map card for /video: Playback actions: For You / Following / Play / pause / mute / Next / previous reel / Mở profile tác giả | Engagement actions: Like / Unlike / Comment / Tặng quà / Voice Match → Matching

User actions that mutate this feature's data:
- useCreateVideoComment -> POST /videos/{id}/comments (apps/web/src/features/short-video/components/video-comments-sheet.tsx:54)
- useRecordVideoView -> POST /videos/{id}/views (apps/web/src/features/short-video/components/video-slide.tsx:118)

## 3. API

| endpoint | auth | idempotency | throttle | FE callers |
|---|---|---|---|---|
| POST /videos/upload-intent ([short-video](../api/short-video.md)) | JWT | required |  | 0 |
| POST /videos/{id}/finalize ([short-video](../api/short-video.md)) | JWT |  |  | 0 |
| GET /videos ([short-video](../api/short-video.md)) | JWT |  |  | 1 |
| GET /videos/{id} ([short-video](../api/short-video.md)) | JWT |  |  | 0 |
| POST /videos/{id}/report ([short-video](../api/short-video.md)) | JWT |  |  | 1 |
| POST /videos/{id}/views ([short-video](../api/short-video.md)) | JWT |  |  | 1 |
| POST /videos/{id}/reactions ([short-video](../api/short-video.md)) | JWT |  |  | 1 |
| DELETE /videos/{id}/reactions ([short-video](../api/short-video.md)) | JWT |  |  | 1 |
| POST /videos/{id}/comments ([short-video](../api/short-video.md)) | JWT |  |  | 1 |
| GET /videos/{id}/comments ([short-video](../api/short-video.md)) | JWT |  |  | 1 |
| DELETE /videos/{id}/comments/{commentId} ([short-video](../api/short-video.md)) | JWT |  |  | 0 |


## 4. Backend flows (service map)

- [short-video-browse-and-detail](../flows/short-video-browse-and-detail.md): Duyệt video ngắn, xem chi tiết, view và reaction (5 endpoints of this feature)
- [short-video-comments](../flows/short-video-comments.md): Bình luận video ngắn (3 endpoints of this feature)
- [short-video-publish-moderation](../flows/short-video-publish-moderation.md): Đăng video ngắn và duyệt bởi admin (2 endpoints of this feature)
- [short-video-report](../flows/short-video-report.md): Báo cáo video vi phạm (1 endpoints of this feature)

## 5. Data

- tables owned by the module(s): `video_comments`, `video_reactions`, `video_views`, `videos` (see ../db/short-video.md)
- status vocabularies enforced by CHECK:
  - videos: uploading | processing | pending_review | published | removed | rejected | failed

## 6. Events

No realtime event is produced by these modules.

## 7. Errors

- error code groups: [ShortVideoErrors](../errors/shortvideoerrors.md)
- codes the FE references for this feature: none

## 8. Configuration

env vars read by the module(s): `VIDEO_RANKING_JOB_INTERVAL_MS`, `VIDEO_RANK_WEIGHT_VIEW`, `VIDEO_RANK_WEIGHT_LIKE`, `VIDEO_RANK_WEIGHT_COMMENT`, `VIDEO_RANK_TIME_DECAY_HOURS`, `VIDEO_SWEEPER_INTERVAL_MS`, `VIDEO_UPLOAD_TIMEOUT_SECONDS`, `NODE_ENV`, `VIDEO_UPLOAD_ENABLED`, `VIDEO_CAPTION_MAX_LENGTH`, `VIDEO_MODERATION_MODE`, `VIDEO_QUALIFIED_VIEW_MIN_MS`, `VIDEO_REPORT_AUTOHIDE_THRESHOLD` (see ../config/)

## 9. Tests

- short-video: 3 unit spec files, 1 integration spec files
- e2e files that call these endpoints: none
- FE specs: apps/web/src/features/party-room/api.spec.ts, apps/web/src/features/party-room/components/member-list.spec.tsx, apps/web/src/features/party-room/components/party-chat.spec.tsx, apps/web/src/features/party-room/components/party-stage.spec.tsx, apps/web/src/features/party-room/components/room-list.spec.tsx, apps/web/src/features/party-room/create-room-schema.spec.ts, apps/web/src/features/party-room/hooks/use-party-room-media.spec.tsx, apps/web/src/features/short-video/components/video-comments-sheet.spec.tsx, apps/web/src/features/short-video/components/video-like-button.spec.tsx, apps/web/src/features/short-video/components/video-reel-feed.spec.tsx, apps/web/src/features/short-video/create-video-comment-schema.spec.ts

## 10. Known issues (public subset of the ledger)

- P088 [fixed] View count video cộng đôi khi đồng thời
- P089 [fixed] Sweeper video lỗi bị đói (lặp 200 hàng cũ nhất)
- P108 [fixed] Replay upload-intent cấp lại URL sau khi video đã published/failed

See ../issues/index.md.
