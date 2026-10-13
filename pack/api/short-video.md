# REST API of short-video

id: api:short-video
module: module:short-video
prefix: /api/v1; success body {data, meta?}; errors are DomainException codes (see ../errors/)
see: ../modules/short-video.md

### POST /videos/upload-intent

Tạo Video{uploading} + presigned uploadUrl qua VideoStoragePort; bắt buộc Idempotency-Key (replay phát lại ĐÚNG storageKey, khác caption 409); bị chặn 403 khi VIDEO_UPLOAD_ENABLED=false.

- handler: ShortVideoController.createUploadIntent (apps/core-api/src/modules/short-video/short-video.controller.ts:55); operationId: ShortVideoController_createUploadIntent
- auth: Bearer JWT; requires Idempotency-Key header
- params: header:Idempotency-Key*:string
- body CreateUploadIntentDto: caption:string
- response 201 UploadIntentDto: videoId:string, uploadUrl:string
- FE callers: none in this repo (mobile/third party or dead)

### POST /videos/{id}/finalize

Client báo upload xong: conditional UPDATE uploading -> processing, gọi VideoTranscodePort.transcode rồi sang pending_review|published theo VIDEO_MODERATION_MODE; lỗi transcode -> failed. Chỉ tác giả.

- handler: ShortVideoController.finalizeUpload (apps/core-api/src/modules/short-video/short-video.controller.ts:75); operationId: ShortVideoController_finalizeUpload
- auth: Bearer JWT
- params: path:id*:string
- response 200 VideoDto: id:string, authorUserId:string, author:PublicProfileDto, author.id:string, author.nickname:string, author.gender:enum(unknown|male|female|other), author.avatarId:string, author.interests:array<string>, status:enum(uploading|processing|pending_review|published|removed|rejected|failed), playbackUrl:string, thumbnailUrl:string, caption:string, durationSeconds:number, viewCount:number, likeCount:number, commentCount:number, createdAt:string(date-time)
- FE callers: none in this repo (mobile/third party or dead)

### GET /videos

Danh sách video published (for_you hoặc following = video của bạn bè qua listFriendIds; sort recent|ranked) kèm author PublicProfileDto batch; bạn chưa có ai -> trang rỗng.

- handler: ShortVideoController.list (apps/core-api/src/modules/short-video/short-video.controller.ts:89); operationId: ShortVideoController_list
- auth: Bearer JWT
- params: query:sort:enum(recent|ranked), query:feed:enum(for_you|following), query:limit:number, query:cursor:string
- response 200 VideosPageDto: items:array<VideoDto>, items[].id:string, items[].authorUserId:string, items[].author:PublicProfileDto, items[].author.id:string, items[].author.nickname:string, items[].author.gender:enum(unknown|male|female|other), items[].author.avatarId:string, items[].author.interests:array<string>, items[].status:enum(uploading|processing|pending_review|published|removed|rejected|failed), items[].playbackUrl:string, items[].thumbnailUrl:string, items[].caption:string, items[].durationSeconds:number, items[].viewCount:number, items[].likeCount:number, items[].commentCount:number, items[].createdAt:string(date-time), nextCursor:string
- FE callers: none in this repo (mobile/third party or dead)

### GET /videos/{id}

Chi tiết 1 video qua getVideoOrThrow: chỉ tác giả hoặc video published, còn lại 404 VIDEO_NOT_FOUND.

- handler: ShortVideoController.getVideo (apps/core-api/src/modules/short-video/short-video.controller.ts:104); operationId: ShortVideoController_getVideo
- auth: Bearer JWT
- params: path:id*:string
- response 200 VideoDto: id:string, authorUserId:string, author:PublicProfileDto, author.id:string, author.nickname:string, author.gender:enum(unknown|male|female|other), author.avatarId:string, author.interests:array<string>, status:enum(uploading|processing|pending_review|published|removed|rejected|failed), playbackUrl:string, thumbnailUrl:string, caption:string, durationSeconds:number, viewCount:number, likeCount:number, commentCount:number, createdAt:string(date-time)
- FE callers: none in this repo (mobile/third party or dead)

### POST /videos/{id}/report

