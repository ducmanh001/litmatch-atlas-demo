# Feature slice: support-tickets

id: slice:support-tickets
status: implemented; owner: core-api/support with core-api/admin

Idempotent support tickets with customer tracking and staff workflow

This file follows ONE feature through every layer. Layers are joined by the actual endpoint calls found in the frontend code, so it shows what the code does, not what a plan says.

## 1. Product contract and docs

- registry contracts: openapi/core-api.json, docs/plans/2026-07-15-ui-contract-completion.md
- registry verification tests: apps/core-api/src/modules/support/support.service.spec.ts
- service docs: docs/services/support-service.md, docs/services/admin-service.md

## 2. Frontend

| route | app | endpoints of this feature it calls | page |
|---|---|---|---|
| [/moderation](../fe/route-admin-moderation.md) | admin | GET /admin/reports; GET /admin/support/tickets; GET /admin/videos/pending; GET /admin/videos/published; POST /admin/reports/{id}/resolve; POST /admin/reports/{id}/dismiss … | features/moderation/pages/moderation-page.tsx |
| [/config](../fe/route-admin-config.md) | admin | GET /admin/config/economy-catalog; PATCH /admin/config/iap-products/{productId}; PATCH /admin/config/vip-plans/{id}; POST /admin/notifications/broadcast | features/config/pages/config-page.tsx |
| [/permissions](../fe/route-admin-permissions.md) | admin | GET /admin/permissions; GET /admin/staff; PATCH /admin/permissions/{role}/{permission}; PATCH /admin/staff/{id}/role | features/permissions/pages/permissions-page.tsx |
| [/users](../fe/route-admin-users.md) | admin | GET /admin/users; POST /admin/users/{id}/ban; POST /admin/users/{id}/unban | features/users/pages/users-page.tsx |
| [/gifts](../fe/route-admin-gifts.md) | admin | GET /admin/gifts; POST /admin/gifts; PATCH /admin/gifts/{id} | features/gifts/pages/gifts-page.tsx |
| [/economy](../fe/route-admin-economy.md) | admin | GET /admin/economy/wallet/{userId}; GET /admin/economy/users/{userId}/transactions; POST /admin/economy/transactions/{id}/refund | features/economy/pages/economy-page.tsx |
| [/help](../fe/route-web-help.md) | web | GET /support/tickets/me; POST /support/tickets | app/(public)/help/page.tsx |
| [/rooms](../fe/route-admin-rooms.md) | admin | GET /admin/rooms; POST /admin/rooms/{id}/close | features/rooms/pages/rooms-page.tsx |
| [/](../fe/route-admin-root.md) | admin | GET /admin/reports | app/app-shell.tsx |
| [/](../fe/route-admin-root.md) | admin | GET /admin/dashboard | features/dashboard/pages/dashboard-page.tsx |

FE features (directories): [admin/moderation](../fe/feature-admin-moderation.md), [admin/config](../fe/feature-admin-config.md), [admin/permissions](../fe/feature-admin-permissions.md), [admin/users](../fe/feature-admin-users.md), [admin/gifts](../fe/feature-admin-gifts.md), [admin/economy](../fe/feature-admin-economy.md), [web/support](../fe/feature-web-support.md), [admin/rooms](../fe/feature-admin-rooms.md), [admin/dashboard](../fe/feature-admin-dashboard.md)

UI action map card for /moderation: Tabs / filters: Reports: pending / resolved / dismissed / Pending videos / Published videos / Support: open / progress / resolved / closed | Review actions: Resolve / dismiss / bulk case / Approve / reject / remove video / Reply / assign status ticket

