# Environment variables ECONOMY_*

id: config:ECONOMY
secret values are never printed

| var | app | class | type/valid | default | required | set in | meaning |
|---|---|---|---|---|---|---|---|
| ECONOMY_IAP_VERIFIER | core-api | switch | string 'dev', 'store', 'disabled' | dev |  | .env.example:163, deploy/hosted/core-api.env.example:34, deploy/production/compose.yml:87, k8s/base/core-api/configmap.yaml:73 |  |
| ECONOMY_APPLE_SHARED_SECRET | core-api | secret | string |  |  | .env.example:164, k8s/base/core-api/secret.yaml:33 |  |
| ECONOMY_GOOGLE_PACKAGE_NAME | core-api | other | string |  |  | .env.example:165, k8s/base/core-api/configmap.yaml:74 |  |
| ECONOMY_GOOGLE_SA_EMAIL | core-api | other | string |  |  | .env.example:166, k8s/base/core-api/secret.yaml:34 |  |
| ECONOMY_GOOGLE_SA_PRIVATE_KEY | core-api | secret | string |  |  | .env.example:167, k8s/base/core-api/secret.yaml:35 |  |
| ECONOMY_STORE_HTTP_TIMEOUT_MS | core-api | tuning | number | 10000 |  | .env.example:169, k8s/base/core-api/configmap.yaml:75 |  |
| ECONOMY_OUTBOX_RELAY_ENABLED | core-api | switch | boolean | false |  | .env.example:170, deploy/hosted/core-api.env.example:37, deploy/production/compose.yml:88, k8s/base/core-api/configmap.yaml:76 |  |
| ECONOMY_OUTBOX_RELAY_INTERVAL_MS | core-api | tuning | number | 2000 |  | .env.example:171, k8s/base/core-api/configmap.yaml:77 |  |
| ECONOMY_OUTBOX_MAX_ATTEMPTS | core-api | tuning | number | 5 |  | .env.example:172 |  |
| ECONOMY_RECONCILIATION_ENABLED | core-api | switch | boolean | true |  | .env.example:176, k8s/base/core-api/configmap.yaml:78 |  |
| ECONOMY_RECONCILIATION_INTERVAL_MS | core-api | tuning | number | 300000 |  | .env.example:177, k8s/base/core-api/configmap.yaml:79 |  |
| ECONOMY_RECONCILIATION_FAST_INTERVAL_MS | core-api | tuning | number | 60000 |  | .env.example:178, k8s/base/core-api/configmap.yaml:80 | Tier fast (bất biến Nợ=Có + orphan receipt) chỉ là 1 câu aggregate + 1 câu COUNT → rẻ hơn nhiều so với tier deep scan sample ví; default 60s = phát hiện lệch nhanh gấp 5 lần deep … |
| ECONOMY_APPLE_WEBHOOK_VERIFIER | core-api | switch | string 'dev', 'store' | store |  | .env.example:196, deploy/hosted/core-api.env.example:35, deploy/production/compose.yml:89, k8s/base/core-api/configmap.yaml:83 | Refund/chargeback (docs/services/economy-service.md § 5) Mặc định 'store' (fail-closed) — thiếu config thì getOrThrow() chết ngay lúc verify thay vì âm thầm chấp nhận webhook giả … |
| ECONOMY_APPLE_ROOT_CA_PEM | core-api | secret | string |  |  | .env.example:198, k8s/base/core-api/configmap.yaml:86 |  |
| ECONOMY_GOOGLE_RTDN_VERIFIER | core-api | switch | string 'dev', 'store' | store |  | .env.example:199, deploy/hosted/core-api.env.example:36, deploy/production/compose.yml:90, k8s/base/core-api/configmap.yaml:87 |  |
| ECONOMY_GOOGLE_RTDN_AUDIENCE | core-api | other | string |  |  | .env.example:201, k8s/base/core-api/configmap.yaml:88 |  |
| ECONOMY_GOOGLE_RTDN_SERVICE_ACCOUNT_EMAIL | core-api | other | string |  |  | .env.example:202, k8s/base/core-api/configmap.yaml:89 |  |
| ECONOMY_APPLE_ISSUER_ID | core-api | other | string |  |  | .env.example:204, k8s/base/core-api/configmap.yaml:90 |  |
| ECONOMY_APPLE_KEY_ID | core-api | other | string |  |  | .env.example:205, k8s/base/core-api/configmap.yaml:91 |  |
| ECONOMY_APPLE_PRIVATE_KEY | core-api | secret | string |  |  | .env.example:206, k8s/base/core-api/secret.yaml:36 |  |
| ECONOMY_APPLE_BUNDLE_ID | core-api | other | string |  |  | .env.example:208, k8s/base/core-api/configmap.yaml:94 | Webhook Apple chỉ chứng minh "Apple ký", bundleId mới chứng minh "ký cho app này"; để trống thì kiểm tra này bị bỏ qua (mọi app dùng chung chuỗi chứng chỉ Apple). Bắt buộc khi IAP… |
| ECONOMY_APPLE_SERVER_API_ENV | core-api | switch | string 'sandbox', 'production' | sandbox |  | .env.example:209, k8s/base/core-api/configmap.yaml:95 |  |
| ECONOMY_REFUND_POLL_ENABLED | core-api | switch | boolean | false |  | .env.example:210, deploy/hosted/core-api.env.example:38, deploy/production/compose.yml:91, k8s/base/core-api/configmap.yaml:96 |  |
| ECONOMY_REFUND_POLL_INTERVAL_MS | core-api | tuning | number | 3600000 |  | .env.example:211, k8s/base/core-api/configmap.yaml:97 |  |
| ECONOMY_REFUND_POLL_WINDOW_DAYS | core-api | tuning | number | 60 |  | .env.example:212, k8s/base/core-api/configmap.yaml:98 |  |
