# admin route /

id: fe:route:admin:/
file: apps/admin/src/features/dashboard/pages/dashboard-page.tsx:30
layout: admin (RequireAuth + role)
reached source files: 15 (static import closure); data features: dashboard, rooms

## UI action map card (human-authored, not verified against code)

DASHBOARD /: Admin app — tổng quan vận hành

View actions:
  - New users / active users
  - Diamond spent / user tiers
  - Live rooms / top rooms
  - Daily chart / recent activities
Navigate:
  - Mở Users / Moderation / Rooms
  - Theo dõi audit activity

## Data it reads (GET)

| endpoint | hook | query key | polling | stale time | module | defined at |
|---|---|---|---|---|---|---|
| GET /admin/dashboard | useAdminDashboard | dashboardKeys.summary |  | 30_000 | [admin](../api/admin.md) | apps/admin/src/features/dashboard/api.ts:17 |
| GET /party/rooms | useLiveRooms | roomsKeys.all |  | 5000 | [party-room](../api/party-room.md) | apps/admin/src/features/rooms/api.ts:27 |

## Actions that change data (mutations)

none found

## Interactive elements (0)

none

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
- staleTime 30_000 (apps/admin/src/features/dashboard/api.ts:20)
- cookie-write  (apps/admin/src/shared/i18n/locale-store.ts:28)

## i18n keys used (32)

- auth.changePhone: ← Đổi số điện thoại
- auth.guestAvailable: Có thể dùng tài khoản khách.
- common.live: 
- common.loading: Đang tải…
- common.unknownError: Sự cố không xác định. Vui lòng tải lại.
- dashboard.activePlans: 
- dashboard.activeUsers: 
- dashboard.activeUsersHint: 
- dashboard.activities: 
- dashboard.dailyDiamond: 
- dashboard.diamondSpent: 
- dashboard.empty: 
- dashboard.free: 
- dashboard.ledgerHint: 
- dashboard.liveRooms: 
- dashboard.loading: 
- dashboard.newUsers: 
- dashboard.noActivities: 
- dashboard.noRooms: 
- dashboard.recordedDiamond: 
- dashboard.roomsCount: 
- dashboard.roomsHint: 
- dashboard.totalSevenDays: 
- dashboard.userComposition: 
- dashboard.users: 
- … 7 more, see i18n-*.md

## Tests

- apps/admin/src/features/dashboard/pages/dashboard-page.spec.tsx
- apps/admin/src/features/rooms/pages/rooms-page.spec.tsx

## Backend

modules: [admin](../modules/admin.md), [party-room](../modules/party-room.md)

service-map flows touching these endpoints: [admin-dashboard](../flows/admin-dashboard.md), [party-room-detail-and-comments](../flows/party-room-detail-and-comments.md)
