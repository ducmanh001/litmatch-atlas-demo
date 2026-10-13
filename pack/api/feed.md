# REST API of feed

id: api:feed
module: module:feed
prefix: /api/v1; success body {data, meta?}; errors are DomainException codes (see ../errors/)
see: ../modules/feed.md

### POST /stories

Đăng story từ mediaAssetId: bắt buộc Idempotency-Key (unique DB), guest bị chặn, URL ảnh chỉ nhận qua Media.resolveImageUrl, hết hạn sau STORY_TTL_HOURS.

- handler: StoryController.createStory (apps/core-api/src/modules/feed/controllers/story.controller.ts:45); operationId: StoryController_createStory
- auth: Bearer JWT; requires Idempotency-Key header
- params: header:Idempotency-Key*:string
- body CreateStoryDto: mediaAssetId*:string, caption:string, audience:enum(public|friends)[default="friends"]
- response 201 StoryDto: id:string, authorUserId:string, mediaUrl:string, caption:string, audience:enum(public|friends), expiresAt:string(date-time), createdAt:string(date-time)
- FE callers: none in this repo (mobile/third party or dead)

### GET /stories/ring

Ring story còn hạn của mình + bạn bè (listFriendIds), loại tác giả block 2 chiều và tác giả ẩn hồ sơ.

- handler: StoryController.getRing (apps/core-api/src/modules/feed/controllers/story.controller.ts:76); operationId: StoryController_getRing
- auth: Bearer JWT
- response 200 array<StoryDto>: [].id:string, [].authorUserId:string, [].mediaUrl:string, [].caption:string, [].audience:enum(public|friends), [].expiresAt:string(date-time), [].createdAt:string(date-time)
- FE callers: none in this repo (mobile/third party or dead)

### GET /stories/{storyId}

Xem 1 story và ghi StoryView (self-view không đếm, xem lại idempotent); block/hidden/sai audience đều trả 404 STORY_NOT_FOUND.

- handler: StoryController.viewStory (apps/core-api/src/modules/feed/controllers/story.controller.ts:87); operationId: StoryController_viewStory
- auth: Bearer JWT
- params: path:storyId*:string
- response 200 StoryDto: id:string, authorUserId:string, mediaUrl:string, caption:string, audience:enum(public|friends), expiresAt:string(date-time), createdAt:string(date-time)
- FE callers: none in this repo (mobile/third party or dead)

### GET /stories/{storyId}/viewers

Danh sách người xem story, CHỈ tác giả truy vấn được; lọc block hiện tại lúc đọc.

- handler: StoryController.listViewers (apps/core-api/src/modules/feed/controllers/story.controller.ts:100); operationId: StoryController_listViewers
- auth: Bearer JWT
- params: path:storyId*:string
- response 200 StoryViewersDto: viewerIds:array<string>
- FE callers: none in this repo (mobile/third party or dead)

### POST /stories/{storyId}/reply

Trả lời story thành DM qua FriendService.sendMessage (attachment story_reply); cần đã có Conversation với tác giả (thường là bạn, hoặc mở trước qua profile chat/gift), nếu không 403 STORY_REPLY_REQUIRES_FRIENDSHIP.

- handler: StoryController.replyToStory (apps/core-api/src/modules/feed/controllers/story.controller.ts:113); operationId: StoryController_replyToStory
- auth: Bearer JWT; requires Idempotency-Key header
- params: path:storyId*:string, header:Idempotency-Key*:string
- body ReplyToStoryDto: content*:string
- response 201 MessageDto: id:string, conversationId:string, senderUserId:string, content:string, attachment:MessageAttachmentDto, attachment.kind:string, attachment.payload:object, sentAt:string(date-time)
- FE callers: none in this repo (mobile/third party or dead)

### POST /feed/posts

Đăng bài: Idempotency-Key (unique DB, khác nội dung 409), guest bị chặn, ảnh chỉ qua imageAssetId được Media resolve, audience mặc định public.

- handler: FeedController.createPost (apps/core-api/src/modules/feed/feed.controller.ts:53); operationId: FeedController_createPost
- auth: Bearer JWT; requires Idempotency-Key header
- params: header:Idempotency-Key*:string
- body CreatePostDto: content:string, imageAssetId:string, audience:enum(public|friends|only_me)[default="public"]
- response 201 PostDto: id:string, authorUserId:string, author:PublicProfileDto, author.id:string, author.nickname:string, author.gender:enum(unknown|male|female|other), author.avatarId:string, author.interests:array<string>, content:string, imageUrl:string, audience:enum(public|friends|only_me), likeCount:number, commentCount:number, createdAt:string(date-time)
- FE callers: web apps/web/src/features/feed/api.ts:70

### GET /feed/posts

Feed công khai toàn cục (chỉ audience=public), cursor seq giảm dần, loại tác giả đang block/bị block.

- handler: FeedController.listFeed (apps/core-api/src/modules/feed/feed.controller.ts:84); operationId: FeedController_listFeed
- auth: Bearer JWT
- params: query:limit:number, query:cursor:string
- response 200 PostsPageDto: items:array<PostDto>, items[].id:string, items[].authorUserId:string, items[].author:PublicProfileDto, items[].author.id:string, items[].author.nickname:string, items[].author.gender:enum(unknown|male|female|other), items[].author.avatarId:string, items[].author.interests:array<string>, items[].content:string, items[].imageUrl:string, items[].audience:enum(public|friends|only_me), items[].likeCount:number, items[].commentCount:number, items[].createdAt:string(date-time), nextCursor:string
- FE callers: web apps/web/src/features/feed/api.ts:33

### GET /feed/users/{userId}/posts

Timeline một tác giả: audience khớp quan hệ (chính mình thấy hết, bạn thấy public+friends, người lạ chỉ public); block/hidden trả như không tồn tại.

