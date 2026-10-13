# Environment variables AUTH_*

id: config:AUTH
secret values are never printed

| var | app | class | type/valid | default | required | set in | meaning |
|---|---|---|---|---|---|---|---|
| AUTH_REFRESH_TTL_DAYS | core-api | tuning | number | 30 |  | .env.example:113, k8s/base/core-api/configmap.yaml:42 |  |
| AUTH_OTP_TTL_SECONDS | core-api | tuning | number | 300 |  | .env.example:114, k8s/base/core-api/configmap.yaml:43 |  |
| AUTH_OTP_MAX_ATTEMPTS | core-api | tuning | number | 5 |  | .env.example:115, k8s/base/core-api/configmap.yaml:44 |  |
| AUTH_OTP_REQUESTS_PER_HOUR | core-api | tuning | number | 5 |  | .env.example:116, k8s/base/core-api/configmap.yaml:45 |  |
| AUTH_OTP_PEPPER | core-api | secret | string |  | yes | .env.example:117, deploy/production/.env.example:22, deploy/hosted/core-api.env.example:9, deploy/production/compose.yml:66, k8s/base/core-api/secret.yaml:29, .github/workflows/ci.yml:20 |  |
| AUTH_GUEST_DEVICE_TOKEN_SECRET | core-api | secret | string |  | yes | .env.example:118, deploy/production/.env.example:23, deploy/hosted/core-api.env.example:10, deploy/production/compose.yml:67, k8s/base/core-api/secret.yaml:30 |  |
| AUTH_GUEST_DEVICE_TOKEN_TTL_DAYS | core-api | secret | number |  |  | .env.example:119 |  |
| AUTH_PHONE_OTP_ENABLED | core-api | switch | boolean | true |  | .env.example:120, deploy/hosted/core-api.env.example:11, deploy/production/compose.yml:68, k8s/base/core-api/configmap.yaml:46 |  |
| AUTH_GOOGLE_CLIENT_ID | core-api | other | string |  |  | .env.example:122, deploy/hosted/core-api.env.example:13, deploy/production/compose.yml:69, k8s/base/core-api/configmap.yaml:47 |  |
| AUTH_APPLE_CLIENT_ID | core-api | other | string |  |  | .env.example:123, deploy/hosted/core-api.env.example:14, deploy/production/compose.yml:70, k8s/base/core-api/configmap.yaml:48 |  |
| AUTH_FACEBOOK_APP_ID | core-api | other | string |  |  | .env.example:124, deploy/hosted/core-api.env.example:15, deploy/production/compose.yml:71, k8s/base/core-api/configmap.yaml:49 |  |
| AUTH_FACEBOOK_APP_SECRET | core-api | secret | string |  |  | .env.example:125, deploy/hosted/core-api.env.example:16, deploy/production/compose.yml:72, k8s/base/core-api/secret.yaml:41 |  |
| AUTH_CROSS_ORIGIN_DEV | core-api | endpoint | boolean | false |  | .env.example:126, deploy/production/compose.yml:73, docker-compose.dev.yml:79 |  |
| AUTH_COOKIE_SAME_SITE | core-api | switch | string 'strict', 'none' | strict |  | .env.example:128, deploy/hosted/core-api.env.example:12 |  |
