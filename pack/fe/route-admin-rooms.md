# admin route /rooms

id: fe:route:admin:/rooms
file: apps/admin/src/features/rooms/pages/rooms-page.tsx:70
layout: admin (RequireAuth + role)
reached source files: 12 (static import closure); data features: rooms

## UI action map card (human-authored, not verified against code)

ROOMS /rooms: Giám sát Party Room live

Room actions:
  - Xem live room / host / title
  - Xem member / trạng thái
  - Đóng room → Party app
  - Dashboard xem top rooms
Operational effect:
  - Room biến mất khỏi danh sách live
  - Ghi admin activity

## Data it reads (GET)

| endpoint | hook | query key | polling | stale time | module | defined at |
|---|---|---|---|---|---|---|
| GET /admin/rooms | useAdminRooms | roomsKeys.admin |  | 5000 | [admin](../api/admin.md) | apps/admin/src/features/rooms/api.ts:40 |

## Actions that change data (mutations)

| UI element and label | handler | hook | endpoint | module | call site |
|---|---|---|---|---|---|
| (no matching UI element found; called from RoomsPage) |  | useCloseRoom | POST /admin/rooms/{id}/close | [admin](../api/admin.md) | apps/admin/src/features/rooms/pages/rooms-page.tsx:53 |

## Interactive elements (1)

- Button.onClick rooms.close (rooms.close) -> onClose (apps/admin/src/features/rooms/pages/rooms-page.tsx:109)

## Realtime events listened

none

## Navigation destinations

none

## Error codes handled in the FE

none referenced by code string (errors are shown through generic ApiError message handling)

## Guest and capability gating

- no isGuest check
- no capability check

## Form validation (zod)

- envSchema (apps/admin/src/shared/env.ts:8): VITE_API_URL=z.url(); VITE_AUTH_GOOGLE_CLIENT_ID=z.string().min(1).optional(); VITE_SENTRY_DSN=z.url().optional(); VITE_SENTRY_ENVIRONMENT=z.string().min(1).default('production'); VITE_SENTRY_RELEASE=z.string().max(200).optional(); VITE_PHONE_OTP_ENABLED=z .enum(['true', 'false']) .default('true') .transform((value) => value === 'tr…

## Browser storage and cookies

- staleTime 5000 (apps/admin/src/features/rooms/api.ts:32)
- staleTime 5000 (apps/admin/src/features/rooms/api.ts:45)
- cookie-write  (apps/admin/src/shared/i18n/locale-store.ts:28)

## i18n keys used (23)

- auth.changePhone: ← Đổi số điện thoại
- auth.guestAvailable: Có thể dùng tài khoản khách.
- common.live: 
- common.loading: Đang tải…
- common.unknownError: Sự cố không xác định. Vui lòng tải lại.
- dashboard.noRooms: 
- language.english: English
- language.vietnamese: Tiếng Việt
- rooms.alreadyClosed: 
- rooms.close: 
- rooms.closed: 
- rooms.confirmClose: 
- rooms.hint: 
- rooms.host: 
- rooms.hoursMinutes: 
- rooms.liveFor: 
- rooms.members: 
- rooms.minutes: 
- rooms.speakerLimit: 
- state.copyTraceId: 
- state.trace: 
- state.traceCopied: 
- state.traceCopyFailed: 

## Tests

- apps/admin/src/features/rooms/pages/rooms-page.spec.tsx

## Backend

modules: [admin](../modules/admin.md)

service-map flows touching these endpoints: [admin-room-management](../flows/admin-room-management.md)
