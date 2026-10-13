# Module admin

id: module:admin
kind: core-api module
code: apps/core-api/src/modules/admin

AdminModule là lớp điều phối back-office cho apps/admin: kiểm quyền (role + permission theo bảng admin_role_permissions), gọi đúng service của module sở hữu dữ liệu và ghi admin_audit_logs CÙNG transaction với hành động. Nó chỉ sở hữu ma trận quyền; KHÔNG sở hữu user/ví/ledger/video/phòng/report/ticket/gift/notification và không tự ghi vào bảng của họ.

## Facts

- tables owned: admin_role_permissions
- API: 31 endpoints, 0 public, 31 admin-only, 0 require Idempotency-Key, 0 with own throttle
- error codes: 6 in AdminErrors
- tests: 2 unit spec files, 1 integration spec files
- history: 9 commits (0 fixes), last 2026-08-12
- features: runtime-capabilities, support-tickets

## Where to read next

- tables: ../db/admin.md
- API contract: ../api/admin.md
- why it talks to other services: ../service-map/admin.md and ../service-map/admin-techniques.md
- error codes: ../errors/adminerrors.md
- flows involving it: [admin-ban-and-refund](../flows/admin-ban-and-refund.md), [admin-broadcast-notification](../flows/admin-broadcast-notification.md), [admin-dashboard](../flows/admin-dashboard.md), [admin-economy-inspect-and-config](../flows/admin-economy-inspect-and-config.md), [admin-gift-catalog](../flows/admin-gift-catalog.md), [admin-report-resolution](../flows/admin-report-resolution.md), [admin-room-management](../flows/admin-room-management.md), [admin-staff-and-permissions](../flows/admin-staff-and-permissions.md), [admin-support-tickets](../flows/admin-support-tickets.md), [admin-user-lookup-and-unban](../flows/admin-user-lookup-and-unban.md), [admin-video-moderation-queue](../flows/admin-video-moderation-queue.md), [short-video-publish-moderation](../flows/short-video-publish-moderation.md)
