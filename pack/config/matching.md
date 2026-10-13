# Environment variables MATCHING_*

id: config:MATCHING
secret values are never printed

| var | app | class | type/valid | default | required | set in | meaning |
|---|---|---|---|---|---|---|---|
| MATCHING_MATCHER_INTERVAL_MS | core-api | tuning | number | 10000 |  | .env.example:216, deploy/hosted/core-api.env.example:41, k8s/base/core-api/configmap.yaml:101 | Matching — event wake-up là đường chính; interval chỉ là backstop thưa (docs/services/matching-service.md § 8) |
| MATCHING_MATCHER_BATCH_SIZE | core-api | tuning | number | 20 |  | .env.example:217, k8s/base/core-api/configmap.yaml:102 |  |
| MATCHING_SWEEPER_INTERVAL_MS | core-api | tuning | number | 5000 |  | .env.example:218, k8s/base/core-api/configmap.yaml:103 |  |
| MATCHING_QUEUE_MAX_WAIT_SECONDS | core-api | tuning | number | 120 |  | .env.example:219, k8s/base/core-api/configmap.yaml:104 |  |
| MATCHING_CONFIRM_TIMEOUT_SECONDS | core-api | tuning | number | 15 |  | .env.example:220, k8s/base/core-api/configmap.yaml:105 |  |
| MATCHING_AGE_BAND_SIZE | core-api | tuning | number | 5 |  | .env.example:221, k8s/base/core-api/configmap.yaml:106 |  |
| MATCHING_SPEEDUP_PRICE_DIAMOND | core-api | other | number | 50 |  | .env.example:222, k8s/base/core-api/configmap.yaml:107 |  |
| MATCHING_VIP_SPEEDUP_PRICE_DIAMOND | core-api | other | number | 40 |  | .env.example:223, k8s/base/core-api/configmap.yaml:108 |  |
| MATCHING_SVIP_SPEEDUP_PRICE_DIAMOND | core-api | other | number | 30 |  | .env.example:224, k8s/base/core-api/configmap.yaml:109 |  |
| MATCHING_PRIORITY_BOOST_MS | core-api | tuning | number | 300000 |  | .env.example:226, k8s/base/core-api/configmap.yaml:110 |  |
| MATCHING_TRUST_PENALTY_MS_PER_POINT | core-api | tuning | number | 2000 |  | .env.example:239, k8s/base/core-api/configmap.yaml:121 | Trust score < 100 làm chậm priority matching (docs/services/safety-service.md § 3.2) — KHÔNG chặn hẳn matching, chỉ làm "trẻ" ảo trong queue; ban thật là UserStatus.Banned |
| MATCHING_TRUST_PENALTY_MAX_MS | core-api | tuning | number | 120000 |  | .env.example:240, k8s/base/core-api/configmap.yaml:122 |  |
| MATCHING_GUEST_DAILY_LIMIT | core-api | tuning | number | 3 |  | .env.example:227, deploy/hosted/core-api.env.example:39, deploy/production/compose.yml:92, k8s/base/core-api/configmap.yaml:111 |  |
| MATCHING_GUEST_QUOTA_PEPPER | core-api | secret | string |  | yes | .env.example:228, deploy/production/.env.example:24, deploy/hosted/core-api.env.example:40, deploy/production/compose.yml:93, k8s/base/core-api/configmap.yaml:112 |  |
| MATCHING_DAILY_SOUL_LIMIT | core-api | tuning | number | 10 |  | .env.example:229, k8s/base/core-api/configmap.yaml:113 |  |
| MATCHING_DAILY_VOICE_LIMIT | core-api | tuning | number | 10 |  | .env.example:230, k8s/base/core-api/configmap.yaml:114 |  |
| MATCHING_VIP_DAILY_SOUL_LIMIT | core-api | tuning | number | 30 |  | .env.example:231, k8s/base/core-api/configmap.yaml:115 |  |
| MATCHING_VIP_DAILY_VOICE_LIMIT | core-api | tuning | number | 20 |  | .env.example:232, k8s/base/core-api/configmap.yaml:116 |  |
| MATCHING_SVIP_DAILY_SOUL_LIMIT | core-api | tuning | number | 60 |  | .env.example:233, k8s/base/core-api/configmap.yaml:117 |  |
| MATCHING_SVIP_DAILY_VOICE_LIMIT | core-api | tuning | number | 40 |  | .env.example:234, k8s/base/core-api/configmap.yaml:118 |  |
| MATCHING_EXTRA_SOUL_PRICE_DIAMOND | core-api | other | number | 20 |  | .env.example:235, k8s/base/core-api/configmap.yaml:119 |  |
| MATCHING_EXTRA_VOICE_PRICE_DIAMOND | core-api | other | number | 40 |  | .env.example:236, k8s/base/core-api/configmap.yaml:120 |  |
| MATCHING_INVITE_TTL_SECONDS | core-api | tuning | number | 3600 |  | .env.example:379 | CTA mời Voice/Soul Match — W4, mở rộng module `matching` (docs/services/matching-service.md § Invite) Invite hết hạn nếu người nhận không phản hồi — sweeper chuyển Pending → Expir… |
| MATCHING_INVITE_RATE_LIMIT_PER_HOUR | core-api | tuning | number | 10 |  | .env.example:381 | Đối xứng cho mọi user (không phân biệt giới tính trong logic) — chống spam mời hàng loạt |
| MATCHING_INVITE_SWEEPER_INTERVAL_MS | core-api | tuning | number | 60000 |  | .env.example:382 |  |