Report video: kiểm video visible rồi SafetyService.reportVideo (unique 1 report/người/video); vượt VIDEO_REPORT_AUTOHIDE_THRESHOLD distinct reporter thì tự published -> removed.

- handler: ShortVideoController.reportVideo (apps/core-api/src/modules/short-video/short-video.controller.ts:118); operationId: ShortVideoController_reportVideo
- auth: Bearer JWT
- params: path:id*:string
- body ReportVideoDto: reason*:enum(harassment|spam|underage|inappropriate_content|other), description:string
- response 204: 
- FE callers: none in this repo (mobile/third party or dead)

### POST /videos/{id}/views

Ghi watch-time: self-view bỏ qua, video_views unique, viewCount chỉ +1 đúng lúc qualified chuyển false->true (cùng transaction).

- handler: ShortVideoController.recordView (apps/core-api/src/modules/short-video/short-video.controller.ts:132); operationId: ShortVideoController_recordView
- auth: Bearer JWT
- params: path:id*:string
- body RecordViewDto: watchTimeMs*:number[minimum=0]
- response 204: 
- FE callers: none in this repo (mobile/third party or dead)

### POST /videos/{id}/reactions

Thả tim idempotent: insert VideoReaction + tăng likeCount cùng transaction, thua race unique thì replay không đếm đôi.

- handler: ShortVideoController.like (apps/core-api/src/modules/short-video/short-video.controller.ts:143); operationId: ShortVideoController_like
- auth: Bearer JWT
- params: path:id*:string
- response 201 ReactionStatusDto: liked:boolean, likeCount:number
- FE callers: none in this repo (mobile/third party or dead)

### DELETE /videos/{id}/reactions

Bỏ tim idempotent: xoá VideoReaction và chỉ giảm likeCount khi xoá được dòng.

- handler: ShortVideoController.unlike (apps/core-api/src/modules/short-video/short-video.controller.ts:153); operationId: ShortVideoController_unlike
- auth: Bearer JWT
- params: path:id*:string
- response 200 ReactionStatusDto: liked:boolean, likeCount:number
- FE callers: none in this repo (mobile/third party or dead)

### POST /videos/{id}/comments

Bình luận video: insert VideoComment + tăng commentCount cùng transaction, kèm author PublicProfileDto.

- handler: ShortVideoController.createComment (apps/core-api/src/modules/short-video/short-video.controller.ts:163); operationId: ShortVideoController_createComment
- auth: Bearer JWT
- params: path:id*:string
- body CreateVideoCommentDto: content*:string
- response 201 VideoCommentDto: id:string, videoId:string, authorUserId:string, author:PublicProfileDto, author.id:string, author.nickname:string, author.gender:enum(unknown|male|female|other), author.avatarId:string, author.interests:array<string>, content:string, createdAt:string(date-time)
- FE callers: none in this repo (mobile/third party or dead)

### GET /videos/{id}/comments

Liệt kê comment theo cursor sau khi qua getVideoOrThrow, batch author PublicProfileDto.

- handler: ShortVideoController.listComments (apps/core-api/src/modules/short-video/short-video.controller.ts:182); operationId: ShortVideoController_listComments
- auth: Bearer JWT
- params: path:id*:string, query:limit:number, query:cursor:string
- response 200 VideoCommentsPageDto: items:array<VideoCommentDto>, items[].id:string, items[].videoId:string, items[].authorUserId:string, items[].author:PublicProfileDto, items[].author.id:string, items[].author.nickname:string, items[].author.gender:enum(unknown|male|female|other), items[].author.avatarId:string, items[].author.interests:array<string>, items[].content:string, items[].createdAt:string(date-time), nextCursor:string
- FE callers: none in this repo (mobile/third party or dead)

### DELETE /videos/{id}/comments/{commentId}

Xoá mềm bình luận (set deletedAt): chỉ tác giả comment hoặc tác giả video được xoá, người khác 403, comment đã xoá 404; commentCount giảm 1 vô điều kiện trong cùng transaction.

- handler: ShortVideoController.deleteComment (apps/core-api/src/modules/short-video/short-video.controller.ts:195); operationId: ShortVideoController_deleteComment
- auth: Bearer JWT
- params: path:id*:string, path:commentId*:string
- response 204: 
- FE callers: none in this repo (mobile/third party or dead)

