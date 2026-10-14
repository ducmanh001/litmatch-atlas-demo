# Lookup: environment variables

id: lookup:env-vars

Variable name to the file with type, default, where it is set.

| id | file | note |
|---|---|---|
| ANALYTICS_ENABLED | ../config/analytics.md | core-api switch; default false; Server analytics — disabled by default; provider credentials are only require… |
| ANALYTICS_HTTP_TIMEOUT_MS | ../config/analytics.md | core-api tuning; default 5000 |
| ANALYTICS_PROVIDER | ../config/analytics.md | core-api switch; default disabled |
| AUTH_APPLE_CLIENT_ID | ../config/auth.md | core-api other; default - |
| AUTH_COOKIE_SAME_SITE | ../config/auth.md | core-api switch; default strict |
| AUTH_CROSS_ORIGIN_DEV | ../config/auth.md | core-api endpoint; default false |
| AUTH_FACEBOOK_APP_ID | ../config/auth.md | core-api other; default - |
| AUTH_FACEBOOK_APP_SECRET | ../config/auth.md | core-api secret; default - |
| AUTH_GOOGLE_CLIENT_ID | ../config/auth.md | core-api other; default - |
| AUTH_GUEST_DEVICE_TOKEN_SECRET | ../config/auth.md | core-api secret; default - |
| AUTH_GUEST_DEVICE_TOKEN_TTL_DAYS | ../config/auth.md | core-api secret; default - |
| AUTH_OTP_MAX_ATTEMPTS | ../config/auth.md | core-api tuning; default 5 |
| AUTH_OTP_PEPPER | ../config/auth.md | core-api secret; default - |
| AUTH_OTP_REQUESTS_PER_HOUR | ../config/auth.md | core-api tuning; default 5 |
| AUTH_OTP_TTL_SECONDS | ../config/auth.md | core-api tuning; default 300 |
| AUTH_PHONE_OTP_ENABLED | ../config/auth.md | core-api switch; default true |
| AUTH_REFRESH_TTL_DAYS | ../config/auth.md | core-api tuning; default 30 |
| AWS_ACCESS_KEY_ID | ../config/aws.md | core-api other; default - |
| AWS_REGION | ../config/aws.md | core-api other; default us-east-1 |
| AWS_S3_BUCKET | ../config/aws.md | core-api other; default - |
| AWS_S3_ENDPOINT | ../config/aws.md | core-api endpoint; default - |
| AWS_S3_FORCE_PATH_STYLE | ../config/aws.md | core-api other; default false |
| AWS_SECRET_ACCESS_KEY | ../config/aws.md | core-api secret; default - |
| CALLING_FREE_CALL_SECONDS | ../config/calling.md | core-api tuning; default 420 |
| CALLING_PENDING_TIMEOUT_SECONDS | ../config/calling.md | core-api tuning; default 60 |
| CALLING_RECONNECT_WINDOW_SECONDS | ../config/calling.md | core-api tuning; default 30 |
| CALLING_TICKER_INTERVAL_MS | ../config/calling.md | core-api tuning; default 1000 |
| CALLING_TOKEN_TTL_SECONDS | ../config/calling.md | core-api secret; default - |
| CAPABILITY_MAINTENANCE_FEATURES | ../config/misc.md | core-api switch; default - |
| CORS_ORIGINS | ../config/misc.md | core-api endpoint; default -; Task 0 (docs/12 § 12.7 point 3) — validate format lúc boot, không đợi tới enabl… |
| CORS_ORIGINS | ../config/misc.md | signaling-gateway endpoint; default - |
| DATABASE_POOL_ACQUIRE_TIMEOUT_MS | ../config/database.md | core-api tuning; default 10000; Thời hạn chờ lấy một kết nối khi pool đã đầy; hết hạn thì lỗi thay vì chờ vô … |
| DATABASE_POOL_MAX | ../config/database.md | core-api tuning; default 10; Số kết nối tối đa MỖI pod tới Postgres (pg-pool). Mặc định 10 = mặc định của dri… |
| DATABASE_URL | ../config/database.md | core-api endpoint; default - |
| DISCOVERY_AGE_BUCKETS | ../config/discovery.md | core-api other; default 18,25,31,41; Mốc tuổi tăng dần, phân tách dấu phẩy — bucket rộng, không lộ tuổi chính… |
| DISCOVERY_DISTANCE_BUCKETS_KM | ../config/discovery.md | core-api other; default 1,3,5,10,20; Mốc khoảng cách tăng dần (km), phân tách dấu phẩy — API chỉ trả bucket, … |
| DISCOVERY_GUEST_VISIBLE | ../config/discovery.md | core-api other; default false; Discovery — browse-only W1 (docs/services/discovery-service.md) Guest chưa gắn… |
| DISCOVERY_LOCATION_FRESHNESS_HOURS | ../config/discovery.md | core-api tuning; default 24; Vị trí quá hạn tự biến mất khỏi nearby (derive khi đọc, không cron dọn `user_loc… |
| DISCOVERY_LOCATION_QUANTIZE_DEGREES | ../config/discovery.md | core-api other; default 0.0045; Nearby — W4 (docs/services/discovery-service.md § Nearby) Lưới quantize toạ đ… |
| DISCOVERY_LOCATION_UPDATE_RATE_LIMIT_PER_HOUR | ../config/discovery.md | core-api tuning; default 12; Chống spam cập nhật vị trí (rò rỉ lộ trình di chuyển) — số lần ghi vị trí tối đa… |
| DISCOVERY_NEARBY_CANDIDATE_CAP | ../config/discovery.md | core-api other; default 500; Trần an toàn số candidate lấy từ DB trước khi sort/paginate ở app (MVP: bounding… |
| DISCOVERY_NEARBY_QUERY_RATE_LIMIT_PER_HOUR | ../config/discovery.md | core-api tuning; default 30; Chống dò quét trilateration bằng nhiều truy vấn liên tiếp — số lần truy vấn near… |
| DISCOVERY_NEARBY_RADIUS_KM | ../config/discovery.md | core-api other; default 20; Bán kính tìm kiếm tối đa — dùng làm bounding-box prefilter trước khi tính haversi… |
| ECONOMY_APPLE_BUNDLE_ID | ../config/economy.md | core-api other; default -; Webhook Apple chỉ chứng minh "Apple ký", bundleId mới chứng minh "ký cho app này";… |
| ECONOMY_APPLE_ISSUER_ID | ../config/economy.md | core-api other; default - |
| ECONOMY_APPLE_KEY_ID | ../config/economy.md | core-api other; default - |
| ECONOMY_APPLE_PRIVATE_KEY | ../config/economy.md | core-api secret; default - |
| ECONOMY_APPLE_ROOT_CA_PEM | ../config/economy.md | core-api secret; default - |
| ECONOMY_APPLE_SERVER_API_ENV | ../config/economy.md | core-api switch; default sandbox |
| ECONOMY_APPLE_SHARED_SECRET | ../config/economy.md | core-api secret; default - |
| ECONOMY_APPLE_WEBHOOK_VERIFIER | ../config/economy.md | core-api switch; default store; Refund/chargeback (docs/services/economy-service.md § 5) Mặc định 'store' (fa… |
| ECONOMY_GOOGLE_PACKAGE_NAME | ../config/economy.md | core-api other; default - |
| ECONOMY_GOOGLE_RTDN_AUDIENCE | ../config/economy.md | core-api other; default - |
| ECONOMY_GOOGLE_RTDN_SERVICE_ACCOUNT_EMAIL | ../config/economy.md | core-api other; default - |
| ECONOMY_GOOGLE_RTDN_VERIFIER | ../config/economy.md | core-api switch; default store |
| ECONOMY_GOOGLE_SA_EMAIL | ../config/economy.md | core-api other; default - |
| ECONOMY_GOOGLE_SA_PRIVATE_KEY | ../config/economy.md | core-api secret; default - |
| ECONOMY_IAP_VERIFIER | ../config/economy.md | core-api switch; default dev |
| ECONOMY_OUTBOX_MAX_ATTEMPTS | ../config/economy.md | core-api tuning; default 5 |
| ECONOMY_OUTBOX_RELAY_ENABLED | ../config/economy.md | core-api switch; default false |
| ECONOMY_OUTBOX_RELAY_INTERVAL_MS | ../config/economy.md | core-api tuning; default 2000 |
| ECONOMY_RECONCILIATION_ENABLED | ../config/economy.md | core-api switch; default true |
| ECONOMY_RECONCILIATION_FAST_INTERVAL_MS | ../config/economy.md | core-api tuning; default 60000; Tier fast (bất biến Nợ=Có + orphan receipt) chỉ là 1 câu aggregate + 1 câu CO… |
| ECONOMY_RECONCILIATION_INTERVAL_MS | ../config/economy.md | core-api tuning; default 300000 |
| ECONOMY_REFUND_POLL_ENABLED | ../config/economy.md | core-api switch; default false |
| ECONOMY_REFUND_POLL_INTERVAL_MS | ../config/economy.md | core-api tuning; default 3600000 |
| ECONOMY_REFUND_POLL_WINDOW_DAYS | ../config/economy.md | core-api tuning; default 60 |
| ECONOMY_STORE_HTTP_TIMEOUT_MS | ../config/economy.md | core-api tuning; default 10000 |
| EVENT_BUS_CONSUMER_MAX_ATTEMPTS | ../config/event.md | core-api tuning; default 3 |
| EVENT_BUS_CONSUMER_RETRY_DELAY_MS | ../config/event.md | core-api tuning; default 250 |
| EVENT_BUS_KAFKA_REQUEST_TIMEOUT_MS | ../config/event.md | core-api tuning; default 5000 |
| EVENT_BUS_KAFKA_RETRIES | ../config/event.md | core-api other; default 1 |
| FRIEND_MESSAGE_MAX_LENGTH | ../config/misc.md | core-api tuning; default 2000; Friend Chat 1-1 — Giai đoạn 2 (docs/services/friend-service.md § 7); chat lâu … |
| GIFT_POINTS_RATE_PERCENT | ../config/misc.md | core-api other; default 40; Gift — Giai đoạn 3 (docs/services/gift-service.md); tỉ lệ quy đổi DIA→PTS cho ngư… |
| HTTP_TRUST_PROXY_HOPS | ../config/misc.md | core-api other; default 0; Chỉ trust đúng số proxy do operator kiểm soát; 0 = kết nối trực tiếp, không tin X-… |
| JWT_ACCESS_TTL_SECONDS | ../config/jwt.md | core-api tuning; default 900 |
| JWT_SECRET | ../config/jwt.md | core-api secret; default - |
| JWT_SECRET | ../config/jwt.md | signaling-gateway secret; default - |
| JWT_SECRET_PREVIOUS | ../config/jwt.md | core-api secret; default -; Không được trùng khoá hiện tại và cũng không được trùng khoá của guest device tok… |
| JWT_SECRET_PREVIOUS | ../config/jwt.md | signaling-gateway secret; default - |
| KAFKA_BROKERS | ../config/misc.md | core-api other; default localhost:9092 |
| LIVEKIT_API_KEY | ../config/livekit.md | core-api secret; default - |
| LIVEKIT_API_SECRET | ../config/livekit.md | core-api secret; default - |
| LIVEKIT_API_URL | ../config/livekit.md | core-api endpoint; default -; Endpoint server-to-server (RoomServiceClient: createRoom/deleteRoom/updateParti… |
| LIVEKIT_REGION_URLS | ../config/livekit.md | core-api other; default -; GĐ7 multi-region (ADR 0005): JSON map region (User.region) → ws/wss URL edge LiveK… |
| LIVEKIT_URL | ../config/livekit.md | core-api endpoint; default ws://localhost:7880; Calling — Giai đoạn 2 (docs/services/calling-service.md § 6);… |
| MATCHING_AGE_BAND_SIZE | ../config/matching.md | core-api tuning; default 5 |
| MATCHING_CONFIRM_TIMEOUT_SECONDS | ../config/matching.md | core-api tuning; default 15 |
| MATCHING_DAILY_SOUL_LIMIT | ../config/matching.md | core-api tuning; default 10 |
| MATCHING_DAILY_VOICE_LIMIT | ../config/matching.md | core-api tuning; default 10 |
| MATCHING_EXTRA_SOUL_PRICE_DIAMOND | ../config/matching.md | core-api other; default 20 |
| MATCHING_EXTRA_VOICE_PRICE_DIAMOND | ../config/matching.md | core-api other; default 40 |
| MATCHING_GUEST_DAILY_LIMIT | ../config/matching.md | core-api tuning; default 3 |
| MATCHING_GUEST_QUOTA_PEPPER | ../config/matching.md | core-api secret; default - |
| MATCHING_INVITE_RATE_LIMIT_PER_HOUR | ../config/matching.md | core-api tuning; default 10; Đối xứng cho mọi user (không phân biệt giới tính trong logic) — chống spam mời h… |
| MATCHING_INVITE_SWEEPER_INTERVAL_MS | ../config/matching.md | core-api tuning; default 60000 |
| MATCHING_INVITE_TTL_SECONDS | ../config/matching.md | core-api tuning; default 3600; CTA mời Voice/Soul Match — W4, mở rộng module `matching` (docs/services/matchi… |
| MATCHING_MATCHER_BATCH_SIZE | ../config/matching.md | core-api tuning; default 20 |
| MATCHING_MATCHER_INTERVAL_MS | ../config/matching.md | core-api tuning; default 10000; Matching — event wake-up là đường chính; interval chỉ là backstop thưa (docs/… |
| MATCHING_PRIORITY_BOOST_MS | ../config/matching.md | core-api tuning; default 300000 |
| MATCHING_QUEUE_MAX_WAIT_SECONDS | ../config/matching.md | core-api tuning; default 120 |
| MATCHING_SPEEDUP_PRICE_DIAMOND | ../config/matching.md | core-api other; default 50 |
| MATCHING_SVIP_DAILY_SOUL_LIMIT | ../config/matching.md | core-api tuning; default 60 |
| MATCHING_SVIP_DAILY_VOICE_LIMIT | ../config/matching.md | core-api tuning; default 40 |
| MATCHING_SVIP_SPEEDUP_PRICE_DIAMOND | ../config/matching.md | core-api other; default 30 |
| MATCHING_SWEEPER_INTERVAL_MS | ../config/matching.md | core-api tuning; default 5000 |
| MATCHING_TRUST_PENALTY_MAX_MS | ../config/matching.md | core-api tuning; default 120000 |
| MATCHING_TRUST_PENALTY_MS_PER_POINT | ../config/matching.md | core-api tuning; default 2000; Trust score < 100 làm chậm priority matching (docs/services/safety-service.md … |
| MATCHING_VIP_DAILY_SOUL_LIMIT | ../config/matching.md | core-api tuning; default 30 |
| MATCHING_VIP_DAILY_VOICE_LIMIT | ../config/matching.md | core-api tuning; default 20 |
| MATCHING_VIP_SPEEDUP_PRICE_DIAMOND | ../config/matching.md | core-api other; default 40 |
| MEDIA_ALLOWED_IMAGE_TYPES | ../config/media.md | core-api other; default image/jpeg,image/png,image/webp,image/gif |
| MEDIA_IMAGE_MAX_BYTES | ../config/media.md | core-api tuning; default 10 * 1024 * 1024 |
| MEDIA_PUBLIC_BASE_URL | ../config/media.md | core-api endpoint; default - |
| MEDIA_R2_ACCESS_KEY_ID | ../config/media.md | core-api other; default - |
| MEDIA_R2_ACCOUNT_ID | ../config/media.md | core-api other; default - |
| MEDIA_R2_BUCKET | ../config/media.md | core-api other; default - |
| MEDIA_R2_SECRET_ACCESS_KEY | ../config/media.md | core-api secret; default - |
| MEDIA_STORAGE_PROVIDER | ../config/media.md | core-api switch; default dev; Image uploads — dev adapter mặc định; production phải dùng storage cloud thật. |
| MEDIA_UPLOAD_URL_TTL_SECONDS | ../config/media.md | core-api tuning; default 900 |
| MOOD_STATUS_TTL_HOURS | ../config/misc.md | core-api tuning; default 24; Mood — preset-only W1 (docs/services/mood-service.md) TTL mood tính từ lúc set (… |
| MOVIE_MATCH_ALLOWED_VIDEO_HOSTS | ../config/movie.md | core-api other; default youtube.com,youtu.be; Danh sách phân tách dấu phẩy — parse mảng ở service (không pars… |
| MOVIE_MATCH_ANON_DURATION_SECONDS | ../config/movie.md | core-api tuning; default 1080; 18:00 đúng timer badge mockup |
| MOVIE_MATCH_ANON_VIDEO_URLS | ../config/movie.md | core-api other; default 'https://www.youtube.com/watch?v=aqz-KE-bpKQ,https://www.youtube.com/watch?v=eRsGyueV… |
| MOVIE_MATCH_MESSAGE_MAX_LENGTH | ../config/movie.md | core-api tuning; default 500 |
| MOVIE_MATCH_QUEUE_MAX_WAIT_SECONDS | ../config/movie.md | core-api tuning; default 120 |
| MOVIE_MATCH_URL_MAX_LENGTH | ../config/movie.md | core-api tuning; default 2048; Movie Match — Giai đoạn 5 (docs/services/movie-match-service.md § 8) |
| NOTIFICATION_APNS_BUNDLE_ID | ../config/notification.md | core-api other; default - |
| NOTIFICATION_APNS_ENVIRONMENT | ../config/notification.md | core-api switch; default sandbox |
| NOTIFICATION_APNS_KEY_ID | ../config/notification.md | core-api other; default - |
| NOTIFICATION_APNS_PRIVATE_KEY | ../config/notification.md | core-api secret; default - |
| NOTIFICATION_APNS_TEAM_ID | ../config/notification.md | core-api other; default - |
| NOTIFICATION_FCM_CLIENT_EMAIL | ../config/notification.md | core-api other; default - |
| NOTIFICATION_FCM_PRIVATE_KEY | ../config/notification.md | core-api secret; default - |
| NOTIFICATION_FCM_PROJECT_ID | ../config/notification.md | core-api other; default - |
| NOTIFICATION_PUSH_HTTP_TIMEOUT_MS | ../config/notification.md | core-api tuning; default 5000 |
| NOTIFICATION_PUSH_PROVIDER | ../config/notification.md | core-api switch; default dev; Notification — Giai đoạn 4 (docs/services/notification-service.md § 4) 'dev' (n… |
| PALM_MATCH_QUEUE_MAX_WAIT_SECONDS | ../config/palm.md | core-api tuning; default 120 |
| PALM_MATCH_SESSION_DURATION_SECONDS | ../config/palm.md | core-api tuning; default 300 |
| PALM_MATCH_TARGET_NAME_MAX_LENGTH | ../config/palm.md | core-api tuning; default 50; Palm Match — Giai đoạn 5 (docs/services/palm-match-service.md § 5) |
| PARTY_COMMENT_MAX_LENGTH | ../config/party.md | core-api tuning; default 300 |
| PARTY_EMPTY_ROOM_TIMEOUT_SECONDS | ../config/party.md | core-api tuning; default 300; LiveKit tự đóng room trống sau N giây — backstop tầng SFU cho phòng vô chủ |
| PARTY_HOST_DISCONNECT_GRACE_SECONDS | ../config/party.md | core-api tuning; default 15; Host rớt kết nối NGOÀI Ý MUỐN (webhook participant_left) — chờ tự kết nối lại tr… |
| PARTY_HOST_GRACE_CHECK_INTERVAL_MS | ../config/party.md | core-api tuning; default 5000; Tần suất quét phòng hết grace — tách riêng PARTY_SWEEPER_INTERVAL_MS (30s, bac… |
| PARTY_MAX_MEMBERS | ../config/party.md | core-api tuning; default 100; Truyền vào LiveKit maxParticipants lúc tạo room — SFU tự chặn join vượt ngưỡng |
| PARTY_MAX_SPEAKERS | ../config/party.md | core-api tuning; default 8; Party Room — Giai đoạn 3 (docs/services/party-room-service.md § 7) Cap speaker là… |
| PARTY_MEMBER_DISCONNECT_GRACE_SECONDS | ../config/party.md | core-api tuning; default 15; Member rớt mạng ngoài ý muốn còn hiển thị trong roster trong thời gian ngắn để r… |
| PARTY_STALE_ROOM_SECONDS | ../config/party.md | core-api tuning; default 120; Phòng active mà không còn member active quá N giây → sweeper đóng (webhook có t… |
| PARTY_SWEEPER_INTERVAL_MS | ../config/party.md | core-api tuning; default 30000 |
| PARTY_TITLE_MAX_LENGTH | ../config/party.md | core-api tuning; default 100 |
| PARTY_TOKEN_TTL_SECONDS | ../config/party.md | core-api secret; default - |
| PAYOS_API_BASE_URL | ../config/payos.md | core-api endpoint; default https://api-merchant.payos.vn |
| PAYOS_API_KEY | ../config/payos.md | core-api secret; default - |
| PAYOS_CANCEL_URL | ../config/payos.md | core-api endpoint; default - |
| PAYOS_CHECKSUM_KEY | ../config/payos.md | core-api secret; default - |
| PAYOS_CLIENT_ID | ../config/payos.md | core-api other; default -; payOS web top-up: credential chỉ ở core-api; URL callback chỉ phục vụ UX, không cr… |
| PAYOS_HTTP_TIMEOUT_MS | ../config/payos.md | core-api tuning; default 10000 |
| PAYOS_ORDER_EXPIRES_SECONDS | ../config/payos.md | core-api tuning; default 900 |
| PAYOS_RETURN_URL | ../config/payos.md | core-api endpoint; default - |
| PAYOS_WEB_WALLET_URL | ../config/payos.md | core-api endpoint; default - |
| PORT | ../config/misc.md | core-api other; default 3000 |
| POSTHOG_HOST | ../config/misc.md | core-api endpoint; default https://us.i.posthog.com |
| POSTHOG_PROJECT_TOKEN | ../config/misc.md | core-api secret; default - |
| PROFILE_DIRECT_MESSAGE_DAILY_FIRST_CHAT_THRESHOLD | ../config/misc.md | core-api tuning; default 10; Profile social actions — từ người bắt chuyện đầu tiên thứ N+1 trong ngày UTC, mở… |
| REDIS_URL | ../config/misc.md | core-api endpoint; default redis://localhost:6379 |
| REDIS_URL | ../config/misc.md | signaling-gateway endpoint; default redis://localhost:6379 |
| SAFETY_REMATCH_COOLDOWN_DAYS | ../config/safety.md | core-api tuning; default 30; Safety — Giai đoạn 4 (docs/services/safety-service.md) Không ghép lại nếu có rep… |
| SAFETY_REPORT_COOLDOWN_DAYS | ../config/safety.md | core-api tuning; default 7; 1 cặp (reporter, target) chỉ tính 1 report hiệu lực lên trust score mỗi X ngày — … |
| SAFETY_TRUST_PENALTY_DAILY_CAP | ../config/safety.md | core-api other; default 20; Trần tổng điểm trừ/ngày cho 1 target — chặn nhiều reporter khác nhau đánh sập tru… |
| SAFETY_TRUST_PENALTY_PER_REPORT | ../config/safety.md | core-api tuning; default 5 |
| SAFETY_TRUST_SCORE_FLOOR | ../config/safety.md | core-api other; default 0 |
| SENTRY_DSN | ../config/sentry.md | core-api secret; default - |
| SENTRY_DSN | ../config/sentry.md | signaling-gateway secret; default - |
| SENTRY_RELEASE | ../config/sentry.md | core-api other; default - |
| SENTRY_RELEASE | ../config/sentry.md | signaling-gateway other; default - |
| SIGNALING_PORT | ../config/misc.md | signaling-gateway other; default 3001 |
| SOUL_CHAT_DURATION_SECONDS | ../config/soul.md | core-api tuning; default 150; Soul Match — Giai đoạn 2 (docs/services/soul-match-service.md § 6); default 2-3… |
| SOUL_CHAT_MESSAGE_MAX_LENGTH | ../config/soul.md | core-api tuning; default 500; .max khớp MESSAGE_CONTENT_HARD_CAP (sanity cap transport — soul-match.constants… |
| SOUL_RATING_WINDOW_SECONDS | ../config/soul.md | core-api tuning; default 120 |
| STORY_SWEEPER_INTERVAL_MS | ../config/misc.md | core-api tuning; default 3600000 |
| STORY_TTL_HOURS | ../config/misc.md | core-api tuning; default 24; Stories — W3 (docs/services/feed-service.md § 8) TTL story tính từ lúc tạo (snap… |
| STREAK_MILESTONE_DAYS | ../config/streak.md | core-api tuning; default 3,7,14,30,50,100; Streak trò chuyện (docs/services/streak-service.md, mở rộng module… |
| STREAK_WARNING_CHECK_INTERVAL_MS | ../config/streak.md | core-api tuning; default 3600000 |
| STREAK_WARNING_HOURS | ../config/streak.md | core-api tuning; default 20; Giờ UTC trong ngày (0-23) — cron chỉ cảnh báo SAU mốc giờ này (gần hết ngày mà c… |
| SWAGGER_ENABLED | ../config/misc.md | core-api switch; default true |
| THROTTLE_LIMIT | ../config/throttle.md | core-api tuning; default 100 |
| THROTTLE_STORAGE | ../config/throttle.md | core-api switch; default memory; memory: bộ đếm theo từng process (đủ cho 1 pod/dev). redis: bộ đếm dùng chun… |
| THROTTLE_TTL_SECONDS | ../config/throttle.md | core-api tuning; default 60 |
| USER_DEFAULT_AVATAR_ID | ../config/misc.md | core-api other; default default-01 |
| VIDEO_CAPTION_MAX_LENGTH | ../config/video.md | core-api tuning; default 500; Video ngắn — W5, hướng Momo (docs/services/short-video-service.md) |
| VIDEO_MODERATION_MODE | ../config/video.md | core-api switch; default pre; pre = duyệt trước khi public (dating app VN, mặc định an toàn); post = public n… |
| VIDEO_QUALIFIED_VIEW_MIN_MS | ../config/video.md | core-api tuning; default 3000; Watch-time tối thiểu để tính 1 view "qualified" — cộng Video.viewCount đúng 1 … |
| VIDEO_RANKING_JOB_INTERVAL_MS | ../config/video.md | core-api tuning; default 1800000 |
| VIDEO_RANK_TIME_DECAY_HOURS | ../config/video.md | core-api tuning; default 48 |
| VIDEO_RANK_WEIGHT_COMMENT | ../config/video.md | core-api other; default 5 |
| VIDEO_RANK_WEIGHT_LIKE | ../config/video.md | core-api other; default 3 |
| VIDEO_RANK_WEIGHT_VIEW | ../config/video.md | core-api other; default 1; rankScore = view*W_VIEW + like*W_LIKE + comment*W_COMMENT, decay theo giờ kể từ cr… |
| VIDEO_REPORT_AUTOHIDE_THRESHOLD | ../config/video.md | core-api tuning; default 5; Số distinct reporter để auto-hide (published → removed) — KHÔNG phải trust-score … |
| VIDEO_SWEEPER_INTERVAL_MS | ../config/video.md | core-api tuning; default 3600000 |
| VIDEO_UPLOAD_ENABLED | ../config/video.md | core-api switch; default false; Fail-closed: upload chỉ mở khi môi trường chủ động bật và provider thật đã đư… |
| VIDEO_UPLOAD_TIMEOUT_SECONDS | ../config/video.md | core-api tuning; default 3600; Video kẹt ở 'uploading' quá lâu (client bỏ dở/crash giữa chừng) → sweeper expi… |
| WEB_PUSH_ENABLED | ../config/web.md | core-api switch; default false; Browser Web Push — tắt mặc định; khi bật production phải có đủ VAPID credenti… |
| WEB_PUSH_PRIVATE_KEY | ../config/web.md | core-api secret; default - |
| WEB_PUSH_PUBLIC_KEY | ../config/web.md | core-api secret; default - |
| WEB_PUSH_SUBJECT | ../config/web.md | core-api other; default - |
| WS_CONNECTION_LEASE_MS | ../config/misc.md | signaling-gateway tuning; default 90000 |
| WS_MAX_CONNECTIONS_PER_USER | ../config/misc.md | signaling-gateway tuning; default 3 |
