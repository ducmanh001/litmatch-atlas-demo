# REST API of notification

id: api:notification
module: module:notification
prefix: /api/v1; success body {data, meta?}; errors are DomainException codes (see ../errors/)
see: ../modules/notification.md

### GET /notifications

Liệt kê notification của chính user, mới nhất trước, phân trang cursor theo seq (cursor sai trả 400 CURSOR_INVALID).

- handler: NotificationController.list (apps/core-api/src/modules/notification/notification.controller.ts:42); operationId: NotificationController_list
- auth: Bearer JWT
- params: query:limit:number, query:cursor:string
- response 200 NotificationsPageDto: items:array<NotificationDto>, items[].id:string, items[].type:enum(match_confirmed|friend_message|gift_received|post_liked|post_commented|streak_milestone|streak_at_risk|match_invite_received|admin_broadcast), items[].payload:object, items[].readAt:object, items[].createdAt:string(date-time), nextCursor:object
- FE callers: none in this repo (mobile/third party or dead)

### GET /notifications/unread-count

Đếm notification chưa đọc (readAt IS NULL) cho badge chuông; web poll mỗi 60s.

- handler: NotificationController.unreadCount (apps/core-api/src/modules/notification/notification.controller.ts:55); operationId: NotificationController_unreadCount
- auth: Bearer JWT
- response 200 UnreadCountDto: count:number
- FE callers: none in this repo (mobile/third party or dead)

### POST /notifications/web-push/subscription

Đăng ký/cập nhật browser push subscription (endpoint+p256dh+auth) của user; upsert theo endpoint unique.

- handler: NotificationController.upsertWebPushSubscription (apps/core-api/src/modules/notification/notification.controller.ts:65); operationId: NotificationController_upsertWebPushSubscription
- auth: Bearer JWT
- body UpsertWebPushSubscriptionDto: endpoint*:string[maxLength=2048], expirationTime:number, keys*:WebPushSubscriptionKeysDto, keys.p256dh*:string[minLength=1 maxLength=255], keys.auth*:string[minLength=1 maxLength=255]
- response 204: 
- FE callers: none in this repo (mobile/third party or dead)

### DELETE /notifications/web-push/subscription

Huỷ subscription theo endpoint, chỉ xoá hàng thuộc chính user (delete theo userId+endpoint).

- handler: NotificationController.deleteWebPushSubscription (apps/core-api/src/modules/notification/notification.controller.ts:75); operationId: NotificationController_deleteWebPushSubscription
- auth: Bearer JWT
- body DeleteWebPushSubscriptionDto: endpoint*:string[maxLength=2048]
- response 204: 
- FE callers: none in this repo (mobile/third party or dead)

### POST /notifications/{notificationId}/read

Đánh dấu đã đọc, idempotent; chỉ chủ sở hữu (người khác nhận 404 NOTIFICATION_NOT_FOUND để không lộ sự tồn tại).

- handler: NotificationController.markRead (apps/core-api/src/modules/notification/notification.controller.ts:85); operationId: NotificationController_markRead
- auth: Bearer JWT
- params: path:notificationId*:string
- response 204: 
- FE callers: none in this repo (mobile/third party or dead)