- handler: FeedController.listUserTimeline (apps/core-api/src/modules/feed/feed.controller.ts:99); operationId: FeedController_listUserTimeline
- auth: Bearer JWT
- params: path:userId*:string, query:limit:number, query:cursor:string
- response 200 PostsPageDto: items:array<PostDto>, items[].id:string, items[].authorUserId:string, items[].author:PublicProfileDto, items[].author.id:string, items[].author.nickname:string, items[].author.gender:enum(unknown|male|female|other), items[].author.avatarId:string, items[].author.interests:array<string>, items[].content:string, items[].imageUrl:string, items[].audience:enum(public|friends|only_me), items[].likeCount:number, items[].commentCount:number, items[].createdAt:string(date-time), nextCursor:string
- FE callers: web apps/web/src/features/feed/api.ts:51

### GET /feed/posts/{postId}

Chi tiết 1 bài qua getPostOrThrow: đã xoá, bị block, tác giả ẩn hồ sơ hay sai audience đều 404 POST_NOT_FOUND (chống oracle).

- handler: FeedController.getPost (apps/core-api/src/modules/feed/feed.controller.ts:115); operationId: FeedController_getPost
- auth: Bearer JWT
- params: path:postId*:string
- response 200 PostDto: id:string, authorUserId:string, author:PublicProfileDto, author.id:string, author.nickname:string, author.gender:enum(unknown|male|female|other), author.avatarId:string, author.interests:array<string>, content:string, imageUrl:string, audience:enum(public|friends|only_me), likeCount:number, commentCount:number, createdAt:string(date-time)
- FE callers: web apps/web/src/features/feed/api.ts:112

### DELETE /feed/posts/{postId}

Xoá mềm bài (deletedAt), chỉ tác giả; đi qua getPostOrThrow nên cũng áp block/audience.

- handler: FeedController.deletePost (apps/core-api/src/modules/feed/feed.controller.ts:128); operationId: FeedController_deletePost
- auth: Bearer JWT
- params: path:postId*:string
- response 204: 
- FE callers: web apps/web/src/features/feed/api.ts:86

### POST /feed/posts/{postId}/comments

Bình luận: guest bị chặn, guard block/audience, insert Comment + tăng commentCount + tạo Notification trong cùng transaction, sendPush sau commit.

- handler: FeedController.createComment (apps/core-api/src/modules/feed/feed.controller.ts:138); operationId: FeedController_createComment
- auth: Bearer JWT
- params: path:postId*:string
- body CreateCommentDto: content*:string
- response 201 CommentDto: id:string, postId:string, authorUserId:string, author:PublicProfileDto, author.id:string, author.nickname:string, author.gender:enum(unknown|male|female|other), author.avatarId:string, author.interests:array<string>, content:string, createdAt:string(date-time)
- FE callers: web apps/web/src/features/feed/api.ts:141

### GET /feed/posts/{postId}/comments

Liệt kê comment cũ -> mới theo cursor seq sau khi qua getPostOrThrow, kèm batch author PublicProfileDto.

- handler: FeedController.listComments (apps/core-api/src/modules/feed/feed.controller.ts:152); operationId: FeedController_listComments
- auth: Bearer JWT
- params: path:postId*:string, query:limit:number, query:cursor:string
- response 200 CommentsPageDto: items:array<CommentDto>, items[].id:string, items[].postId:string, items[].authorUserId:string, items[].author:PublicProfileDto, items[].author.id:string, items[].author.nickname:string, items[].author.gender:enum(unknown|male|female|other), items[].author.avatarId:string, items[].author.interests:array<string>, items[].content:string, items[].createdAt:string(date-time), nextCursor:string
- FE callers: web apps/web/src/features/feed/api.ts:124

### DELETE /feed/comments/{commentId}

Xoá mềm comment, chỉ tác giả comment; chỉ request chuyển deletedAt NULL -> timestamp mới giảm commentCount (chống trừ đôi khi race).

- handler: FeedController.deleteComment (apps/core-api/src/modules/feed/feed.controller.ts:165); operationId: FeedController_deleteComment
- auth: Bearer JWT
- params: path:commentId*:string
- response 204: 
- FE callers: none in this repo (mobile/third party or dead)

### POST /feed/posts/{postId}/reactions

Thả tim idempotent: insert Reaction + tăng likeCount + tạo Notification post_liked cùng transaction; thua race unique thì trả replay, không đếm đôi; guest bị chặn.

- handler: FeedController.like (apps/core-api/src/modules/feed/feed.controller.ts:175); operationId: FeedController_like
- auth: Bearer JWT
- params: path:postId*:string
- response 200 ReactionStatusDto: liked:boolean, likeCount:number
- FE callers: web apps/web/src/features/feed/api.ts:176

### DELETE /feed/posts/{postId}/reactions

Bỏ thả tim idempotent: xoá Reaction và chỉ giảm likeCount khi thực sự xoá được dòng.

- handler: FeedController.unlike (apps/core-api/src/modules/feed/feed.controller.ts:186); operationId: FeedController_unlike
- auth: Bearer JWT
- params: path:postId*:string
- response 200 ReactionStatusDto: liked:boolean, likeCount:number
- FE callers: web apps/web/src/features/feed/api.ts:179

### GET /feed/posts/{postId}/reactions

Trạng thái thả tim của chính mình + tổng likeCount, qua getPostOrThrow.

- handler: FeedController.reactionStatus (apps/core-api/src/modules/feed/feed.controller.ts:197); operationId: FeedController_reactionStatus
- auth: Bearer JWT
- params: path:postId*:string
- response 200 ReactionStatusDto: liked:boolean, likeCount:number
- FE callers: web apps/web/src/features/feed/api.ts:163