User actions that mutate this feature's data:
- useDismissReport -> POST /admin/reports/{id}/dismiss (apps/admin/src/features/moderation/components/reports-panel.tsx:395)
- useResolveReport -> POST /admin/reports/{id}/resolve (apps/admin/src/features/moderation/components/reports-panel.tsx:401)
- useUpdateSupportTicket -> PATCH /admin/support/tickets/{id} (apps/admin/src/features/moderation/components/support-tickets-panel.tsx:61)
- useApproveVideo -> POST /admin/videos/{id}/approve (apps/admin/src/features/moderation/components/video-moderation-panels.tsx:44)
- useRejectVideo -> POST /admin/videos/{id}/reject (apps/admin/src/features/moderation/components/video-moderation-panels.tsx:49)
- useRemoveVideo -> POST /admin/videos/{id}/remove (apps/admin/src/features/moderation/components/video-moderation-panels.tsx:97)
- useSetIapProductActive -> PATCH /admin/config/iap-products/{productId} (apps/admin/src/features/config/pages/config-page.tsx:48)
- useSetVipPlanActive -> PATCH /admin/config/vip-plans/{id} (apps/admin/src/features/config/pages/config-page.tsx:49)
- useBroadcastNotification -> POST /admin/notifications/broadcast (apps/admin/src/features/config/pages/config-page.tsx:206)
- useSetRolePermission -> PATCH /admin/permissions/{role}/{permission} (apps/admin/src/features/permissions/pages/permissions-page.tsx:36)
- useSetStaffRole -> PATCH /admin/staff/{id}/role (apps/admin/src/features/permissions/pages/permissions-page.tsx:55)

## 3. API

| endpoint | auth | idempotency | throttle | FE callers |
|---|---|---|---|---|
| POST /support/tickets ([support](../api/support.md)) | JWT | required |  | 1 |
| GET /support/tickets/me ([support](../api/support.md)) | JWT |  |  | 1 |
| GET /admin/dashboard ([admin](../api/admin.md)) | JWT + @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.ViewUsers) |  |  | 1 |
| GET /admin/users ([admin](../api/admin.md)) | JWT + @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.ViewUsers) |  |  | 1 |
| GET /admin/users/{id} ([admin](../api/admin.md)) | JWT + @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.ViewUsers) |  |  | 0 |
| POST /admin/users/{id}/ban ([admin](../api/admin.md)) | JWT + @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.BanUsers) |  |  | 1 |
| POST /admin/users/{id}/unban ([admin](../api/admin.md)) | JWT + @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.BanUsers) |  |  | 1 |
| GET /admin/reports ([admin](../api/admin.md)) | JWT + @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.ResolveReports) |  |  | 1 |
| POST /admin/reports/{id}/resolve ([admin](../api/admin.md)) | JWT + @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.ResolveReports) |  |  | 1 |
| POST /admin/reports/{id}/dismiss ([admin](../api/admin.md)) | JWT + @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.ResolveReports) |  |  | 1 |
| GET /admin/videos/pending ([admin](../api/admin.md)) | JWT + @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.ResolveReports) |  |  | 1 |
| GET /admin/videos/published ([admin](../api/admin.md)) | JWT + @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.ResolveReports) |  |  | 1 |
| POST /admin/videos/{id}/approve ([admin](../api/admin.md)) | JWT + @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.ResolveReports) |  |  | 1 |
| POST /admin/videos/{id}/reject ([admin](../api/admin.md)) | JWT + @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.ResolveReports) |  |  | 1 |
| POST /admin/videos/{id}/remove ([admin](../api/admin.md)) | JWT + @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.ResolveReports) |  |  | 0 |
| POST /admin/rooms/{id}/close ([admin](../api/admin.md)) | JWT + @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.ManageRooms) |  |  | 1 |
| GET /admin/rooms ([admin](../api/admin.md)) | JWT + @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.ManageRooms) |  |  | 1 |
| GET /admin/support/tickets ([admin](../api/admin.md)) | JWT + @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.ManageSupport) |  |  | 1 |
| PATCH /admin/support/tickets/{id} ([admin](../api/admin.md)) | JWT + @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.ManageSupport) |  |  | 0 |
| GET /admin/gifts ([admin](../api/admin.md)) | JWT + @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.ManageGifts) |  |  | 1 |
| POST /admin/gifts ([admin](../api/admin.md)) | JWT + @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.ManageGifts) |  |  | 1 |
| PATCH /admin/gifts/{id} ([admin](../api/admin.md)) | JWT + @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.ManageGifts) |  |  | 1 |
| GET /admin/economy/wallet/{userId} ([admin](../api/admin.md)) | JWT + @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.RefundTransaction) |  |  | 1 |
| GET /admin/economy/users/{userId}/transactions ([admin](../api/admin.md)) | JWT + @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.RefundTransaction) |  |  | 0 |
| POST /admin/economy/transactions/{id}/refund ([admin](../api/admin.md)) | JWT + @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.RefundTransaction) |  |  | 0 |
| GET /admin/config/economy-catalog ([admin](../api/admin.md)) | JWT + @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.ManageConfig) |  |  | 0 |
| PATCH /admin/config/iap-products/{productId} ([admin](../api/admin.md)) | JWT + @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.ManageConfig) |  |  | 0 |
| PATCH /admin/config/vip-plans/{id} ([admin](../api/admin.md)) | JWT + @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.ManageConfig) |  |  | 0 |
| POST /admin/notifications/broadcast ([admin](../api/admin.md)) | JWT + @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.ManageConfig) |  |  | 0 |
| GET /admin/permissions ([admin](../api/admin.md)) | JWT + @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.ManagePermissions) |  |  | 1 |
| PATCH /admin/permissions/{role}/{permission} ([admin](../api/admin.md)) | JWT + @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.ManagePermissions) |  |  | 1 |
| GET /admin/staff ([admin](../api/admin.md)) | JWT + @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.ManagePermissions) |  |  | 1 |
| PATCH /admin/staff/{id}/role ([admin](../api/admin.md)) | JWT + @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.ManagePermissions) |  |  | 1 |


