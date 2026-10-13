# Admin authorization matrix

id: api:authz
see: admin.md

| permission | label | moderator default | admin default | endpoints |
|---|---|---|---|---|
| view_users | Xem danh sách người dùng | true | true | GET /admin/dashboard; GET /admin/users; GET /admin/users/{id} |
| ban_users | Khoá / Mở khoá tài khoản | true | true | POST /admin/users/{id}/ban; POST /admin/users/{id}/unban |
| resolve_reports | Duyệt báo cáo & video ngắn | true | true | GET /admin/reports; POST /admin/reports/{id}/resolve; POST /admin/reports/{id}/dismiss; GET /admin/videos/pending; GET /admin/videos/published; POST /admin/videos/{id}/approve; POST /admin/videos/{id}/reject; POST /admin/videos/{id}/remove |
| refund_transaction | Hoàn tiền giao dịch | false | true | GET /admin/economy/wallet/{userId}; GET /admin/economy/users/{userId}/transactions; POST /admin/economy/transactions/{id}/refund |
| manage_gifts | Quản lý Gift catalog | false | true | GET /admin/gifts; POST /admin/gifts; PATCH /admin/gifts/{id} |
| manage_config | Cấu hình gói Diamond / Thông báo | false | true | GET /admin/config/economy-catalog; PATCH /admin/config/iap-products/{productId}; PATCH /admin/config/vip-plans/{id}; POST /admin/notifications/broadcast |
| manage_rooms | Quản lý Party Room | true | true | POST /admin/rooms/{id}/close; GET /admin/rooms |
| manage_permissions | Phân quyền admin | false | true | GET /admin/permissions; PATCH /admin/permissions/{role}/{permission}; GET /admin/staff; PATCH /admin/staff/{id}/role |
| manage_support | Xử lý yêu cầu hỗ trợ | true | true | GET /admin/support/tickets; PATCH /admin/support/tickets/{id} |
