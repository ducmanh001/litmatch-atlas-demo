# Environment variables JWT_*

id: config:JWT
secret values are never printed

| var | app | class | type/valid | default | required | set in | meaning |
|---|---|---|---|---|---|---|---|
| JWT_SECRET | core-api | secret | string |  | yes | .env.example:106, deploy/production/.env.example:19, deploy/hosted/core-api.env.example:8, deploy/hosted/signaling-gateway.env.example:5, deploy/production/compose.yml:64, deploy/production/compose.yml:127, k8s/base/core-api/secret.yaml:27, k8s/base/signaling-gateway/secret.yaml:16, .github/workflows/ci.yml:19 |  |
| JWT_SECRET_PREVIOUS | core-api | secret | string |  |  | .env.example:111, deploy/production/.env.example:21, deploy/production/compose.yml:65, deploy/production/compose.yml:128 | Không được trùng khoá hiện tại và cũng không được trùng khoá của guest device token: nếu trùng, token guest-device (sống nhiều ngày) sẽ verify được như access token của người dùng… |
| JWT_ACCESS_TTL_SECONDS | core-api | tuning | number | 900 |  | .env.example:107, k8s/base/core-api/configmap.yaml:41 |  |
| JWT_SECRET | signaling-gateway | secret | string |  | yes | .env.example:106, deploy/production/.env.example:19, deploy/hosted/core-api.env.example:8, deploy/hosted/signaling-gateway.env.example:5, deploy/production/compose.yml:64, deploy/production/compose.yml:127, k8s/base/core-api/secret.yaml:27, k8s/base/signaling-gateway/secret.yaml:16, .github/workflows/ci.yml:19 |  |
| JWT_SECRET_PREVIOUS | signaling-gateway | secret | string |  |  | .env.example:111, deploy/production/.env.example:21, deploy/production/compose.yml:65, deploy/production/compose.yml:128 |  |
