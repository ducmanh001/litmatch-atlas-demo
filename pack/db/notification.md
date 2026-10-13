# Database tables owned by module notification

id: db:notification
module: module:notification
tables: notifications, web_push_subscriptions
see: ../modules/notification.md

### table: notifications

In-app notification (docs/services/notification-service.md). `payload` chỉ chứa dữ liệu tối thiểu để client tự render — KHÔNG hardcode text hiển thị ở backend, và KHÔNG bao giờ chứa partnerId/nickname cho `match_confirmed` (Soul Match ẩn danh tới khi unlock — docs/06).

Entity: Notification (apps/core-api/src/modules/notification/entities/notification.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | uuid |  | gen_random_uuid() | PK |  |
| seq | bigint |  | nextval('notifications_seq_seq'::regclass) |  | Thứ tự tăng dần DB cấp cho cursor keyset — không dùng createdAt (docs/05 § 5.4). |
| user_id | uuid |  |  | FK→users.id |  |
| type | character varying(32) |  |  |  |  |
| payload | jsonb |  |  |  |  |
| read_at | timestamp with time zone | null |  |  |  |
| created_at | timestamp with time zone |  | now() |  |  |

Indexes: idx_notifications_unread_user (user_id) WHERE (read_at IS NULL); idx_notifications_user_seq (user_id, seq)

Access: notification(owner) r3/w5 via manager+manager.save(instance)+querybuilder+repository [apps/core-api/src/modules/notification/notification.service.ts:60, apps/core-api/src/modules/notification/notification.service.ts:75]

### table: web_push_subscriptions

Một endpoint browser/user — endpoint được coi là credential, không log ra ngoài adapter.

Entity: WebPushSubscription (apps/core-api/src/modules/notification/entities/web-push-subscription.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | uuid |  | gen_random_uuid() | PK |  |
| user_id | uuid |  |  | FK→users.id |  |
| endpoint | text |  |  | UQ |  |
| p256dh | character varying(255) |  |  |  |  |
| auth | character varying(255) |  |  |  |  |
| created_at | timestamp with time zone |  | now() |  |  |
| updated_at | timestamp with time zone |  | now() |  |  |

Constraints: unique uq_web_push_subscriptions_endpoint (endpoint)

Indexes: idx_web_push_subscriptions_user (user_id); UNIQUE uq_web_push_subscriptions_endpoint (endpoint)

Access: notification(owner) r1/w3 via repository [apps/core-api/src/modules/notification/services/web-push-subscription.service.ts:20, apps/core-api/src/modules/notification/services/web-push-subscription.service.ts:32]

