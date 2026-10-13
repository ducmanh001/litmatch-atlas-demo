# web route /video

id: fe:route:web:/video
file: apps/web/src/app/(app)/video/page.tsx
layout: app (login required: AppChromeWithAuth)
title: Video
reached source files: 24 (static import closure); data features: party-room, short-video

Access: every route in the (app) group renders inside AppChromeWithAuth (AuthGate): no session -> redirected to /login. Guests can log in with a device id but several actions are limited (see "Guest and capability gating").

## UI action map card (human-authored, not verified against code)

VIDEO /video: Short video feed

Playback actions:
  - For You / Following
  - Play / pause / mute
  - Next / previous reel
  - Mở profile tác giả
Engagement actions:
  - Like / Unlike
  - Comment
  - Tặng quà
  - Voice Match → Matching

## Data it reads (GET)

| endpoint | hook | query key | polling | stale time | module | defined at |
|---|---|---|---|---|---|---|
| GET /videos | useVideoFeed | shortVideoKeys.list(sort, scope) |  |  | [short-video](../api/short-video.md) | apps/web/src/features/short-video/api.ts:34 |
| GET /videos/{id}/comments | useVideoComments | shortVideoKeys.comments(videoId) |  |  | [short-video](../api/short-video.md) | apps/web/src/features/short-video/api.ts:74 |
| GET /gifts | useGiftCatalog | ['gifts', 'catalog'] as const |  |  | [gift](../api/gift.md) | apps/web/src/features/party-room/api.ts:287 |

## Actions that change data (mutations)

