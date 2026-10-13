# REST API of admin

id: api:admin
module: module:admin
prefix: /api/v1; success body {data, meta?}; errors are DomainException codes (see ../errors/)
see: ../modules/admin.md

### GET /admin/dashboard

Dashboard tổng hợp: thống kê user, doanh số diamond 7 ngày, số phòng đang mở và 10 hoạt động audit gần nhất. Cần permission view_users.

- handler: AdminController.getDashboard (apps/core-api/src/modules/admin/admin.controller.ts:104); operationId: AdminController_getDashboard
- auth: Bearer JWT; guards: @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.ViewUsers)
- response 200 AdminDashboardDto: newUsersToday:number, newUsersPreviousDay:number, activeUsers:number, activeRoomCount:number, totalDiamondSpentSevenDays:string, dailyDiamondSpent:array<AdminDailyDiamondDto>, dailyDiamondSpent[].date:string, dailyDiamondSpent[].amount:string, userTiers:AdminUserTierDistributionDto, userTiers.free:number, userTiers.vip:number, userTiers.svip:number, recentActivities:array<AdminActivityDto>, recentActivities[].id:string, recentActivities[].actorUserId:string, recentActivities[].actorNickname:string, recentActivities[].action:string, recentActivities[].targetType:string, recentActivities[].targetId:string, recentActivities[].createdAt:string(date-time)
- FE callers: admin apps/admin/src/features/dashboard/api.ts:17

### GET /admin/users

Danh sách user lọc theo status/role/nickname, phân trang offset. Cần view_users.

- handler: AdminController.listUsers (apps/core-api/src/modules/admin/admin.controller.ts:114); operationId: AdminController_listUsers
- auth: Bearer JWT; guards: @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.ViewUsers)
- params: query:status:enum(active|banned), query:role:enum(user|moderator|admin), query:nickname:string, query:limit:number, query:offset:number
- response 200 AdminUsersPageDto: items:array<AdminUserDto>, items[].id:string, items[].nickname:string, items[].gender:enum(unknown|male|female|other), items[].avatarId:string, items[].isGuest:boolean, items[].status:enum(active|banned), items[].role:enum(user|moderator|admin), total:number
- FE callers: admin apps/admin/src/features/users/api.ts:27

### GET /admin/users/{id}

Xem hồ sơ nội bộ của một user (status + role). Cần view_users.

- handler: AdminController.getUser (apps/core-api/src/modules/admin/admin.controller.ts:131); operationId: AdminController_getUser
- auth: Bearer JWT; guards: @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.ViewUsers)
- params: path:id*:string
- response 200 AdminUserDto: id:string, nickname:string, gender:enum(unknown|male|female|other), avatarId:string, isGuest:boolean, status:enum(active|banned), role:enum(user|moderator|admin)
- FE callers: none in this repo (mobile/third party or dead)

### POST /admin/users/{id}/ban

Khoá tài khoản user và ghi audit user.banned trong cùng transaction; chặn tự ban chính mình (CANNOT_BAN_SELF). Cần ban_users.

- handler: AdminController.banUser (apps/core-api/src/modules/admin/admin.controller.ts:141); operationId: AdminController_banUser
- auth: Bearer JWT; guards: @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.BanUsers)
- params: path:id*:string
- response 200 AdminUserDto: id:string, nickname:string, gender:enum(unknown|male|female|other), avatarId:string, isGuest:boolean, status:enum(active|banned), role:enum(user|moderator|admin)
- FE callers: admin apps/admin/src/features/users/api.ts:49

### POST /admin/users/{id}/unban

Mở khoá tài khoản user và ghi audit user.unbanned cùng transaction. Cần ban_users.

