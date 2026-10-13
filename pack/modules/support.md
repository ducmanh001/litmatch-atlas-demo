# Module support

id: module:support
kind: core-api module
code: apps/core-api/src/modules/support

Sở hữu bảng support_tickets: người dùng gửi phản hồi/yêu cầu hỗ trợ (idempotent) và xem lại phiếu của mình; vòng đời trạng thái open -> in_progress -> resolved -> closed. Không có endpoint cho staff: staff xử lý qua module admin gọi vào SupportService, và support không gửi thông báo hay realtime khi staff trả lời.

## Facts

- tables owned: support_tickets
- API: 2 endpoints, 0 public, 0 admin-only, 1 require Idempotency-Key, 0 with own throttle
- error codes: 4 in SupportErrors
- tests: 1 unit spec files, 0 integration spec files
- history: 2 commits (0 fixes), last 2026-07-27
- features: support-tickets

## Where to read next

- tables: ../db/support.md
- API contract: ../api/support.md
- why it talks to other services: ../service-map/support.md and ../service-map/support-techniques.md
- error codes: ../errors/supporterrors.md
- flows involving it: [admin-support-tickets](../flows/admin-support-tickets.md)
