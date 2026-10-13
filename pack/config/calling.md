# Environment variables CALLING_*

id: config:CALLING
secret values are never printed

| var | app | class | type/valid | default | required | set in | meaning |
|---|---|---|---|---|---|---|---|
| CALLING_FREE_CALL_SECONDS | core-api | tuning | number | 420 |  | .env.example:280, k8s/base/core-api/configmap.yaml:141 |  |
| CALLING_PENDING_TIMEOUT_SECONDS | core-api | tuning | number | 60 |  | .env.example:281, k8s/base/core-api/configmap.yaml:142 |  |
| CALLING_RECONNECT_WINDOW_SECONDS | core-api | tuning | number | 30 |  | .env.example:282, k8s/base/core-api/configmap.yaml:143 |  |
| CALLING_TICKER_INTERVAL_MS | core-api | tuning | number | 1000 |  | .env.example:283, k8s/base/core-api/configmap.yaml:144 |  |
| CALLING_TOKEN_TTL_SECONDS | core-api | secret | number |  |  | .env.example:284, k8s/base/core-api/configmap.yaml:145 |  |
