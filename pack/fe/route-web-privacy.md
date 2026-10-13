# web route /privacy

id: fe:route:web:/privacy
file: apps/web/src/app/(public)/privacy/page.tsx
layout: public
reached source files: 11 (static import closure); data features: privacy

## UI action map card (human-authored, not verified against code)

MORE /help /privacy: Settings, support, account

Navigation actions:
  - Xem/chỉnh sửa Profile
  - Mở Discovery / Video / Party / Feed
  - Premium / Wallet
  - Mời bạn bè
Settings actions:
  - Theme / Language
  - Privacy / Block / Report
  - FAQ / Gửi feedback
  - Logout → Login

## Data it reads (GET)

| endpoint | hook | query key | polling | stale time | module | defined at |
|---|---|---|---|---|---|---|
| GET /users/me/privacy | usePrivacySettings | privacyKeys.settings |  |  | [user](../api/user.md) | apps/web/src/features/privacy/api.ts:20 |
| GET /users/me | useCurrentUser | currentUserKey |  | Infinity | [user](../api/user.md) | apps/web/src/shared/auth/use-current-user.ts:20 |

## Actions that change data (mutations)

| UI element and label | handler | hook | endpoint | module | call site |
|---|---|---|---|---|---|
| (no matching UI element found; called from toggle) |  | useUpdatePrivacySettings | PUT /users/me/privacy | [user](../api/user.md) | apps/web/src/app/(public)/privacy/page.tsx:110 |

## Interactive elements (0)

none

## Realtime events listened

none

## Navigation destinations

- /help via link (apps/web/src/app/(public)/privacy/page.tsx:197)

## Error codes handled in the FE

none referenced by code string (errors are shown through generic ApiError message handling)

## Guest and capability gating

- guest checks (isGuest) in: apps/web/src/shared/analytics/product-analytics-components.tsx, apps/web/src/shared/analytics/product-analytics.ts
- no capability check

## Browser storage and cookies

- staleTime Infinity (apps/web/src/shared/auth/use-current-user.ts:25)
- cookie-write  (apps/web/src/shared/analytics/product-analytics.ts:93)
- cookie-write  (apps/web/src/shared/i18n/locale-store.ts:37)

## Analytics events

- web_vital (apps/web/src/shared/analytics/product-analytics.ts:144)

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

- apps/web/src/app/(public)/privacy/page.spec.tsx

## Backend

modules: [user](../modules/user.md)

service-map flows touching these endpoints: none
