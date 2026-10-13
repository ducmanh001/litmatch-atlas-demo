# Database tables owned by module short-video

id: db:short-video
module: module:short-video
tables: video_comments, video_reactions, video_views, videos
see: ../modules/short-video.md

### table: video_comments

Comment dưới 1 video — sở hữu riêng bởi `short-video` (KHÔNG share bảng `comments` của Feed dù cùng shape).

Entity: VideoComment (apps/core-api/src/modules/short-video/entities/video-comment.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | uuid |  | gen_random_uuid() | PK |  |
| seq | bigint |  | nextval('video_comments_seq_seq'::regclass) |  |  |
| video_id | uuid |  |  | FK→videos.id |  |
| author_user_id | uuid |  |  | FK→users.id |  |
| content | text |  |  |  |  |
| deleted_at | timestamp with time zone | null |  |  |  |
| created_at | timestamp with time zone |  | now() |  |  |

Indexes: idx_video_comments_video_seq (video_id, seq)

Access: short-video(owner) r2/w4 via manager+manager.save(instance)+querybuilder+repository [apps/core-api/src/modules/short-video/short-video.service.ts:352, apps/core-api/src/modules/short-video/short-video.service.ts:370]

### table: video_reactions

Reaction "thả tim" cho video — 1 loại duy nhất, toggle idempotent, sở hữu riêng bởi `short-video`.

Entity: VideoReaction (apps/core-api/src/modules/short-video/entities/video-reaction.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | uuid |  | gen_random_uuid() | PK |  |
| video_id | uuid |  |  | FK→videos.id |  |
| user_id | uuid |  |  | FK→users.id |  |
| created_at | timestamp with time zone |  | now() |  |  |

Constraints: unique uq_video_reactions_video_user (video_id,user_id)

Indexes: UNIQUE uq_video_reactions_video_user (video_id, user_id)

Access: short-video(owner) r0/w3 via manager+manager.save(instance) [apps/core-api/src/modules/short-video/short-video.service.ts:312, apps/core-api/src/modules/short-video/short-video.service.ts:329]

### table: video_views

View 1 video (docs/services/short-video-service.md § 3) — unique (video, viewer) chống đếm đôi, cùng pattern `story_views`. `qualified` = đã xem đủ ngưỡng thời lượng (`VIDEO_QUALIFIED_VIEW_MIN_MS`) — chỉ view qualified mới cộng `Video.viewCount`. Self-view KHÔNG được ghi (chặn ở `VideoService.recordView` trước khi insert).

Entity: VideoView (apps/core-api/src/modules/short-video/entities/video-view.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | uuid |  | gen_random_uuid() | PK |  |
| video_id | uuid |  |  | FK→videos.id |  |
| viewer_id | uuid |  |  | FK→users.id |  |
| watch_time_ms | integer |  | 0 |  |  |
| qualified | boolean |  | false |  |  |
| created_at | timestamp with time zone |  | now() |  |  |

Constraints: unique uq_video_views_video_viewer (video_id,viewer_id)

Indexes: UNIQUE uq_video_views_video_viewer (video_id, viewer_id)

Access: short-video(owner) r1/w3/lock1 via manager+manager(lock option)+querybuilder [apps/core-api/src/modules/short-video/short-video.service.ts:272, apps/core-api/src/modules/short-video/short-video.service.ts:284]

### table: videos

Video ngắn (docs/services/short-video-service.md) — V1 hướng Momo, KHÔNG phải feed toàn cục cạnh tranh TikTok. Upload qua presigned URL (`VideoStoragePort`) — body video không bao giờ chạm NestJS, `storageKey` chỉ là object key trỏ tới file trên storage. `playbackUrl`/ `thumbnailUrl`/`durationSeconds` NULL cho tới khi transcode xong (`VideoTranscodePort`). `likeCount`/`commentCount`/`viewCount` denormalized, cập nhật ATOMIC cùng transaction insert dòng nguồn (cùng pattern `Post.likeCount`) — KHÔNG phải nguồn sự thật, chỉ cache đọc nhanh.

Entity: Video (apps/core-api/src/modules/short-video/entities/video.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | uuid |  | gen_random_uuid() | PK |  |
| author_user_id | uuid |  |  | FK→users.id |  |
| status | character varying(16) |  | 'uploading'::character varying |  |  |
| storage_key | character varying(512) |  |  |  |  |
| playback_url | character varying(2048) | null |  |  |  |
| thumbnail_url | character varying(2048) | null |  |  |  |
| caption | text | null |  |  |  |
| duration_seconds | integer | null |  |  |  |
| view_count | integer |  | 0 |  |  |
| like_count | integer |  | 0 |  |  |
| comment_count | integer |  | 0 |  |  |
| rank_score | double precision | null |  |  | NULL tới khi ranking job chạy lần đầu — fallback `sort=recent` khi NULL (short-video-service.md § 4). |
| idempotency_key | character varying(255) |  |  | UQ |  |
| created_at | timestamp with time zone |  | now() |  |  |
| updated_at | timestamp with time zone |  | now() |  |  |
| storage_cleaned_at | timestamp with time zone | null |  |  | Sweeper đã xoá object trên storage của video `failed`; NULL = còn phải dọn. |

Constraints: check videos_comment_count_check ((comment_count >= 0)); check videos_like_count_check ((like_count >= 0)); check videos_status_check (((status)::text = ANY ((ARRAY['uploading'::character varying, 'processing'::character varying, 'pending_review'::character varying, 'published'::character var…; check videos_view_count_check ((view_count >= 0)); unique uq_videos_idempotency_key (idempotency_key)

Indexes: idx_videos_author (author_user_id); idx_videos_failed_uncleaned (updated_at, id) WHERE (((status)::text = 'failed'::text) AND (storage_cleaned_at IS NULL)); idx_videos_status_created (status, created_at); UNIQUE uq_videos_idempotency_key (idempotency_key)

Access: short-video(owner) r9/w12 via manager+querybuilder+raw-sql+repository [apps/core-api/src/modules/short-video/jobs/video-ranking.service.ts:76, apps/core-api/src/modules/short-video/jobs/video-sweeper.service.ts:70]

