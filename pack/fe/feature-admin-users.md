# admin feature users

id: fe:feature:admin:users
directory: apps/admin/src/features/users/ (2 source files, 1 components, 1 spec files)

routes whose data comes from this feature: [/users](route-admin-users.md)

## Hooks (data layer)

| hook | endpoint(s) | query key | polling | stale | invalidates on success | defined at |
|---|---|---|---|---|---|---|
| useUsersList | GET /admin/users | usersKeys.list(filter, offset) |  | 5000 |  | apps/admin/src/features/users/api.ts:23 |
| useBanUser | POST /admin/users/{id}/ban |  |  |  | invalidateQueries({ queryKey: usersKeys.all }) | apps/admin/src/features/users/api.ts:45 |
| useUnbanUser | POST /admin/users/{id}/unban |  |  |  | invalidateQueries({ queryKey: usersKeys.all }) | apps/admin/src/features/users/api.ts:58 |

## Components

- apps/admin/src/features/users/pages/users-page.tsx: 7 interactive elements, uses useT, useCurrentUserId, useState, useUsersList, useBanUser, useUnbanUser, checks isGuest

## Tests

- apps/admin/src/features/users/pages/users-page.spec.tsx
