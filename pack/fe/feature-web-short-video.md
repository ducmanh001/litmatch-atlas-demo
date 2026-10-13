# web feature short-video

id: fe:feature:web:short-video
directory: apps/web/src/features/short-video/ (7 source files, 5 components, 4 spec files)

routes whose data comes from this feature: [/video](route-web-video.md)

## Hooks (data layer)

| hook | endpoint(s) | query key | polling | stale | invalidates on success | defined at |
|---|---|---|---|---|---|---|
| useVideoFeed | GET /videos | shortVideoKeys.list(sort, scope) |  |  |  | apps/web/src/features/short-video/api.ts:30 |
| useSendVideoGift | POST /videos/{videoId}/gifts |  |  |  |  | apps/web/src/features/short-video/api.ts:52 |
| useVideoComments | GET /videos/{id}/comments | shortVideoKeys.comments(videoId) |  |  |  | apps/web/src/features/short-video/api.ts:67 |
| useCreateVideoComment | POST /videos/{id}/comments |  |  |  | invalidateQueries({ queryKey: shortVideoKeys.comments(videoId), }); invalidateQueries({ queryKey: shortVideoKeys.listAll }) | apps/web/src/features/short-video/api.ts:88 |
| useSetVideoReaction | POST /videos/{id}/reactions, DELETE /videos/{id}/reactions |  |  |  | invalidateQueries({ queryKey: shortVideoKeys.listAll }) | apps/web/src/features/short-video/api.ts:107 |
| useLikeVideo | (none directly) |  |  |  |  | apps/web/src/features/short-video/api.ts:126 |
| useUnlikeVideo | (none directly) |  |  |  |  | apps/web/src/features/short-video/api.ts:130 |
| useRecordVideoView | POST /videos/{id}/views |  |  |  |  | apps/web/src/features/short-video/api.ts:134 |
| useReportVideo | POST /videos/{id}/report |  |  |  |  | apps/web/src/features/short-video/api.ts:145 |

## Components

- apps/web/src/features/short-video/components/video-comments-sheet.tsx: 3 interactive elements, uses useVideoComments, useForm, useCreateVideoComment, useId, useRef, useEffect
- apps/web/src/features/short-video/components/video-gift-sheet.tsx: 2 interactive elements, uses useGiftCatalog, useSendVideoGift, useIdempotencyKey
- apps/web/src/features/short-video/components/video-like-button.tsx: 1 interactive elements, uses useState, useLikeVideo, useUnlikeVideo
- apps/web/src/features/short-video/components/video-reel-feed.tsx: 4 interactive elements, uses useState, useVideoFeed, useRef, useEffect
- apps/web/src/features/short-video/components/video-slide.tsx: 1 interactive elements, uses useRef, useLocale, useRecordVideoView, useEffect

## Validation schemas

- createVideoCommentSchema (apps/web/src/features/short-video/create-video-comment-schema.ts:5): content=z.string().trim().min(1, 'Nhập nội dung bình luận')

## Tests

- apps/web/src/features/short-video/components/video-comments-sheet.spec.tsx
- apps/web/src/features/short-video/components/video-like-button.spec.tsx
- apps/web/src/features/short-video/components/video-reel-feed.spec.tsx
- apps/web/src/features/short-video/create-video-comment-schema.spec.ts
