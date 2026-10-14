# Async flows: realtime, outbox, jobs, queues: transports

id: system:async:transports

Written by an AI agent from the code; every file:line quote was checked automatically (exists within ±3 lines) but NO independent verification pass has run yet. Treat claims as strong leads; open the cited file before editing.

### redis-pubsub-realtime

- name: Redis Pub/Sub — kênh realtime theo người nhận
- purpose: core-api publish envelope `{ event, data }` vào channel `realtime:user:{userId}`; signaling-gateway PSUBSCRIBE `realtime:user:*` để đẩy xuống socket. Hợp đồng tên event/payload nằm ở libs/common-dtos/src/lib/realtime-events.ts.
- deliverySemantics: At-most-once, fire-and-forget: Redis Pub/Sub không lưu tin; publish lỗi bị nuốt và chỉ log warn nên nghiệp vụ đã commit không bị ảnh hưởng. Không outbox cho luồng này (dual-write chấp nhận có chủ đích).
- ordering: Envelope chỉ có `event` + `data`, không có số thứ tự hay khóa; thứ tự giao phụ thuộc thứ tự PUBLISH của Redis và code không bảo đảm gì xuyên pod hay xuyên module.
- failureBehavior: Client Redis của core-api đặt connectTimeout/commandTimeout 1s, maxRetriesPerRequest 1 và tắt offline queue nên publish khi Redis down thất bại nhanh (không tích lũy RAM) rồi bị log warn; client phải dựa vào REST polling.
- replay: Không có: không lưu, không connection-state-recovery; sau reconnect web gọi invalidateQueries() toàn bộ để đồng bộ lại từ REST.
- profileSwitches: REDIS_URL (core-api, mặc định redis://localhost:6379; signaling-gateway dùng cùng Redis); Không có cờ bật/tắt realtime; hosted-free dùng Redis trên Upstash (docs/03-architecture.md § profile hosted)
- whereDefined (evidence): libs/common-dtos/src/lib/realtime-events.ts:13, libs/common-dtos/src/lib/realtime-events.ts:102, apps/core-api/src/common/realtime/publish-realtime.ts:24, apps/core-api/src/common/realtime/publish-realtime.ts:14, apps/core-api/src/common/realtime/redis-realtime-publisher.adapter.ts:6, apps/core-api/src/common/redis/core-redis-client.ts:52, apps/core-api/src/common/redis/core-redis-client.ts:50, docs/03-architecture.md:168, apps/signaling-gateway/src/app/signaling.gateway.ts:346

### socketio-gateway

- name: Socket.IO signaling-gateway (namespace /signaling)
- purpose: Verify JWT lúc handshake, cấp quota kết nối theo user bằng lease Redis, tự join socket vào room `user:{sub}` và relay nguyên văn envelope Redis → socket; không chứa business logic và không query DB.
- deliverySemantics: At-most-once tới socket đang nối vào pod nhận pmessage: không ACK, không buffer cho socket offline, không đảm bảo giao. Mỗi pod nhận mọi event và chỉ emit vào room cục bộ (server.local) nên mỗi socket nhận đúng một bản.
- ordering: Theo thứ tự pmessage trên subscriber connection của từng pod; gateway không gán thêm số thứ tự.
- failureBehavior: Pod mất PSUBSCRIBE (readiness `redisSubscription: down`) thì socket trên pod đó không nhận event tới khi phục hồi hoặc reconnect sang pod khác. Quota Redis lỗi hoặc refresh lease lỗi thì fail closed (từ chối handshake `CONNECTION_QUOTA_UNAVAILABLE` hoặc ngắt socket); JWT sai thì `UNAUTHORIZED`.
- replay: Không có (không bật connectionStateRecovery); web refetch REST khi sự kiện reconnect.
- profileSwitches: SIGNALING_PORT (mặc định 3001); CORS_ORIGINS (allow-list cho cả polling và websocket handshake); JWT_SECRET (bắt buộc, trùng core-api) và JWT_SECRET_PREVIOUS (chỉ trong cửa sổ xoay khóa); REDIS_URL (mặc định redis://localhost:6379); WS_MAX_CONNECTIONS_PER_USER (mặc định 3, tối đa 3); WS_CONNECTION_LEASE_MS (mặc định 90000, 10000–300000); Web: NEXT_PUBLIC_SOCKET_URL; client cố định transports: [websocket] nên không cần sticky session
- whereDefined (evidence): apps/signaling-gateway/src/app/signaling.gateway.ts:46, apps/signaling-gateway/src/app/signaling.gateway.ts:332, apps/signaling-gateway/src/app/signaling.gateway.ts:173, apps/signaling-gateway/src/app/signaling.gateway.ts:258, apps/signaling-gateway/src/app/connection-quota.service.ts:15, apps/signaling-gateway/src/app/redis-adapter.service.ts:46, apps/signaling-gateway/src/app/health.controller.ts:40, docs/services/realtime-gateway.md:100, apps/web/src/shared/realtime/socket.ts:48, apps/signaling-gateway/src/config/env.validation.ts:43

### kafka-outbox

- name: Transactional outbox → Kafka (Economy) + DLQ
- purpose: Event bền của Economy được ghi vào `outbox_events` cùng DB transaction với ledger; OutboxRelayService publish lên topic `litmatch.economy.events` qua EventBusPort/KafkaEventBusAdapter. Hiện chưa có consumer trong repo.
- deliverySemantics: At-least-once: publish xảy ra trước COMMIT của dòng outbox nên crash giữa chừng sẽ publish lại; producer idempotent chỉ khử retry nội bộ. Envelope mang `id` ổn định để consumer tương lai dedupe, chưa có inbox.
- ordering: Khóa Kafka = payload.transactionId, nếu không có thì payload.userId, nếu không thì id dòng. Có thứ tự theo khóa trong một partition; không có thứ tự toàn cục và không có thứ tự theo user giữa các giao dịch khác nhau.
- failureBehavior: publish lỗi → attempts + 1 mỗi tick; đủ ECONOMY_OUTBOX_MAX_ATTEMPTS (5) → gửi `<topic>.DLQ` rồi đặt dead_lettered_at (kể cả khi DLQ cũng lỗi). Relay giữ khóa dòng suốt lúc publish nên broker chậm/down kéo dài giữ DB connection và khóa.
- replay: Dòng giữ nguyên trong `outbox_events` (published_at NULL khi relay tắt; dead_lettered_at khi terminal) làm nguồn replay; repo không có script/job replay hay retention (đã grep scripts/ và docs/runbooks).
- profileSwitches: ECONOMY_OUTBOX_RELAY_ENABLED (mặc định false — cả .env.example, k8s, hosted, production compose đều false); ECONOMY_OUTBOX_RELAY_INTERVAL_MS (2000), ECONOMY_OUTBOX_MAX_ATTEMPTS (5); KAFKA_BROKERS (mặc định localhost:9092), EVENT_BUS_KAFKA_REQUEST_TIMEOUT_MS (5000), EVENT_BUS_KAFKA_RETRIES (1); EVENT_BUS_CONSUMER_MAX_ATTEMPTS (3), EVENT_BUS_CONSUMER_RETRY_DELAY_MS (250) — chỉ cho consumer, hiện chưa dùng
- whereDefined (evidence): apps/core-api/src/modules/economy/economy.constants.ts:11, apps/core-api/src/modules/economy/jobs/outbox-relay.service.ts:82, apps/core-api/src/common/events/kafka-event-bus.adapter.ts:189, apps/core-api/src/common/events/kafka-event-bus.adapter.ts:188, apps/core-api/src/common/events/events.module.ts:10, apps/core-api/src/config/env.validation.ts:265, apps/core-api/src/config/env.validation.ts:372, docs/runbooks/hosted-free-release.md:56

### redis-zset-matching-queue

- name: Redis ZSET — hàng đợi ghép cặp Voice/Soul (dẫn xuất từ Postgres)
- purpose: Mỗi shard `matching:queue:{matchType}:{region}:{ageBand}` là sorted set (member = ticketId, score = enqueuedAt + trustPenaltyMs − priorityBoostMs); set `matching:shards:active` liệt kê shard có ticket. Postgres `match_tickets` mới là nguồn sự thật, matcher luôn verify lại dưới khóa FOR UPDATE.
- deliverySemantics: Work queue pop-một-lần: ZPOPMIN atomic nên hai matcher không lấy trùng ticket. Không ACK: ticket đã pop mà tryPair chưa requeue sẽ vắng mặt khỏi Redis (DB vẫn queued) cho tới khi client retry join hoặc sweeper expire.
- ordering: Score tăng dần (chờ lâu/được boost trước, phạt trust cộng thêm); requeue dùng đúng score đã pop nên không mất lượt chờ.
- failureBehavior: ZADD sau commit lỗi → ticket thành "zombie DB" (client retry cùng Idempotency-Key sẽ re-enqueue; sweeper expire sau MATCHING_QUEUE_MAX_WAIT_SECONDS). Redis mất dữ liệu (vd restore) thì ZSET rỗng trong khi DB còn ticket queued — cùng cơ chế phục hồi.
- replay: ensureEnqueued (ZADD NX) khi client replay join cùng Idempotency-Key; sweeper requeue bên đã confirm bằng ticket mới.
- profileSwitches: REDIS_URL; MATCHING_MATCHER_INTERVAL_MS (10000) — chỉ là backstop; MATCHING_MATCHER_BATCH_SIZE (20); MATCHING_SWEEPER_INTERVAL_MS (5000), MATCHING_QUEUE_MAX_WAIT_SECONDS (120)
- whereDefined (evidence): apps/core-api/src/modules/matching/redis/matching-redis.provider.ts:21, apps/core-api/src/modules/matching/redis/matching-redis.provider.ts:29, apps/core-api/src/modules/matching/redis/matching-redis.provider.ts:50, apps/core-api/src/modules/matching/redis/redis-matching-queue.adapter.ts:26, apps/core-api/src/modules/matching/matching.service.ts:198, apps/core-api/src/config/env.validation.ts:468

### pg-queue-tables

- name: Bảng Postgres dùng làm hàng đợi (Movie Match, Palm Match, match_tickets)
- purpose: Movie Match ẩn danh (`movie_match_queue_entries`) và Palm Match (`palm_match_queue_entries`) ghép cặp ngay trong transaction của request join dưới advisory lock, không có worker nền và không phát realtime event khi ghép được cặp. `match_tickets` là bảng trạng thái bền của hàng đợi Voice/Soul.
- deliverySemantics: Giao dịch Postgres: enqueue idempotent (INSERT ... ON CONFLICT DO NOTHING theo user_id), chọn tối đa 20 ứng viên cũ nhất, kiểm block/report, tạo session và xóa hai entry trong cùng transaction. Không có ACK/retry vì không có consumer.
- ordering: queued_at ASC, user_id ASC, LIMIT 20 ứng viên mỗi lần join; advisory lock `pg_advisory_xact_lock(hashtext(key))` serialize toàn bộ matcher của từng tính năng.
- failureBehavior: Entry hết hạn (MOVIE_MATCH_QUEUE_MAX_WAIT_SECONDS / PALM_MATCH_QUEUE_MAX_WAIT_SECONDS, 120s) chỉ bị xóa lazily khi có join/GET current kế tiếp; không có job dọn nền. Advisory lock toàn cục nên mọi lượt join cùng tính năng xếp hàng tuần tự trên Postgres.
- replay: Join idempotent theo PK user_id; GET /movie-match/anon/current và GET /palm-match/current là đường khôi phục state.
- profileSwitches: MOVIE_MATCH_QUEUE_MAX_WAIT_SECONDS (120), MOVIE_MATCH_ANON_DURATION_SECONDS; PALM_MATCH_QUEUE_MAX_WAIT_SECONDS (120), PALM_MATCH_SESSION_DURATION_SECONDS
- whereDefined (evidence): apps/core-api/src/modules/movie-match/movie-match.constants.ts:11, apps/core-api/src/modules/palm-match/palm-match.constants.ts:34, apps/core-api/src/modules/movie-match/movie-match.service.ts:308, apps/core-api/src/modules/palm-match/palm-match.service.ts:128, apps/core-api/src/modules/palm-match/palm-match.service.ts:141, apps/web/src/features/palm-match/api.ts:10, apps/web/src/features/movie-match/anon-api.ts:18, apps/core-api/src/config/env.validation.ts:661

### in-process-wakeup

- name: MatcherWakeup — tín hiệu đánh thức trong cùng process
- purpose: Khi ticket vừa được ZADD, MatchingService gọi matcherWakeup.notify(); MatcherWorkerService debounce thành một lần runOnce() để ghép gần như tức thì thay vì chờ interval backstop.
- deliverySemantics: Tín hiệu trong bộ nhớ của từng pod, không bền: mất khi restart, không báo sang pod khác. An toàn vì hàng đợi nằm ở Redis dùng chung và interval backstop quét lại.
- ordering: Không áp dụng; debounce 50 ms, trễ tối đa 250 ms, tối thiểu 1000 ms giữa hai lần chạy do wake-up để enqueue liên tục không thành vòng polling dày.
- failureBehavior: Lỗi trong runOnce chỉ log "Matcher wake-up lỗi"; ticket vẫn nằm trong Redis chờ lần wake-up/interval kế tiếp.
- replay: Không có; phục hồi nhờ interval `matching-matcher` (bị bỏ khi idle, nhưng enqueue là request nên đánh thức runtime).
- profileSwitches: Không có cờ env; hằng số MATCHER_WAKE_DEBOUNCE_MS=50, MATCHER_WAKE_MAX_DELAY_MS=250, MATCHER_WAKE_MIN_INTERVAL_MS=1000 trong matcher-worker.service.ts; MATCHING_MATCHER_INTERVAL_MS (backstop)
- whereDefined (evidence): apps/core-api/src/modules/matching/matcher-wakeup.ts:6, apps/core-api/src/modules/matching/matching.service.ts:626, apps/core-api/src/modules/matching/jobs/matcher-worker.service.ts:47, apps/core-api/src/modules/matching/jobs/matcher-worker.service.ts:48, apps/core-api/src/modules/matching/jobs/matcher-worker.service.ts:50

### interval-jobs

- name: ManagedInterval — job định kỳ trong core-api
- purpose: Mọi job nền của core-api là setInterval đăng ký qua SchedulerRegistry (15 định nghĩa, 14 trong ManagedInterval + 1 timer lease ở gateway). Không có hàng đợi job, không BullMQ, không cron decorator, không tách worker deployment: mọi pod core-api đều chạy toàn bộ job.
- deliverySemantics: Lặp định kỳ, at-least-once-by-repetition: tick trượt không được bù, tick đang chạy thì tick sau bị bỏ (scheduledRunInFlight). Nhiều job có `clusterSingleton` để chỉ một pod làm việc nhờ pg_try_advisory_xact_lock, nhưng đúng đắn vẫn dựa vào idempotency của domain.
- ordering: Không áp dụng.
- failureBehavior: Lỗi task chỉ log (errorMessage) và interval tiếp tục; với clusterSingleton, pod không giành được khóa hoặc gặp lỗi sẽ im lặng tới clusterRetryAfter = now + max(interval, 5s) nên failover có thể trễ vài giây. Khóa advisory gắn với transaction của một connection riêng, tự nhả khi connection chết.
- replay: Không có; việc dang dở được tick sau quét lại theo trạng thái trong DB.
- profileSwitches: Cờ bật/tắt theo job: ECONOMY_OUTBOX_RELAY_ENABLED (false), ECONOMY_RECONCILIATION_ENABLED (true), ECONOMY_REFUND_POLL_ENABLED (false); Interval theo job (xem mục jobs); cổng idle: skipWhenIdle trong code, ngưỡng cố định 5 phút (không có env)
- whereDefined (evidence): apps/core-api/src/common/scheduling/managed-interval.ts:45, apps/core-api/src/common/scheduling/managed-interval.ts:52, apps/core-api/src/common/scheduling/managed-interval.ts:84, apps/core-api/src/common/scheduling/managed-interval.ts:10, apps/core-api/src/common/scheduling/managed-interval.ts:135, apps/core-api/src/app/app.module.ts:123

### livekit-webhooks

- name: Webhook LiveKit → core-api (calling + party)
- purpose: LiveKit server POST sự kiện room/participant tới `/api/v1/calling/webhooks/livekit` và `/api/v1/party/webhooks/livekit` (cả hai URL nhận mọi event, mỗi controller lọc theo prefix room `call-*` / `party-*`). Chữ ký JWT được verify trên rawBody trước khi đọc nội dung.
- deliverySemantics: Handler idempotent và chịu out-of-order (set-if-null, end-if-not-ended, no-op khi state terminal); sau khi verify luôn trả 200. Việc LiveKit có retry hay không nằm ngoài repo (comment code giả định có retry).
- ordering: Không bảo đảm; handler không phụ thuộc thứ tự.
- failureBehavior: Webhook mất: calling có backstop GET /calling/calls/{id} đối soát bằng RoomService.listParticipants và ticker pending_timeout; party có party-room-sweeper (30s) và host-grace-check (5s). Sai chữ ký trả 401.
- replay: Không có; trạng thái được đối soát lại qua RoomService và sweeper.
- profileSwitches: LIVEKIT_API_KEY / LIVEKIT_API_SECRET (khóa ký/verify webhook, khớp livekit.yaml); LIVEKIT_URL, LIVEKIT_API_URL (endpoint client và control API); URL webhook cấu hình phía LiveKit: apps/media-server/livekit.yaml, deploy/production/livekit.yaml, k8s/base/media-server/configmap.yaml
- whereDefined (evidence): apps/core-api/src/modules/calling/webhooks/livekit-webhook.controller.ts:51, apps/core-api/src/modules/calling/webhooks/livekit-webhook.controller.ts:24, apps/core-api/src/modules/calling/calling.service.ts:491, apps/core-api/src/modules/party-room/party-room.service.ts:975, apps/core-api/src/modules/party-room/webhooks/party-livekit-webhook.controller.ts:51, deploy/production/livekit.yaml:13, deploy/production/livekit.yaml:14, k8s/base/media-server/configmap.yaml:49

### provider-webhooks

- name: Webhook nhà cung cấp thanh toán (payOS, Apple, Google RTDN)
- purpose: payOS báo thanh toán thành công (nạp Diamond), Apple App Store Server Notifications V2 báo REFUND/REVOKE, Google RTDN chỉ là tín hiệu phụ (CANCELED chỉ log, không hoàn tiền). Hoàn tiền Google chỉ đến từ job quét Voided Purchases.
- deliverySemantics: At-least-once từ nhà cung cấp; xử lý idempotent: payOS theo idempotencyKey `payos-credit:{orderCode}` (unique DB), hoàn tiền theo `refund:{provider}:{providerTransactionId}` và trạng thái receipt. Luôn trả 200 sau khi verify để tránh retry storm.
- ordering: Không bảo đảm; ledger idempotent nên webhook đến lặp/trễ không ghi đôi.
- failureBehavior: Chữ ký sai bị từ chối 401 trước mọi query/write; payOS không khớp order/số tiền thì không credit nhưng vẫn trả 200. Receipt chưa biết được ack và để job refund-poll (khi bật) bắt lại; throttle apple/google 60 req/phút, payos 120 req/phút.
- replay: Gọi lại webhook là replay an toàn nhờ idempotency; không có công cụ replay riêng.
- profileSwitches: PAYOS_CLIENT_ID, PAYOS_API_KEY, PAYOS_CHECKSUM_KEY (HMAC verify); ECONOMY_APPLE_WEBHOOK_VERIFIER và ECONOMY_GOOGLE_RTDN_VERIFIER (mặc định `store`, fail-closed), ECONOMY_APPLE_ROOT_CA_PEM, ECONOMY_APPLE_BUNDLE_ID; ECONOMY_IAP_VERIFIER (mặc định dev) cho đường verify mua hàng từ client; ECONOMY_REFUND_POLL_ENABLED (mặc định false) bật job backstop hoàn tiền
- whereDefined (evidence): apps/core-api/src/modules/economy/webhooks/economy-webhooks.controller.ts:121, apps/core-api/src/modules/economy/webhooks/economy-webhooks.controller.ts:123, apps/core-api/src/modules/economy/webhooks/economy-webhooks.controller.ts:56, apps/core-api/src/modules/economy/webhooks/economy-webhooks.controller.ts:93, apps/core-api/src/modules/economy/webhooks/economy-webhooks.controller.ts:31, apps/core-api/src/modules/economy/webhooks/economy-webhooks.controller.ts:132, apps/core-api/src/modules/economy/services/payos.service.ts:178, apps/core-api/src/config/env.validation.ts:424, apps/core-api/src/config/env.validation.ts:428
