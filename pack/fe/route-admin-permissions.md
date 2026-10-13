# admin route /permissions

id: fe:route:admin:/permissions
file: apps/admin/src/features/permissions/pages/permissions-page.tsx:86
layout: admin (RequireAuth + role)
reached source files: 14 (static import closure); data features: permissions

## UI action map card (human-authored, not verified against code)

PERMISSIONS /permissions: Role và policy của staff

Policy actions:
  - Xem permission matrix
  - Bật / tắt quyền Moderator
  - Bật / tắt quyền Admin
  - Bảo vệ manage_permissions
Staff actions:
  - Đổi role staff
  - Thu hồi quyền → user role

## Data it reads (GET)

| endpoint | hook | query key | polling | stale time | module | defined at |
|---|---|---|---|---|---|---|
| GET /admin/permissions | usePermissionMatrix | permissionKeys.matrix |  |  | [admin](../api/admin.md) | apps/admin/src/features/permissions/api.ts:20 |
| GET /admin/staff | useStaff | permissionKeys.staff |  |  | [admin](../api/admin.md) | apps/admin/src/features/permissions/api.ts:50 |

## Actions that change data (mutations)

| UI element and label | handler | hook | endpoint | module | call site |
|---|---|---|---|---|---|
| (no matching UI element found; called from togglePermission) |  | useSetRolePermission | PATCH /admin/permissions/{role}/{permission} | [admin](../api/admin.md) | apps/admin/src/features/permissions/pages/permissions-page.tsx:36 |
| (no matching UI element found; called from changeStaffRole) |  | useSetStaffRole | PATCH /admin/staff/{id}/role | [admin](../api/admin.md) | apps/admin/src/features/permissions/pages/permissions-page.tsx:55 |

## Interactive elements (1)

- Button.onClick permissions.revoke (permissions.revoke) -> () => changeStaffRole(staffMember, 'user') (apps/admin/src/features/permissions/pages/permissions-page.tsx:205)

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

- cookie-write  (apps/admin/src/shared/i18n/locale-store.ts:28)

## i18n keys used (27)

- auth.changePhone: ← Đổi số điện thoại
- auth.guestAvailable: Có thể dùng tài khoản khách.
- common.disabled: 
- common.enabled: 
- common.loading: Đang tải…
- common.unknownError: Sự cố không xác định. Vui lòng tải lại.
- language.english: English
- language.vietnamese: Tiếng Việt
- permissions.emptyStaff: 
- permissions.loadingPolicy: 
- permissions.loadingStaff: 
- permissions.matrix: 
- permissions.matrixHint: 
- permissions.permission: 
- permissions.revoke: 
- permissions.revoked: 
- permissions.roleChanged: 
- permissions.roleOf: 
- permissions.self: 
- permissions.staffList: 
- permissions.updated: 
- state.copyTraceId: 
- state.trace: 
- state.traceCopied: 
- state.traceCopyFailed: 
- … 2 more, see i18n-*.md

## Tests

- apps/admin/src/features/permissions/pages/permissions-page.spec.tsx

## Backend

modules: [admin](../modules/admin.md)

service-map flows touching these endpoints: [admin-staff-and-permissions](../flows/admin-staff-and-permissions.md)
