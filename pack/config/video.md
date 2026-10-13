# Environment variables VIDEO_*

id: config:VIDEO
secret values are never printed

| var | app | class | type/valid | default | required | set in | meaning |
|---|---|---|---|---|---|---|---|
| VIDEO_CAPTION_MAX_LENGTH | core-api | tuning | number | 500 |  | .env.example:394 | Video ngắn — W5, hướng Momo (docs/services/short-video-service.md) |
| VIDEO_UPLOAD_ENABLED | core-api | switch | boolean | false |  | .env.example:396, deploy/hosted/core-api.env.example:57, deploy/production/compose.yml:96, k8s/base/core-api/configmap.yaml:185 | Fail-closed: upload chỉ mở khi môi trường chủ động bật và provider thật đã được tích hợp. |
| VIDEO_MODERATION_MODE | core-api | switch | string 'pre', 'post' | pre |  | .env.example:398 | pre = duyệt trước khi public (dating app VN, mặc định an toàn); post = public ngay, duyệt sau |
| VIDEO_QUALIFIED_VIEW_MIN_MS | core-api | tuning | number | 3000 |  | .env.example:399 | Watch-time tối thiểu để tính 1 view "qualified" — cộng Video.viewCount đúng 1 lần khi vượt ngưỡng |
| VIDEO_UPLOAD_TIMEOUT_SECONDS | core-api | tuning | number | 3600 |  | .env.example:400 | Video kẹt ở 'uploading' quá lâu (client bỏ dở/crash giữa chừng) → sweeper expire sang 'failed' |
| VIDEO_SWEEPER_INTERVAL_MS | core-api | tuning | number | 3600000 |  | .env.example:401 |  |
| VIDEO_REPORT_AUTOHIDE_THRESHOLD | core-api | tuning | number | 5 |  | .env.example:402 | Số distinct reporter để auto-hide (published → removed) — KHÔNG phải trust-score cá nhân |
| VIDEO_RANK_WEIGHT_VIEW | core-api | other | number | 1 |  | .env.example:403 | rankScore = view*W_VIEW + like*W_LIKE + comment*W_COMMENT, decay theo giờ kể từ createdAt |
| VIDEO_RANK_WEIGHT_LIKE | core-api | other | number | 3 |  | .env.example:404 |  |
| VIDEO_RANK_WEIGHT_COMMENT | core-api | other | number | 5 |  | .env.example:405 |  |
| VIDEO_RANK_TIME_DECAY_HOURS | core-api | tuning | number | 48 |  | .env.example:406 |  |
| VIDEO_RANKING_JOB_INTERVAL_MS | core-api | tuning | number | 1800000 |  | .env.example:407 |  |