- handler: AdminController.unbanUser (apps/core-api/src/modules/admin/admin.controller.ts:155); operationId: AdminController_unbanUser
- auth: Bearer JWT; guards: @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.BanUsers)
- params: path:id*:string
- response 200 AdminUserDto: id:string, nickname:string, gender:enum(unknown|male|female|other), avatarId:string, isGuest:boolean, status:enum(active|banned), role:enum(user|moderator|admin)
- FE callers: admin apps/admin/src/features/users/api.ts:62

### GET /admin/reports

Danh sách báo cáo vi phạm (report) lọc và phân trang offset. Cần resolve_reports.

- handler: AdminController.listReports (apps/core-api/src/modules/admin/admin.controller.ts:169); operationId: AdminController_listReports
- auth: Bearer JWT; guards: @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.ResolveReports)
- params: query:status:enum(pending|resolved|dismissed), query:limit:number, query:offset:number
- response 200 AdminReportsPageDto: items:array<AdminReportDto>, items[].id:string, items[].reporterUserId:string, items[].targetType:enum(user|video), items[].targetUserId:string, items[].targetVideoId:string, items[].reason:enum(harassment|spam|underage|inappropriate_content|other), items[].description:object, items[].trustPenaltyApplied:number, items[].status:enum(pending|resolved|dismissed), items[].createdAt:string(date-time), total:number
- FE callers: admin apps/admin/src/features/moderation/api.ts:25

### POST /admin/reports/{id}/resolve

Đánh dấu report là resolved + audit report.resolved cùng transaction. Cần resolve_reports.

- handler: AdminController.resolveReport (apps/core-api/src/modules/admin/admin.controller.ts:184); operationId: AdminController_resolveReport
- auth: Bearer JWT; guards: @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.ResolveReports)
- params: path:id*:string
- response 200 AdminReportDto: id:string, reporterUserId:string, targetType:enum(user|video), targetUserId:string, targetVideoId:string, reason:enum(harassment|spam|underage|inappropriate_content|other), description:object, trustPenaltyApplied:number, status:enum(pending|resolved|dismissed), createdAt:string(date-time)
- FE callers: admin apps/admin/src/features/moderation/api.ts:44

### POST /admin/reports/{id}/dismiss

Bác bỏ report (dismissed) + audit cùng transaction. Cần resolve_reports.

- handler: AdminController.dismissReport (apps/core-api/src/modules/admin/admin.controller.ts:198); operationId: AdminController_dismissReport
- auth: Bearer JWT; guards: @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.ResolveReports)
- params: path:id*:string
- response 200 AdminReportDto: id:string, reporterUserId:string, targetType:enum(user|video), targetUserId:string, targetVideoId:string, reason:enum(harassment|spam|underage|inappropriate_content|other), description:object, trustPenaltyApplied:number, status:enum(pending|resolved|dismissed), createdAt:string(date-time)
- FE callers: admin apps/admin/src/features/moderation/api.ts:58

### GET /admin/videos/pending

Danh sách video chờ duyệt (chế độ VIDEO_MODERATION_MODE=pre). Cần resolve_reports.

- handler: AdminController.listPendingVideos (apps/core-api/src/modules/admin/admin.controller.ts:212); operationId: AdminController_listPendingVideos
- auth: Bearer JWT; guards: @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.ResolveReports)
- params: query:limit:number, query:cursor:string
- response 200 AdminVideosPageDto: items:array<AdminVideoDto>, items[].id:string, items[].authorUserId:string, items[].status:enum(uploading|processing|pending_review|published|removed|rejected|failed), items[].playbackUrl:string, items[].thumbnailUrl:string, items[].caption:string, items[].createdAt:string(date-time), nextCursor:string
- FE callers: admin apps/admin/src/features/moderation/videos-api.ts:25

### GET /admin/videos/published

Danh sách video đang published (mới nhất trước) để admin gỡ thủ công. Cần resolve_reports.

