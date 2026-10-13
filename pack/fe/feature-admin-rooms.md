# admin feature rooms

id: fe:feature:admin:rooms
directory: apps/admin/src/features/rooms/ (2 source files, 1 components, 1 spec files)

routes whose data comes from this feature: [/](route-admin-root.md), [/rooms](route-admin-rooms.md)

## Hooks (data layer)

| hook | endpoint(s) | query key | polling | stale | invalidates on success | defined at |
|---|---|---|---|---|---|---|
| useLiveRooms | GET /party/rooms | roomsKeys.all |  | 5000 |  | apps/admin/src/features/rooms/api.ts:23 |
| useAdminRooms | GET /admin/rooms | roomsKeys.admin |  | 5000 |  | apps/admin/src/features/rooms/api.ts:36 |
| useCloseRoom | POST /admin/rooms/{id}/close |  |  |  | invalidateQueries({ queryKey: roomsKeys.admin }); invalidateQueries({ queryKey: roomsKeys.all }) | apps/admin/src/features/rooms/api.ts:49 |

## Components

- apps/admin/src/features/rooms/pages/rooms-page.tsx: 1 interactive elements, uses useT, useAdminRooms, useCloseRoom

## Tests

- apps/admin/src/features/rooms/pages/rooms-page.spec.tsx
