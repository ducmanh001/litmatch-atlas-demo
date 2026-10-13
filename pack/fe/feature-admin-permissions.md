# admin feature permissions

id: fe:feature:admin:permissions
directory: apps/admin/src/features/permissions/ (2 source files, 1 components, 1 spec files)

routes whose data comes from this feature: [/permissions](route-admin-permissions.md)

## Hooks (data layer)

| hook | endpoint(s) | query key | polling | stale | invalidates on success | defined at |
|---|---|---|---|---|---|---|
| usePermissionMatrix | GET /admin/permissions | permissionKeys.matrix |  |  |  | apps/admin/src/features/permissions/api.ts:16 |
| useSetRolePermission | PATCH /admin/permissions/{role}/{permission} |  |  |  | invalidateQueries({ queryKey: permissionKeys.matrix }) | apps/admin/src/features/permissions/api.ts:26 |
| useStaff | GET /admin/staff | permissionKeys.staff |  |  |  | apps/admin/src/features/permissions/api.ts:46 |
| useSetStaffRole | PATCH /admin/staff/{id}/role |  |  |  | invalidateQueries({ queryKey: permissionKeys.staff }) | apps/admin/src/features/permissions/api.ts:56 |

## Components

- apps/admin/src/features/permissions/pages/permissions-page.tsx: 1 interactive elements, uses useT, useCurrentUserId, usePermissionMatrix, useStaff, useSetRolePermission, useSetStaffRole

## Tests

- apps/admin/src/features/permissions/pages/permissions-page.spec.tsx