- handler: AdminController.listPublishedVideos (apps/core-api/src/modules/admin/admin.controller.ts:228); operationId: AdminController_listPublishedVideos
- auth: Bearer JWT; guards: @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.ResolveReports)
- params: query:limit:number, query:cursor:string
- response 200 AdminVideosPageDto: items:array<AdminVideoDto>, items[].id:string, items[].authorUserId:string, items[].status:enum(uploading|processing|pending_review|published|removed|rejected|failed), items[].playbackUrl:string, items[].thumbnailUrl:string, items[].caption:string, items[].createdAt:string(date-time), nextCursor:string
- FE callers: admin apps/admin/src/features/moderation/videos-api.ts:38

### POST /admin/videos/{id}/approve

Duyệt video pending_review -> published + audit video.approved cùng transaction. Cần resolve_reports.

- handler: AdminController.approveVideo (apps/core-api/src/modules/admin/admin.controller.ts:244); operationId: AdminController_approveVideo
- auth: Bearer JWT; guards: @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.ResolveReports)
- params: path:id*:string
- response 200 AdminVideoDto: id:string, authorUserId:string, status:enum(uploading|processing|pending_review|published|removed|rejected|failed), playbackUrl:string, thumbnailUrl:string, caption:string, createdAt:string(date-time)
- FE callers: admin apps/admin/src/features/moderation/videos-api.ts:51

### POST /admin/videos/{id}/reject

Từ chối video pending_review -> rejected + audit video.rejected cùng transaction. Cần resolve_reports.

- handler: AdminController.rejectVideo (apps/core-api/src/modules/admin/admin.controller.ts:260); operationId: AdminController_rejectVideo
- auth: Bearer JWT; guards: @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.ResolveReports)
- params: path:id*:string
- response 200 AdminVideoDto: id:string, authorUserId:string, status:enum(uploading|processing|pending_review|published|removed|rejected|failed), playbackUrl:string, thumbnailUrl:string, caption:string, createdAt:string(date-time)
- FE callers: admin apps/admin/src/features/moderation/videos-api.ts:65

### POST /admin/videos/{id}/remove

Gỡ thủ công video published -> removed + audit video.removed, bổ sung cho auto-hide theo ngưỡng report. Cần resolve_reports.

- handler: AdminController.removeVideo (apps/core-api/src/modules/admin/admin.controller.ts:276); operationId: AdminController_removeVideo
- auth: Bearer JWT; guards: @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.ResolveReports)
- params: path:id*:string
- response 200 AdminVideoDto: id:string, authorUserId:string, status:enum(uploading|processing|pending_review|published|removed|rejected|failed), playbackUrl:string, thumbnailUrl:string, caption:string, createdAt:string(date-time)
- FE callers: none in this repo (mobile/third party or dead)

### POST /admin/rooms/{id}/close

Đóng cưỡng bức party room (reason AdminClosed) với audit trong cùng transaction đóng phòng; dọn LiveKit/realtime chạy sau commit trong party-room. Cần manage_rooms.

- handler: AdminController.closeRoom (apps/core-api/src/modules/admin/admin.controller.ts:293); operationId: AdminController_closeRoom
- auth: Bearer JWT; guards: @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.ManageRooms)
- params: path:id*:string
- response 200 AdminCloseRoomResultDto: closed:boolean
- FE callers: admin apps/admin/src/features/rooms/api.ts:53

### GET /admin/rooms

Danh sách party room kèm số member, phân trang cursor. Cần manage_rooms.

- handler: AdminController.listRooms (apps/core-api/src/modules/admin/admin.controller.ts:307); operationId: AdminController_listRooms
- auth: Bearer JWT; guards: @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.ManageRooms)
- params: query:limit:number, query:cursor:string
- response 200 AdminRoomsPageDto: data:array<AdminRoomDto>, data[].id:string, data[].hostUserId:string, data[].title:string, data[].status:enum(active|closed), data[].speakerLimit:number, data[].category:enum(talk|sing|friend|study|other), data[].memberCount:number, data[].closeReason:enum(host_left|finished|swept|error|admin_closed), data[].createdAt:string(date-time), data[].hostDisconnectedAt:string(date-time), meta:object, meta.nextCursor:string
- FE callers: admin apps/admin/src/features/rooms/api.ts:40