| UI element and label | handler | hook | endpoint | module | call site |
|---|---|---|---|---|---|
| (no matching UI element found; called from VideoCommentComposer) |  | useCreateVideoComment | POST /videos/{id}/comments | [short-video](../api/short-video.md) | apps/web/src/features/short-video/components/video-comments-sheet.tsx:54 |
| button.onClick: (no literal label) | () => sendGift.mutate( { giftId: gift.id, idempotencyKey: key }, { onSuccess: () => { resetKey(); showToast( `Đã tặng ${ | useSendVideoGift | POST /videos/{videoId}/gifts | [gift](../api/gift.md) | apps/web/src/features/short-video/components/video-gift-sheet.tsx:65 |
| button.onClick: (no literal label) | onClick | mutation |  | ? | apps/web/src/features/short-video/components/video-like-button.tsx:67 |
| (no matching UI element found; called from VideoSlide) |  | useRecordVideoView | POST /videos/{id}/views | [short-video](../api/short-video.md) | apps/web/src/features/short-video/components/video-slide.tsx:118 |

Other write endpoints reachable (called outside a detected .mutate): POST /videos/{id}/reactions (useSetVideoReaction apps/web/src/features/short-video/api.ts:112); DELETE /videos/{id}/reactions (useSetVideoReaction apps/web/src/features/short-video/api.ts:115)

## Interactive elements (11)

- button.onClick (no literal label) -> () => onScopeChange(value) (apps/web/src/features/short-video/components/video-reel-feed.tsx:35)
- button.onClick (no literal label) -> onOpenComments (apps/web/src/features/short-video/components/video-reel-feed.tsx:141)
- button.onClick Tặng quà cho tác giả -> onOpenGifts (apps/web/src/features/short-video/components/video-reel-feed.tsx:150)
- button.onClick soundMuted             ? 'Bật âm thanh gốc của video'             : ' -> onToggleSound (apps/web/src/features/short-video/components/video-reel-feed.tsx:160)
- form.onSubmit (no literal label) -> onSubmit (apps/web/src/features/short-video/components/video-comments-sheet.tsx:63)
- button.onClick Đóng bình luận -> onClose (apps/web/src/features/short-video/components/video-comments-sheet.tsx:134)
- button.onClick Đóng -> onClose (apps/web/src/features/short-video/components/video-comments-sheet.tsx:161)
- button.onClick Đóng bảng tặng quà -> onClose (apps/web/src/features/short-video/components/video-gift-sheet.tsx:32)
- button.onClick (no literal label) -> () => sendGift.mutate( { giftId: gift.id, idempotencyKey: key }, { on… (apps/web/src/features/short-video/components/video-gift-sheet.tsx:59)
- button.onClick (no literal label) -> onClick (apps/web/src/features/short-video/components/video-like-button.tsx:80)
- button.onClick muted ? 'Bật âm thanh' : 'Tắt âm thanh' -> () => onMutedChange(!muted) (apps/web/src/features/short-video/components/video-slide.tsx:160)

## Realtime events listened

none

## Navigation destinations

- /users/${video.authorUserId via link (apps/web/src/features/short-video/components/video-reel-feed.tsx:125)
- /users/${author.id via link (apps/web/src/shared/ui/content-comment-list.tsx:95)
- /matching via link (apps/web/src/features/short-video/components/video-slide.tsx:201)

## Error codes handled in the FE

none referenced by code string (errors are shown through generic ApiError message handling)

## Guest and capability gating

- no isGuest check
- no capability check

## Form validation (zod)

- createVideoCommentSchema (apps/web/src/features/short-video/create-video-comment-schema.ts:5): content=z.string().trim().min(1, 'Nhập nội dung bình luận')

## Browser storage and cookies

- cookie-write  (apps/web/src/shared/i18n/locale-store.ts:37)

## i18n keys used (315)

- analytics.accept: Đồng ý
- analytics.consentDescription: Litmatch dùng phân tích thao tác và bản phát lại đã che toàn bộ nội dung chữ/input để tìm lỗi và cải thiện sản phẩm. Bạn có thể từ chối mà vẫn dùng đầy đủ tính năng.
- analytics.consentTitle: Cho phép phân tích trải nghiệm?
- analytics.decline: Từ chối
- analytics.settingsSection: Cải thiện trải nghiệm
- app.back: Quay lại
- app.conversation: Trò chuyện
- app.conversationError: Không tải được trò chuyện.
- app.conversationLoading: Đang tải trò chuyện…
- app.feed: Bảng tin
- app.matching: Ghép đôi
- app.messages: Tin nhắn
- app.more: Thêm
- app.partyEyebrow: Trò chuyện nhóm
- app.post: Bài viết
- app.profile: Hồ sơ
- app.publicProfile: Hồ sơ người dùng
- app.room: Phòng nhóm
- app.videoShort: Video ngắn
- app.wallet: Ví & VIP
- app.walletEyebrow: Số dư & gói VIP
- app.watchTogether: Đang xem chung
- auth.changePhone: ← Đổi số điện thoại
- auth.checkingProviders: Đang kiểm tra phương thức đăng nhập…
- auth.checkingSession: Đang kiểm tra phiên đăng nhập…
- … 290 more, see i18n-*.md

## Tests

- apps/web/src/features/party-room/api.spec.ts
- apps/web/src/features/party-room/components/member-list.spec.tsx
- apps/web/src/features/party-room/components/party-chat.spec.tsx
- apps/web/src/features/party-room/components/party-stage.spec.tsx
- apps/web/src/features/party-room/components/room-list.spec.tsx
- apps/web/src/features/party-room/create-room-schema.spec.ts
- apps/web/src/features/party-room/hooks/use-party-room-media.spec.tsx
- apps/web/src/features/short-video/components/video-comments-sheet.spec.tsx
- apps/web/src/features/short-video/components/video-like-button.spec.tsx
- apps/web/src/features/short-video/components/video-reel-feed.spec.tsx
- apps/web/src/features/short-video/create-video-comment-schema.spec.ts

## Backend

modules: [short-video](../modules/short-video.md), [gift](../modules/gift.md)

service-map flows touching these endpoints: [short-video-browse-and-detail](../flows/short-video-browse-and-detail.md), [short-video-comments](../flows/short-video-comments.md), [admin-gift-catalog](../flows/admin-gift-catalog.md), [gift-on-video](../flows/gift-on-video.md)