## 4. Backend flows (service map)

- [admin-economy-inspect-and-config](../flows/admin-economy-inspect-and-config.md): Admin xem ví, giao dịch và chỉnh cấu hình kinh tế (5 endpoints of this feature)
- [admin-staff-and-permissions](../flows/admin-staff-and-permissions.md): Admin quản lý nhân sự và ma trận quyền (4 endpoints of this feature)
- [admin-support-tickets](../flows/admin-support-tickets.md): Người dùng gửi ticket hỗ trợ, admin xử lý (4 endpoints of this feature)
- [admin-video-moderation-queue](../flows/admin-video-moderation-queue.md): Admin duyệt hàng chờ video (4 endpoints of this feature)
- [admin-gift-catalog](../flows/admin-gift-catalog.md): Admin quản lý danh mục quà (3 endpoints of this feature)
- [admin-report-resolution](../flows/admin-report-resolution.md): Admin xử lý báo cáo vi phạm (3 endpoints of this feature)
- [admin-user-lookup-and-unban](../flows/admin-user-lookup-and-unban.md): Admin tra cứu người dùng và mở khoá (3 endpoints of this feature)
- [admin-ban-and-refund](../flows/admin-ban-and-refund.md): Admin khoá tài khoản và hoàn giao dịch (2 endpoints of this feature)
- [admin-room-management](../flows/admin-room-management.md): Admin xem và đóng phòng party (2 endpoints of this feature)
- [admin-broadcast-notification](../flows/admin-broadcast-notification.md): Admin gửi thông báo hàng loạt (1 endpoints of this feature)

## 5. Data

- tables owned by the module(s): `admin_role_permissions`, `support_tickets` (see ../db/support.md, ../db/admin.md)
- status vocabularies enforced by CHECK:
  - admin_role_permissions: moderator | admin
  - support_tickets: feedback | bug | idea
  - support_tickets: open | in_progress | resolved | closed

## 6. Events

No realtime event is produced by these modules.

## 7. Errors

- error code groups: [AdminErrors](../errors/adminerrors.md), [SupportErrors](../errors/supporterrors.md)
- codes the FE references for this feature: none

## 8. Configuration

env vars read by the module(s): none (see ../config/)

## 9. Tests

- admin: 2 unit spec files, 1 integration spec files
- support: 1 unit spec files, 0 integration spec files
- e2e files that call these endpoints: none
- FE specs: apps/admin/src/features/moderation/pages/moderation-page.spec.tsx, apps/admin/src/features/config/pages/config-page.spec.tsx, apps/admin/src/features/permissions/pages/permissions-page.spec.tsx, apps/admin/src/features/users/pages/users-page.spec.tsx, apps/admin/src/features/gifts/pages/gifts-page.spec.tsx, apps/admin/src/features/economy/pages/economy-page.spec.tsx, apps/web/src/app/(public)/help/page.spec.tsx, apps/admin/src/features/rooms/pages/rooms-page.spec.tsx, apps/admin/src/features/dashboard/pages/dashboard-page.spec.tsx

## 10. Known issues (public subset of the ledger)

none linked

See ../issues/index.md.
