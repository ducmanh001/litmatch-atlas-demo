# Environment variables SAFETY_*

id: config:SAFETY
secret values are never printed

| var | app | class | type/valid | default | required | set in | meaning |
|---|---|---|---|---|---|---|---|
| SAFETY_REMATCH_COOLDOWN_DAYS | core-api | tuning | number | 30 |  | .env.example:313, k8s/base/core-api/configmap.yaml:162 | Safety — Giai đoạn 4 (docs/services/safety-service.md) Không ghép lại nếu có report/block giữa 2 user trong X ngày gần nhất (docs/06) |
| SAFETY_REPORT_COOLDOWN_DAYS | core-api | tuning | number | 7 |  | .env.example:315, k8s/base/core-api/configmap.yaml:163 | 1 cặp (reporter, target) chỉ tính 1 report hiệu lực lên trust score mỗi X ngày — chống spam report cùng 1 người |
| SAFETY_TRUST_PENALTY_PER_REPORT | core-api | tuning | number | 5 |  | .env.example:316, k8s/base/core-api/configmap.yaml:164 |  |
| SAFETY_TRUST_PENALTY_DAILY_CAP | core-api | other | number | 20 |  | .env.example:318, k8s/base/core-api/configmap.yaml:165 | Trần tổng điểm trừ/ngày cho 1 target — chặn nhiều reporter khác nhau đánh sập trust score cùng lúc |
| SAFETY_TRUST_SCORE_FLOOR | core-api | other | number | 0 |  | .env.example:319, k8s/base/core-api/configmap.yaml:166 |  |
