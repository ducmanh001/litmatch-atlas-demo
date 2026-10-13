# Environment variables MISC_*

id: config:MISC
secret values are never printed

| var | app | class | type/valid | default | required | set in | meaning |
|---|---|---|---|---|---|---|---|
| PORT | core-api | other | number | 3000 |  | .env.example:84, deploy/hosted/core-api.env.example:2, deploy/production/compose.yml:59, docker-compose.dev.yml:77, k8s/base/core-api/configmap.yaml:23 |  |
| HTTP_TRUST_PROXY_HOPS | core-api | other | number | 0 |  | .env.example:86, k8s/base/core-api/configmap.yaml:27 | Chỉ trust đúng số proxy do operator kiểm soát; 0 = kết nối trực tiếp, không tin X-Forwarded-For. |
| CORS_ORIGINS | core-api | endpoint | string |  |  | .env.example:88, deploy/hosted/core-api.env.example:4, deploy/hosted/signaling-gateway.env.example:4, deploy/production/compose.yml:61, deploy/production/compose.yml:126, docker-compose.dev.yml:20, k8s/base/core-api/configmap.yaml:29, k8s/base/signaling-gateway/configmap.yaml:16, .github/workflows/ci.yml:24 | Task 0 (docs/12 § 12.7 point 3) — validate format lúc boot, không đợi tới enableCors runtime |
| SWAGGER_ENABLED | core-api | switch | boolean | true |  | .env.example:89, deploy/hosted/core-api.env.example:5, deploy/production/compose.yml:60, k8s/base/core-api/configmap.yaml:30 |  |
| CAPABILITY_MAINTENANCE_FEATURES | core-api | switch | string |  |  | .env.example:90 |  |
| REDIS_URL | core-api | endpoint | string | redis://localhost:6379 |  | .env.example:98, deploy/hosted/core-api.env.example:7, deploy/hosted/signaling-gateway.env.example:6, deploy/production/compose.yml:63, deploy/production/compose.yml:129, docker-compose.dev.yml:18, k8s/base/core-api/secret.yaml:32, k8s/base/signaling-gateway/secret.yaml:18, .github/workflows/ci.yml:22 |  |
| KAFKA_BROKERS | core-api | other | string | localhost:9092 |  | .env.example:99, docker-compose.dev.yml:19, k8s/base/core-api/configmap.yaml:38 |  |
| POSTHOG_PROJECT_TOKEN | core-api | secret | string |  |  | .env.example:135, deploy/hosted/core-api.env.example:21 |  |
| POSTHOG_HOST | core-api | endpoint | string | https://us.i.posthog.com |  | .env.example:136, deploy/hosted/core-api.env.example:22, k8s/base/core-api/configmap.yaml:175 |  |
| USER_DEFAULT_AVATAR_ID | core-api | other | string | default-01 |  | .env.example:139, k8s/base/core-api/configmap.yaml:60 |  |
| FRIEND_MESSAGE_MAX_LENGTH | core-api | tuning | number | 2000 |  | .env.example:251, k8s/base/core-api/configmap.yaml:130 | Friend Chat 1-1 — Giai đoạn 2 (docs/services/friend-service.md § 7); chat lâu dài, không giới hạn ngắn như Soul Match .max khớp MESSAGE_CONTENT_HARD_CAP (sanity cap transport — fr… |
| PROFILE_DIRECT_MESSAGE_DAILY_FIRST_CHAT_THRESHOLD | core-api | tuning | number | 10 |  | .env.example:253 | Profile social actions — từ người bắt chuyện đầu tiên thứ N+1 trong ngày UTC, mở chat trực tiếp cần một Gift để tạo khác biệt cho profile được quan tâm nhiều. |
| GIFT_POINTS_RATE_PERCENT | core-api | other | number | 40 |  | .env.example:309, k8s/base/core-api/configmap.yaml:159 | Gift — Giai đoạn 3 (docs/services/gift-service.md); tỉ lệ quy đổi DIA→PTS cho người nhận, PHẢI < 100 (docs/06 § Gift: nhận 1:1 biến gift thành kênh chuyển tiền ngang hàng) |
| MOOD_STATUS_TTL_HOURS | core-api | tuning | number | 24 |  | .env.example:386 | Mood — preset-only W1 (docs/services/mood-service.md) TTL mood tính từ lúc set (snapshot vào expiresAt) — hết hạn = derive khi đọc, không cron |
| STORY_TTL_HOURS | core-api | tuning | number | 24 |  | .env.example:390 | Stories — W3 (docs/services/feed-service.md § 8) TTL story tính từ lúc tạo (snapshot vào expiresAt) — hết hạn = filter lúc đọc, sweeper chỉ dọn rác |
| STORY_SWEEPER_INTERVAL_MS | core-api | tuning | number | 3600000 |  | .env.example:391 |  |
| SIGNALING_PORT | signaling-gateway | other | number | 3001 |  | .env.example:417, deploy/hosted/signaling-gateway.env.example:2, deploy/production/compose.yml:125, docker-compose.dev.yml:104, k8s/base/signaling-gateway/configmap.yaml:14 |  |
| CORS_ORIGINS | signaling-gateway | endpoint | string |  |  | .env.example:88, deploy/hosted/core-api.env.example:4, deploy/hosted/signaling-gateway.env.example:4, deploy/production/compose.yml:61, deploy/production/compose.yml:126, docker-compose.dev.yml:20, k8s/base/core-api/configmap.yaml:29, k8s/base/signaling-gateway/configmap.yaml:16, .github/workflows/ci.yml:24 |  |
| REDIS_URL | signaling-gateway | endpoint | string | redis://localhost:6379 |  | .env.example:98, deploy/hosted/core-api.env.example:7, deploy/hosted/signaling-gateway.env.example:6, deploy/production/compose.yml:63, deploy/production/compose.yml:129, docker-compose.dev.yml:18, k8s/base/core-api/secret.yaml:32, k8s/base/signaling-gateway/secret.yaml:18, .github/workflows/ci.yml:22 |  |
| WS_MAX_CONNECTIONS_PER_USER | signaling-gateway | tuning | number | 3 |  | deploy/hosted/signaling-gateway.env.example:7, k8s/base/signaling-gateway/configmap.yaml:17 |  |
| WS_CONNECTION_LEASE_MS | signaling-gateway | tuning | number | 90000 |  | deploy/hosted/signaling-gateway.env.example:8, k8s/base/signaling-gateway/configmap.yaml:18 |  |
