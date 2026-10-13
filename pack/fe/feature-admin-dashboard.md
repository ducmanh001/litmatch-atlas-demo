# admin feature dashboard

id: fe:feature:admin:dashboard
directory: apps/admin/src/features/dashboard/ (2 source files, 1 components, 1 spec files)

routes whose data comes from this feature: [/](route-admin-root.md)

## Hooks (data layer)

| hook | endpoint(s) | query key | polling | stale | invalidates on success | defined at |
|---|---|---|---|---|---|---|
| useAdminDashboard | GET /admin/dashboard | dashboardKeys.summary |  | 30_000 |  | apps/admin/src/features/dashboard/api.ts:13 |

## Components

- apps/admin/src/features/dashboard/pages/dashboard-page.tsx: 0 interactive elements, uses useT, useLocale, useAdminDashboard, useLiveRooms

## Tests

- apps/admin/src/features/dashboard/pages/dashboard-page.spec.tsx
