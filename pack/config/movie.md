# Environment variables MOVIE_*

id: config:MOVIE
secret values are never printed

| var | app | class | type/valid | default | required | set in | meaning |
|---|---|---|---|---|---|---|---|
| MOVIE_MATCH_URL_MAX_LENGTH | core-api | tuning | number | 2048 |  | .env.example:340, k8s/base/core-api/configmap.yaml:179 | Movie Match — Giai đoạn 5 (docs/services/movie-match-service.md § 8) |
| MOVIE_MATCH_ALLOWED_VIDEO_HOSTS | core-api | other | string | youtube.com,youtu.be |  | .env.example:342, k8s/base/core-api/configmap.yaml:180 | Danh sách phân tách dấu phẩy — parse mảng ở service (không parse ở Joi cho đơn giản) |
| MOVIE_MATCH_ANON_VIDEO_URLS | core-api | other | string | 'https://www.youtube.com/watch?v=aqz-KE-bpKQ,https://www.youtube.com/watch?v=eRsGyueVLvQ', |  | .env.example:344 | Flow ghép ẩn danh (movie-match.html): server chọn video từ danh sách này (phân tách dấu phẩy) |
| MOVIE_MATCH_ANON_DURATION_SECONDS | core-api | tuning | number | 1080 |  | .env.example:346 | 18:00 đúng timer badge mockup |
| MOVIE_MATCH_QUEUE_MAX_WAIT_SECONDS | core-api | tuning | number | 120 |  | .env.example:347 |  |
| MOVIE_MATCH_MESSAGE_MAX_LENGTH | core-api | tuning | number | 500 |  | .env.example:348 |  |
