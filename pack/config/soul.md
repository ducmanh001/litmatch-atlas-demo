# Environment variables SOUL_*

id: config:SOUL
secret values are never printed

| var | app | class | type/valid | default | required | set in | meaning |
|---|---|---|---|---|---|---|---|
| SOUL_CHAT_DURATION_SECONDS | core-api | tuning | number | 150 |  | .env.example:244, k8s/base/core-api/configmap.yaml:125 | Soul Match — Giai đoạn 2 (docs/services/soul-match-service.md § 6); default 2-3 phút theo docs/06 |
| SOUL_RATING_WINDOW_SECONDS | core-api | tuning | number | 120 |  | .env.example:246, k8s/base/core-api/configmap.yaml:126 |  |
| SOUL_CHAT_MESSAGE_MAX_LENGTH | core-api | tuning | number | 500 |  | .env.example:247, k8s/base/core-api/configmap.yaml:127 | .max khớp MESSAGE_CONTENT_HARD_CAP (sanity cap transport — soul-match.constants.ts) |