### GET /admin/support/tickets

Danh sách ticket hỗ trợ của mọi user, lọc status, phân trang cursor. Cần manage_support.

- handler: AdminController.listSupportTickets (apps/core-api/src/modules/admin/admin.controller.ts:320); operationId: AdminController_listSupportTickets
- auth: Bearer JWT; guards: @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.ManageSupport)
- params: query:status:enum(open|in_progress|resolved|closed), query:limit:number, query:cursor:string
- response 200 AdminSupportTicketsPageDto: items:array<SupportTicketDto>, items[].id:string, items[].userId:string, items[].category:enum(feedback|bug|idea), items[].message:string, items[].status:enum(open|in_progress|resolved|closed), items[].staffResponse:string, items[].createdAt:string(date-time), items[].updatedAt:string(date-time), meta:object, meta.nextCursor:string
- FE callers: admin apps/admin/src/features/moderation/support-api.ts:21

### PATCH /admin/support/tickets/{id}

Đổi trạng thái ticket (kiểm canTransition) và staffResponse + audit support.ticket.updated cùng transaction. Cần manage_support.

- handler: AdminController.updateSupportTicket (apps/core-api/src/modules/admin/admin.controller.ts:339); operationId: AdminController_updateSupportTicket
- auth: Bearer JWT; guards: @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.ManageSupport)
- params: path:id*:string
- body AdminUpdateSupportTicketDto: status*:enum(open|in_progress|resolved|closed), staffResponse:string[maxLength=2000]
- response 200 AdminSupportTicketDto: id:string, userId:string, category:enum(feedback|bug|idea), message:string, status:enum(open|in_progress|resolved|closed), staffResponse:string, createdAt:string(date-time), updatedAt:string(date-time)
- FE callers: none in this repo (mobile/third party or dead)

### GET /admin/gifts

Xem toàn bộ catalog quà kể cả quà đã tắt. Cần manage_gifts.

- handler: AdminController.listGifts (apps/core-api/src/modules/admin/admin.controller.ts:355); operationId: AdminController_listGifts
- auth: Bearer JWT; guards: @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.ManageGifts)
- response 200 array<AdminGiftDto>: [].id:string, [].code:string, [].name:string, [].priceDiamond:number, [].active:boolean, [].sortOrder:number, [].createdAt:string(date-time), [].updatedAt:string(date-time)
- FE callers: admin apps/admin/src/features/gifts/api.ts:19

### POST /admin/gifts

Tạo quà mới (code unique uq_gifts_code) + audit gift.created cùng transaction. Cần manage_gifts.

- handler: AdminController.createGift (apps/core-api/src/modules/admin/admin.controller.ts:366); operationId: AdminController_createGift
- auth: Bearer JWT; guards: @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.ManageGifts)
- body CreateGiftDto: code*:string, name*:string, priceDiamond*:number, sortOrder:number[default=0]
- response 200 AdminGiftDto: id:string, code:string, name:string, priceDiamond:number, active:boolean, sortOrder:number, createdAt:string(date-time), updatedAt:string(date-time)
- FE callers: admin apps/admin/src/features/gifts/api.ts:30

### PATCH /admin/gifts/{id}

Sửa giá/tên/thứ tự/bật-tắt quà (không hard-delete) + audit gift.updated cùng transaction. Cần manage_gifts.

- handler: AdminController.updateGift (apps/core-api/src/modules/admin/admin.controller.ts:379); operationId: AdminController_updateGift
- auth: Bearer JWT; guards: @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.ManageGifts)
- params: path:id*:string
- body UpdateGiftDto: name:string, priceDiamond:number, sortOrder:number, active:boolean
- response 200 AdminGiftDto: id:string, code:string, name:string, priceDiamond:number, active:boolean, sortOrder:number, createdAt:string(date-time), updatedAt:string(date-time)
- FE callers: admin apps/admin/src/features/gifts/api.ts:41

