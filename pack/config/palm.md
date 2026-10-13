# Environment variables PALM_*

id: config:PALM
secret values are never printed

| var | app | class | type/valid | default | required | set in | meaning |
|---|---|---|---|---|---|---|---|
| PALM_MATCH_TARGET_NAME_MAX_LENGTH | core-api | tuning | number | 50 |  | .env.example:351, k8s/base/core-api/configmap.yaml:188 | Palm Match — Giai đoạn 5 (docs/services/palm-match-service.md § 5) |
| PALM_MATCH_QUEUE_MAX_WAIT_SECONDS | core-api | tuning | number | 120 |  | .env.example:352 |  |
| PALM_MATCH_SESSION_DURATION_SECONDS | core-api | tuning | number | 300 |  | .env.example:353 |  |
