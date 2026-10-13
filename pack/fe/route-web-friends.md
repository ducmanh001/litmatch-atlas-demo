# web route /friends

id: fe:route:web:/friends
file: apps/web/src/app/(app)/friends/page.tsx
layout: app (login required: AppChromeWithAuth)
title: Bạn bè
reached source files: 16 (static import closure); data features: friend-chat, profile

Access: every route in the (app) group renders inside AppChromeWithAuth (AuthGate): no session -> redirected to /login. Guests can log in with a device id but several actions are limited (see "Guest and capability gating").

## UI action map card (human-authored, not verified against code)

FRIENDS /friends: Danh sách bạn bè và inbox

List actions:
  - Tìm kiếm bạn bè
  - Mở friend profile
  - Mở conversation
  - Xem unread count
Conversation actions:
  - Gửi message / ảnh
  - Mark read / mute
  - Gọi voice → Call
  - Xem chung Movie

## Data it reads (GET)

| endpoint | hook | query key | polling | stale time | module | defined at |
|---|---|---|---|---|---|---|
| GET /friends/connections | useFriendConnections | friendChatKeys.connections |  |  | [friend](../api/friend.md) | apps/web/src/features/friend-chat/api.ts:49 |
| GET /friends | useFriends | friendChatKeys.friends |  |  | [friend](../api/friend.md) | apps/web/src/features/friend-chat/api.ts:35 |
| GET /users/me | useCurrentUser | currentUserKey |  | Infinity | [user](../api/user.md) | apps/web/src/shared/auth/use-current-user.ts:20 |

## Actions that change data (mutations)

| UI element and label | handler | hook | endpoint | module | call site |
|---|---|---|---|---|---|
| button.onClick: t('friendsPage.messageTo', { name: nickname }) | open | useOpenProfileConversation | POST /profiles/{profileUserId}/conversation | [friend](../api/friend.md) | apps/web/src/features/friend-chat/components/friends-directory.tsx:61 |

## Interactive elements (1)

- button.onClick t('friendsPage.messageTo', { name: nickname }) -> open (apps/web/src/features/friend-chat/components/friends-directory.tsx:76)

## Realtime events listened

none

## Navigation destinations

- /chat/{param} via router.push (apps/web/src/features/friend-chat/components/friends-directory.tsx:62)
- /users/{param} via router.push (apps/web/src/features/friend-chat/components/friends-directory.tsx:70)
- /messages via link (apps/web/src/features/friend-chat/components/friends-directory.tsx:125)
- /users/:param/followers via link (apps/web/src/features/friend-chat/components/friends-directory.tsx:133)
- /users/:param/following via link (apps/web/src/features/friend-chat/components/friends-directory.tsx:138)
- /matching via link (apps/web/src/features/friend-chat/components/friends-directory.tsx:166)
- /discovery via link (apps/web/src/features/friend-chat/components/friends-directory.tsx:172)
- /users/${friend.profile.id via link (apps/web/src/features/friend-chat/components/friends-directory.tsx:189)

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

- apps/web/src/features/friend-chat/api.spec.ts
- apps/web/src/features/friend-chat/components/conversation-inbox.spec.tsx
- apps/web/src/features/friend-chat/components/conversation-thread.spec.tsx
- apps/web/src/features/friend-chat/components/friends-directory.spec.tsx
- apps/web/src/features/friend-chat/components/message-list.spec.tsx
- apps/web/src/features/friend-chat/send-message-schema.spec.ts
- apps/web/src/features/profile/components/follow-list-view.spec.tsx
- apps/web/src/features/profile/components/profile-stats.spec.tsx
- apps/web/src/features/profile/components/profile-view.spec.tsx
- apps/web/src/features/profile/components/public-profile-view.spec.tsx
- apps/web/src/features/profile/update-profile-schema.spec.ts

## Backend

modules: [friend](../modules/friend.md), [user](../modules/user.md)

service-map flows touching these endpoints: [friend-follow-lists-and-connections](../flows/friend-follow-lists-and-connections.md), [friend-list-and-conversation-reads](../flows/friend-list-and-conversation-reads.md), [friend-open-conversation-from-profile](../flows/friend-open-conversation-from-profile.md)
