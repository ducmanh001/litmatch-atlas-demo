# Environment variables MEDIA_*

id: config:MEDIA
secret values are never printed

| var | app | class | type/valid | default | required | set in | meaning |
|---|---|---|---|---|---|---|---|
| MEDIA_STORAGE_PROVIDER | core-api | switch | string 'dev', 'r2', 's3', 'minio' | dev |  | .env.example:143, deploy/production/.env.example:29, deploy/hosted/core-api.env.example:58, deploy/production/compose.yml:97, k8s/base/core-api/configmap.yaml:63 | Image uploads — dev adapter mặc định; production phải dùng storage cloud thật. |
| MEDIA_R2_ACCOUNT_ID | core-api | other | string |  |  | .env.example:152, deploy/production/.env.example:38, deploy/hosted/core-api.env.example:67, deploy/production/compose.yml:98, k8s/base/core-api/configmap.yaml:64 |  |
| MEDIA_R2_BUCKET | core-api | other | string |  |  | .env.example:153, deploy/production/.env.example:39, deploy/hosted/core-api.env.example:68, deploy/production/compose.yml:99, k8s/base/core-api/configmap.yaml:65 |  |
| MEDIA_R2_ACCESS_KEY_ID | core-api | other | string |  |  | .env.example:154, deploy/production/.env.example:40, deploy/hosted/core-api.env.example:69, deploy/production/compose.yml:100, k8s/base/core-api/secret.yaml:39 |  |
| MEDIA_R2_SECRET_ACCESS_KEY | core-api | secret | string |  |  | .env.example:155, deploy/production/.env.example:41, deploy/hosted/core-api.env.example:70, deploy/production/compose.yml:101, k8s/base/core-api/secret.yaml:40 |  |
| MEDIA_PUBLIC_BASE_URL | core-api | endpoint | string |  |  | .env.example:156, deploy/production/.env.example:42, deploy/hosted/core-api.env.example:71, deploy/production/compose.yml:102, k8s/base/core-api/configmap.yaml:66 |  |
| MEDIA_UPLOAD_URL_TTL_SECONDS | core-api | tuning | number | 900 |  | .env.example:157, deploy/production/.env.example:43, deploy/hosted/core-api.env.example:72, deploy/production/compose.yml:103, k8s/base/core-api/configmap.yaml:67 |  |
| MEDIA_IMAGE_MAX_BYTES | core-api | tuning | number | 10 * 1024 * 1024 |  | .env.example:158, deploy/production/.env.example:44, deploy/hosted/core-api.env.example:73, deploy/production/compose.yml:104, k8s/base/core-api/configmap.yaml:68 |  |
| MEDIA_ALLOWED_IMAGE_TYPES | core-api | other | string | image/jpeg,image/png,image/webp,image/gif |  | .env.example:159, deploy/production/.env.example:45, deploy/hosted/core-api.env.example:74, deploy/production/compose.yml:105, k8s/base/core-api/configmap.yaml:69 |  |
