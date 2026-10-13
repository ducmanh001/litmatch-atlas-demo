# Environment variables NOTIFICATION_*

id: config:NOTIFICATION
secret values are never printed

| var | app | class | type/valid | default | required | set in | meaning |
|---|---|---|---|---|---|---|---|
| NOTIFICATION_PUSH_PROVIDER | core-api | switch | string 'dev', 'fcm', 'apns', 'disabled' | dev |  | .env.example:323, deploy/hosted/core-api.env.example:42, deploy/production/compose.yml:94, k8s/base/core-api/configmap.yaml:170 | Notification — Giai đoạn 4 (docs/services/notification-service.md § 4) 'dev' (no-op, chặn cứng ở production), 'fcm' hoặc 'apns' dùng adapter provider tương ứng. |
| NOTIFICATION_PUSH_HTTP_TIMEOUT_MS | core-api | tuning | number | 5000 |  | .env.example:324, deploy/hosted/core-api.env.example:43, deploy/production/compose.yml:95, k8s/base/core-api/configmap.yaml:171 |  |
| NOTIFICATION_FCM_PROJECT_ID | core-api | other | string |  |  | .env.example:325, deploy/hosted/core-api.env.example:45 |  |
| NOTIFICATION_FCM_CLIENT_EMAIL | core-api | other | string |  |  | .env.example:326, deploy/hosted/core-api.env.example:46 |  |
| NOTIFICATION_FCM_PRIVATE_KEY | core-api | secret | string |  |  | .env.example:327, deploy/hosted/core-api.env.example:47 |  |
| NOTIFICATION_APNS_TEAM_ID | core-api | other | string |  |  | .env.example:328, deploy/hosted/core-api.env.example:48 |  |
| NOTIFICATION_APNS_KEY_ID | core-api | other | string |  |  | .env.example:329, deploy/hosted/core-api.env.example:49 |  |
| NOTIFICATION_APNS_PRIVATE_KEY | core-api | secret | string |  |  | .env.example:330, deploy/hosted/core-api.env.example:50 |  |
| NOTIFICATION_APNS_BUNDLE_ID | core-api | other | string |  |  | .env.example:331, deploy/hosted/core-api.env.example:51 |  |
| NOTIFICATION_APNS_ENVIRONMENT | core-api | switch | string 'sandbox', 'production' | sandbox |  | .env.example:332, deploy/hosted/core-api.env.example:52 |  |
