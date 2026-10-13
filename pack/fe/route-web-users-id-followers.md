# web route /users/:id/followers

id: fe:route:web:/users/:id/followers
file: apps/web/src/app/(app)/users/[id]/followers/page.tsx
layout: app (login required: AppChromeWithAuth)
title: Người theo dõi
reached source files: 14 (static import closure); data features: profile

Access: every route in the (app) group renders inside AppChromeWithAuth (AuthGate): no session -> redirected to /login. Guests can log in with a device id but several actions are limited (see "Guest and capability gating").

## Data it reads (GET)

| endpoint | hook | query key | polling | stale time | module | defined at |
|---|---|---|---|---|---|---|
| GET /users/me | useCurrentUser | currentUserKey |  | Infinity | [user](../api/user.md) | apps/web/src/shared/auth/use-current-user.ts:20 |
| GET /profiles/{profileUserId}/follow-counts | useFollowCounts | profileKeys.followCounts(id) |  |  | [friend](../api/friend.md) | apps/web/src/features/profile/api.ts:38 |
| GET /profiles/{profileUserId}/followers | useFollowList | profileKeys.followList(id, kind) |  |  | [friend](../api/friend.md) | apps/web/src/features/profile/api.ts:56 |
| GET /profiles/{profileUserId}/following | useFollowList | profileKeys.followList(id, kind) |  |  | [friend](../api/friend.md) | apps/web/src/features/profile/api.ts:59 |

## Actions that change data (mutations)

| UI element and label | handler | hook | endpoint | module | call site |
|---|---|---|---|---|---|
| button.onClick: (no literal label) | toggle | useFollowProfile | POST /profiles/{profileUserId}/follow, DELETE /profiles/{profileUserId}/follow | [friend](../api/friend.md) | apps/web/src/features/profile/components/follow-list-view.tsx:37 |

## Interactive elements (2)

- button.onClick (no literal label) -> toggle (apps/web/src/features/profile/components/follow-list-view.tsx:54)
- button.onClick Đang tải thêm… (follow.loadingMore) -> () => void list.fetchNextPage() (apps/web/src/features/profile/components/follow-list-view.tsx:197)

## Realtime events listened

none

## Navigation destinations

- /users/:param/${tab.kind via link (apps/web/src/features/profile/components/follow-list-view.tsx:117)

## Error codes handled in the FE

none referenced by code string (errors are shown through generic ApiError message handling)

## Guest and capability gating

- no isGuest check
- no capability check

## Browser storage and cookies

- staleTime Infinity (apps/web/src/shared/auth/use-current-user.ts:25)
- staleTime 30_000 (apps/web/src/features/profile/api.ts:182)
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

- apps/web/src/features/profile/components/follow-list-view.spec.tsx
- apps/web/src/features/profile/components/profile-stats.spec.tsx
- apps/web/src/features/profile/components/profile-view.spec.tsx
- apps/web/src/features/profile/components/public-profile-view.spec.tsx
- apps/web/src/features/profile/update-profile-schema.spec.ts

## Backend

modules: [user](../modules/user.md), [friend](../modules/friend.md)

service-map flows touching these endpoints: [friend-follow-lists-and-connections](../flows/friend-follow-lists-and-connections.md), [friend-profile-follow-and-actions](../flows/friend-profile-follow-and-actions.md)
