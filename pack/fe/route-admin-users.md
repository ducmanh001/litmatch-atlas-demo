# admin route /users

id: fe:route:admin:/users
file: apps/admin/src/features/users/pages/users-page.tsx:38
layout: admin (RequireAuth + role)
reached source files: 18 (static import closure); data features: users

## UI action map card (human-authored, not verified against code)

USERS /users: Quản lý tài khoản người dùng

List actions:
  - Tìm theo nickname / status
  - Lọc / phân trang
  - Mở hồ sơ chi tiết
  - Xem role / guest / status
Account actions:
  - Ban / Unban → user app
  - Ghi audit action

## Data it reads (GET)

| endpoint | hook | query key | polling | stale time | module | defined at |
|---|---|---|---|---|---|---|
| GET /admin/users | useUsersList | usersKeys.list(filter, offset) |  | 5000 | [admin](../api/admin.md) | apps/admin/src/features/users/api.ts:27 |

## Actions that change data (mutations)

| UI element and label | handler | hook | endpoint | module | call site |
|---|---|---|---|---|---|
| (no matching UI element found; called from ban) |  | useBanUser | POST /admin/users/{id}/ban | [admin](../api/admin.md) | apps/admin/src/features/users/pages/users-page.tsx:74 |
| (no matching UI element found; called from unban) |  | useUnbanUser | POST /admin/users/{id}/unban | [admin](../api/admin.md) | apps/admin/src/features/users/pages/users-page.tsx:81 |

## Interactive elements (7)

- button.onClick (no literal label) -> () => setProfileUser(user) (apps/admin/src/features/users/pages/users-page.tsx:156)
- Button.onClick users.unban (users.unban) -> () => unban(user) (apps/admin/src/features/users/pages/users-page.tsx:201)
- Button.onClick users.ban (users.ban) -> () => ban(user) (apps/admin/src/features/users/pages/users-page.tsx:210)
- Button.onClick users.previous (users.previous) -> () => setOffset(Math.max(0, offset - PAGE_SIZE)) (apps/admin/src/features/users/pages/users-page.tsx:235)
- Button.onClick users.next (users.next) -> () => setOffset(offset + PAGE_SIZE) (apps/admin/src/features/users/pages/users-page.tsx:246)
- Button.onClick users.banAccount (users.banAccount) -> () => { ban(profileUser); setProfileUser(null); } (apps/admin/src/features/users/pages/users-page.tsx:318)
- Button.onClick users.unban (users.unban) -> () => { unban(profileUser); setProfileUser(null); } (apps/admin/src/features/users/pages/users-page.tsx:331)

## Realtime events listened

none

## Navigation destinations

none

## Error codes handled in the FE

none referenced by code string (errors are shown through generic ApiError message handling)

## Guest and capability gating

- guest checks (isGuest) in: apps/admin/src/features/users/pages/users-page.tsx
- no capability check

## Form validation (zod)

- envSchema (apps/admin/src/shared/env.ts:8): VITE_API_URL=z.url(); VITE_AUTH_GOOGLE_CLIENT_ID=z.string().min(1).optional(); VITE_SENTRY_DSN=z.url().optional(); VITE_SENTRY_ENVIRONMENT=z.string().min(1).default('production'); VITE_SENTRY_RELEASE=z.string().max(200).optional(); VITE_PHONE_OTP_ENABLED=z .enum(['true', 'false']) .default('true') .transform((value) => value === 'tr…

## Browser storage and cookies

- staleTime 5000 (apps/admin/src/features/users/api.ts:41)
- cookie-write  (apps/admin/src/shared/i18n/locale-store.ts:28)

## i18n keys used (32)

- auth.changePhone: ← Đổi số điện thoại
- auth.guestAvailable: Có thể dùng tài khoản khách.
- common.loading: Đang tải…
- common.tryAgain: 
- common.unknownError: Sự cố không xác định. Vui lòng tải lại.
- language.english: English
- language.vietnamese: Tiếng Việt
- state.copyTraceId: 
- state.trace: 
- state.traceCopied: 
- state.traceCopyFailed: 
- users.active: 
- users.all: 
- users.ban: 
- users.banAccount: 
- users.banned: 
- users.bannedToast: 
- users.cannotBanSelf: 
- users.empty: 
- users.gender: 
- users.guest: 
- users.next: 
- users.nickname: 
- users.no: 
- users.previous: 
- … 7 more, see i18n-*.md

## Tests

- apps/admin/src/features/users/pages/users-page.spec.tsx

## Backend

modules: [admin](../modules/admin.md)

service-map flows touching these endpoints: [admin-user-lookup-and-unban](../flows/admin-user-lookup-and-unban.md), [admin-ban-and-refund](../flows/admin-ban-and-refund.md)