### GET /admin/economy/wallet/{userId}

Xem ví (balance snapshot + VIP) của một user, chỉ đọc. Cần refund_transaction.

- handler: AdminController.getWallet (apps/core-api/src/modules/admin/admin.controller.ts:396); operationId: AdminController_getWallet
- auth: Bearer JWT; guards: @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.RefundTransaction)
- params: path:userId*:string
- response 200 AdminWalletDto: balance:string, earnings:string, vipTier:enum(vip|svip), vipExpiresAt:string(date-time)
- FE callers: admin apps/admin/src/features/economy/api.ts:25

### GET /admin/economy/users/{userId}/transactions

Lịch sử giao dịch của user, phân trang cursor, actor-scoped. Cần refund_transaction.

- handler: AdminController.listTransactions (apps/core-api/src/modules/admin/admin.controller.ts:406); operationId: AdminController_listTransactions
- auth: Bearer JWT; guards: @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.RefundTransaction)
- params: path:userId*:string, query:limit:number, query:cursor:string
- response 200 AdminTransactionsPageDto: items:array<AdminTransactionDto>, items[].id:string, items[].type:string, items[].status:string, items[].diamondDelta:string, items[].createdAt:string(date-time), nextCursor:object
- FE callers: none in this repo (mobile/third party or dead)

### POST /admin/economy/transactions/{id}/refund

Hoàn tiền thủ công một giao dịch bằng bút toán đảo (ledger.reverse), bắt buộc có reason, audit cùng transaction; không sửa/xoá giao dịch gốc. Cần refund_transaction.

- handler: AdminController.refundTransaction (apps/core-api/src/modules/admin/admin.controller.ts:426); operationId: AdminController_refundTransaction
- auth: Bearer JWT; guards: @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.RefundTransaction)
- params: path:id*:string
- body RefundTransactionDto: reason*:string
- response 200 RefundResultDto: transactionId:string, reversalTransactionId:string
- FE callers: none in this repo (mobile/third party or dead)

### GET /admin/config/economy-catalog

Xem catalog kinh tế: gói IAP diamond và gói VIP kèm trạng thái active. Cần manage_config.

- handler: AdminController.getEconomyCatalog (apps/core-api/src/modules/admin/admin.controller.ts:442); operationId: AdminController_getEconomyCatalog
- auth: Bearer JWT; guards: @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.ManageConfig)
- response 200 AdminEconomyCatalogDto: iapProducts:array<AdminIapProductDto>, iapProducts[].productId:string, iapProducts[].provider:enum(apple|google), iapProducts[].diamonds:string, iapProducts[].active:boolean, vipPlans:array<AdminVipPlanDto>, vipPlans[].id:string, vipPlans[].tier:enum(vip|svip), vipPlans[].days:number, vipPlans[].priceDiamond:string, vipPlans[].active:boolean
- FE callers: none in this repo (mobile/third party or dead)

### PATCH /admin/config/iap-products/{productId}

Bật/tắt một IAP product + audit economy.iap_product.updated cùng transaction. Cần manage_config.

- handler: AdminController.setIapProductActive (apps/core-api/src/modules/admin/admin.controller.ts:454); operationId: AdminController_setIapProductActive
- auth: Bearer JWT; guards: @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.ManageConfig)
- params: path:productId*:string
- body SetCatalogActiveDto: active*:boolean
- response 200 AdminIapProductDto: productId:string, provider:enum(apple|google), diamonds:string, active:boolean
- FE callers: none in this repo (mobile/third party or dead)

### PATCH /admin/config/vip-plans/{id}

Bật/tắt một gói VIP + audit economy.vip_plan.updated cùng transaction. Cần manage_config.

