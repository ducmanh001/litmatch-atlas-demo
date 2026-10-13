# Environment variables DATABASE_*

id: config:DATABASE
secret values are never printed

| var | app | class | type/valid | default | required | set in | meaning |
|---|---|---|---|---|---|---|---|
| DATABASE_URL | core-api | endpoint | string |  | yes | .env.example:92, deploy/hosted/core-api.env.example:6, deploy/production/compose.yml:62, docker-compose.dev.yml:17, k8s/base/core-api/secret.yaml:31, .github/workflows/ci.yml:21, .github/workflows/hosted-release.yml:41 |  |
| DATABASE_POOL_MAX | core-api | tuning | number | 10 |  | .env.example:95, k8s/base/core-api/configmap.yaml:33 | Số kết nối tối đa MỖI pod tới Postgres (pg-pool). Mặc định 10 = mặc định của driver pg. Tổng kết nối = số pod core-api × giá trị này (+ migration/job) và phải nằm dưới max_connect… |
| DATABASE_POOL_ACQUIRE_TIMEOUT_MS | core-api | tuning | number | 10000 |  | .env.example:97, k8s/base/core-api/configmap.yaml:34 | Thời hạn chờ lấy một kết nối khi pool đã đầy; hết hạn thì lỗi thay vì chờ vô hạn. |
