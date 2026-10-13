# Environment variables STREAK_*

id: config:STREAK
secret values are never printed

| var | app | class | type/valid | default | required | set in | meaning |
|---|---|---|---|---|---|---|---|
| STREAK_MILESTONE_DAYS | core-api | tuning | string | 3,7,14,30,50,100 |  | .env.example:257 | Streak trò chuyện (docs/services/streak-service.md, mở rộng module friend — W2) Mốc ngày chạm milestone (realtime + notification), phân tách dấu phẩy, tăng dần |
| STREAK_WARNING_HOURS | core-api | tuning | number | 20 |  | .env.example:259 | Giờ UTC trong ngày (0-23) — cron chỉ cảnh báo SAU mốc giờ này (gần hết ngày mà chưa nhắn) |
| STREAK_WARNING_CHECK_INTERVAL_MS | core-api | tuning | number | 3600000 |  | .env.example:260 |  |