- handler: AdminController.setVipPlanActive (apps/core-api/src/modules/admin/admin.controller.ts:472); operationId: AdminController_setVipPlanActive
- auth: Bearer JWT; guards: @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.ManageConfig)
- params: path:id*:string
- body SetCatalogActiveDto: active*:boolean
- response 200 AdminVipPlanDto: id:string, tier:enum(vip|svip), days:number, priceDiamond:string, active:boolean
- FE callers: none in this repo (mobile/third party or dead)

### POST /admin/notifications/broadcast

Gửi thông báo broadcast tới nhóm all/vip/non-vip: lưu in-app + audit trong một transaction, rồi push best-effort sau commit. Cần manage_config.

- handler: AdminController.broadcastNotification (apps/core-api/src/modules/admin/admin.controller.ts:490); operationId: AdminController_broadcastNotification
- auth: Bearer JWT; guards: @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.ManageConfig)
- body BroadcastNotificationDto: title*:string[maxLength=120], body*:string[maxLength=500], audience*:enum(all|vip|free)
- response 200 BroadcastNotificationResultDto: broadcastId:string, recipientCount:number
- FE callers: none in this repo (mobile/third party or dead)

### GET /admin/permissions

Đọc ma trận quyền role (moderator/admin) x permission từ admin_role_permissions. Cần manage_permissions.

- handler: AdminController.getPermissionMatrix (apps/core-api/src/modules/admin/admin.controller.ts:505); operationId: AdminController_getPermissionMatrix
- auth: Bearer JWT; guards: @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.ManagePermissions)
- response 200 AdminPermissionMatrixDto: permissions:array<AdminRolePermissionDto>, permissions[].permission:enum(view_users|ban_users|resolve_reports|refund_transaction|manage_gifts|manage_config|manage_rooms|manage_permissions|manage_support), permissions[].label:string, permissions[].moderator:boolean, permissions[].admin:boolean
- FE callers: admin apps/admin/src/features/permissions/api.ts:20

### PATCH /admin/permissions/{role}/{permission}

Bật/tắt một permission cho role admin/moderator + audit; không cho tắt manage_permissions của admin (tránh tự khoá). Cần manage_permissions.

- handler: AdminController.setRolePermission (apps/core-api/src/modules/admin/admin.controller.ts:520); operationId: AdminController_setRolePermission
- auth: Bearer JWT; guards: @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.ManagePermissions)
- params: path:role*:string, path:permission*:string
- body SetRolePermissionDto: enabled*:boolean
- response 204: 
- FE callers: admin apps/admin/src/features/permissions/api.ts:34

### GET /admin/staff

Danh sách nhân sự (user có role admin/moderator). Cần manage_permissions.

- handler: AdminController.listStaff (apps/core-api/src/modules/admin/admin.controller.ts:538); operationId: AdminController_listStaff
- auth: Bearer JWT; guards: @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.ManagePermissions)
- response 200 array<AdminStaffDto>: [].id:string, [].nickname:string, [].role:enum(moderator|admin)
- FE callers: admin apps/admin/src/features/permissions/api.ts:50

### PATCH /admin/staff/{id}/role

Đổi role nhân sự với khoá pessimistic để không hạ admin cuối cùng; chặn tự đổi role chính mình; audit cùng transaction. Cần manage_permissions.

- handler: AdminController.setStaffRole (apps/core-api/src/modules/admin/admin.controller.ts:546); operationId: AdminController_setStaffRole
- auth: Bearer JWT; guards: @RequireRoles(Roles.Admin, Roles.Moderator) @UseGuards(AdminPermissionGuard) @RequireAdminPermission(AdminPermission.ManagePermissions)
- params: path:id*:string
- body SetStaffRoleDto: role*:enum(user|moderator|admin)
- response 200 AdminStaffDto: id:string, nickname:string, role:enum(moderator|admin)
- FE callers: admin apps/admin/src/features/permissions/api.ts:63

