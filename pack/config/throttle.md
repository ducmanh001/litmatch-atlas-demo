# Environment variables THROTTLE_*

id: config:THROTTLE
secret values are never printed

| var | app | class | type/valid | default | required | set in | meaning |
|---|---|---|---|---|---|---|---|
| THROTTLE_TTL_SECONDS | core-api | tuning | number | 60 |  | .env.example:409, k8s/base/core-api/configmap.yaml:191 |  |
| THROTTLE_LIMIT | core-api | tuning | number | 100 |  | .env.example:410, k8s/base/core-api/configmap.yaml:192 |  |
| THROTTLE_STORAGE | core-api | switch | string 'memory', 'redis' | memory |  | .env.example:414, k8s/base/core-api/configmap.yaml:194 | memory: bộ đếm theo từng process (đủ cho 1 pod/dev). redis: bộ đếm dùng chung mọi pod, lỗi Redis thì tạm quay về bộ nhớ của pod. Đặt redis khi chạy nhiều replica (mỗi request tốn … |
