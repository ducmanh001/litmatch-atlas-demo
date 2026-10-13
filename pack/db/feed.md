# Database tables owned by module feed

id: db:feed
module: module:feed
tables: comments, posts, reactions, stories, story_views
see: ../modules/feed.md

### table: comments

Comment dưới 1 post — soft-delete, chỉ tác giả xoá được (feed-service.md § 2).

Entity: Comment (apps/core-api/src/modules/feed/entities/comment.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | uuid |  | gen_random_uuid() | PK |  |
| seq | bigint |  | nextval('comments_seq_seq'::regclass) |  |  |
| post_id | uuid |  |  | FK→posts.id |  |
| author_user_id | uuid |  |  | FK→users.id |  |
| content | text |  |  |  |  |
| deleted_at | timestamp with time zone | null |  |  |  |
| created_at | timestamp with time zone |  | now() |  |  |

Indexes: idx_comments_post_seq (post_id, seq)

Access: feed(owner) r2/w4 via manager+manager.save(instance)+querybuilder+repository [apps/core-api/src/modules/feed/feed.service.ts:251, apps/core-api/src/modules/feed/feed.service.ts:299]

### table: posts

Bài viết feed công khai toàn cục (docs/services/feed-service.md § 1) — không fanout, query thẳng theo `seq` giảm dần. Soft-delete (`deletedAt`) thay vì hard-delete vì `Comment`/ `Reaction` còn tham chiếu `postId` và cần giữ audit.

Entity: Post (apps/core-api/src/modules/feed/entities/post.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | uuid |  | gen_random_uuid() | PK |  |
| seq | bigint |  | nextval('posts_seq_seq'::regclass) |  | Thứ tự tăng dần DB cấp cho cursor keyset — không dùng createdAt (docs/05 § 5.4). |
| author_user_id | uuid |  |  | FK→users.id |  |
| content | text | null |  |  |  |
| image_url | character varying(2048) | null |  |  | Client tự upload ảnh ra host ngoài — backend chỉ lưu URL (không có storage/CDN ở GĐ4). |
| like_count | integer |  | 0 |  | Denormalized, cập nhật ATOMIC cùng transaction insert/delete Reaction (feed-service.md § 5). |
| comment_count | integer |  | 0 |  | Denormalized, cập nhật ATOMIC cùng transaction insert Comment. |
| deleted_at | timestamp with time zone | null |  |  |  |
| created_at | timestamp with time zone |  | now() |  |  |
| audience | character varying(16) |  | 'public'::character varying |  |  |
| idempotency_key | character varying(255) | null |  |  | NULL cho bài cũ trước W3 (migration không backfill) — chỉ bài mới bắt buộc có. |

Constraints: check chk_posts_audience (((audience)::text = ANY ((ARRAY['public'::character varying, 'friends'::character varying, 'only_me'::character varying])::text[]))); check chk_posts_content_or_image (((content IS NOT NULL) OR (image_url IS NOT NULL)))

Indexes: idx_posts_author_audience_seq (author_user_id, audience, seq); idx_posts_seq (seq); UNIQUE uq_posts_idempotency_key (idempotency_key) WHERE (idempotency_key IS NOT NULL)

Access: feed(owner) r4/w7 via manager+querybuilder+repository [apps/core-api/src/modules/feed/feed.service.ts:66, apps/core-api/src/modules/feed/feed.service.ts:67]

### table: reactions

Reaction "thả tim" (docs/01 #6) — 1 loại duy nhất, không phải bảng đa loại reaction. Nguồn sự thật ai đã like (toggle idempotent); `Post.likeCount` đếm atomic cùng transaction (feed-service.md § 5). Mutable/xoá được — KHÔNG phải hành động nhạy cảm cần audit như Block.

Entity: Reaction (apps/core-api/src/modules/feed/entities/reaction.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | uuid |  | gen_random_uuid() | PK |  |
| post_id | uuid |  |  | FK→posts.id |  |
| user_id | uuid |  |  | FK→users.id |  |
| created_at | timestamp with time zone |  | now() |  |  |

Constraints: unique uq_reactions_post_user (post_id,user_id)

Indexes: UNIQUE uq_reactions_post_user (post_id, user_id)

Access: feed(owner) r1/w3 via manager+manager.save(instance)+repository [apps/core-api/src/modules/feed/feed.service.ts:355, apps/core-api/src/modules/feed/feed.service.ts:385]

### table: stories

Story ephemeral (docs/services/feed-service.md § 8) — KHÁC `Post` (không soft-delete/audit): hết hạn = filter lúc đọc (`expiresAt <= now()`) là nguồn sự thật; sweeper hard-delete định kỳ chỉ dọn rác, không phải chốt correctness. `audience=public` hiện chưa có kênh phân phối rộng hơn ring bạn bè ở W3 (Ring stories: chỉ bạn bè + mình, quyết định chốt) — cột giữ để tương thích tương lai, không lộ khác biệt hành vi nào ở W3.

Entity: Story (apps/core-api/src/modules/feed/entities/story.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | uuid |  | gen_random_uuid() | PK |  |
| author_user_id | uuid |  |  | FK→users.id |  |
| media_url | character varying(2048) |  |  |  |  |
| caption | text | null |  |  |  |
| audience | character varying(16) |  | 'friends'::character varying |  |  |
| expires_at | timestamp with time zone |  |  |  | Snapshot lúc tạo = now + STORY_TTL_HOURS — không đổi theo config sau khi đã set. |
| idempotency_key | character varying(255) |  |  | UQ |  |
| created_at | timestamp with time zone |  | now() |  |  |

Constraints: check chk_stories_audience (((audience)::text = ANY ((ARRAY['public'::character varying, 'friends'::character varying])::text[]))); unique uq_stories_idempotency_key (idempotency_key)

Indexes: idx_stories_author_expires (author_user_id, expires_at); idx_stories_expires_at (expires_at); UNIQUE uq_stories_idempotency_key (idempotency_key)

Access: feed(owner) r5/w3 via querybuilder+raw-sql+repository [apps/core-api/src/modules/feed/jobs/story-sweeper.service.ts:61, apps/core-api/src/modules/feed/services/story.service.ts:61]

### table: story_views

Seen-state — unique (storyId, viewerId), chỉ tác giả truy vấn được danh sách (docs/services/ feed-service.md § 8). Self-view (tác giả xem story của chính mình) KHÔNG tạo dòng — không có ý nghĩa "đã xem" khi chính là người đăng.

Entity: StoryView (apps/core-api/src/modules/feed/entities/story-view.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | uuid |  | gen_random_uuid() | PK |  |
| story_id | uuid |  |  | FK→stories.id |  |
| viewer_id | uuid |  |  | FK→users.id |  |
| created_at | timestamp with time zone |  | now() |  |  |

Constraints: unique uq_story_views_story_viewer (story_id,viewer_id)

Indexes: UNIQUE uq_story_views_story_viewer (story_id, viewer_id)

Access: feed(owner) r1/w2 via repository [apps/core-api/src/modules/feed/services/story.service.ts:151, apps/core-api/src/modules/feed/services/story.service.ts:152]

