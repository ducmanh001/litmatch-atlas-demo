# Cache and state: Redis, locks, idempotency, client state: redisClients

id: system:state:redisClients

Written by an AI agent from the code; every file:line quote was checked automatically (exists within ±3 lines) but NO independent verification pass has run yet. Treat claims as strong leads; open the cited file before editing.

### core-api command client dùng chung (createCoreRedisClient)

- createdIn: apps/core-api/src/common/redis/core-redis-client.ts (hàm createCoreRedisClient, gọi từ provider của calling, discovery, friend, gift, matching, mini-game, movie-match, party-room, soul-match, user và app.module cho throttler)
- urlEnv: REDIS_URL (Joi uri scheme redis|rediss, mặc định redis://localhost:6379)
- tls: Mã không đặt option tls riêng; TLS tuỳ scheme của REDIS_URL. deploy/hosted/*.env.example dùng rediss://, compose production dùng redis://redis:6379.
- retryAndTimeouts: connectTimeout 1000 ms, commandTimeout 1000 ms, maxRetriesPerRequest 1, enableOfflineQueue false (lệnh fail nhanh); reconnect theo cấp số nhân 250 ms đến tối đa 15000 ms, jitter +-25%.
- sharedBetween: Mọi module core-api trong cùng process dùng CHUNG một connection theo REDIS_URL (đếm tham chiếu); connection subscriber không đi qua helper này.
- evidence: apps/core-api/src/common/redis/core-redis-client.ts:49
- moreEvidence (evidence): apps/core-api/src/common/redis/core-redis-client.ts:52, apps/core-api/src/common/redis/core-redis-client.ts:3, apps/core-api/src/common/redis/core-redis-client.ts:4, apps/core-api/src/config/env.validation.ts:263, apps/core-api/src/app/app.module.ts:114, apps/core-api/src/modules/user/redis/user-redis.provider.ts:15, deploy/hosted/core-api.env.example:7, deploy/production/compose.yml:63

### signaling quota command client

- createdIn: apps/signaling-gateway/src/app/connection-quota.service.ts (constructor ConnectionQuotaService)
- urlEnv: REDIS_URL của signaling-gateway (cùng Redis với core-api)
- tls: Không đặt option tls; theo scheme REDIS_URL (hosted example dùng rediss://).
- retryAndTimeouts: signalingRedisClientOptions: connectTimeout 1000 ms, commandTimeout 1000 ms, maxRetriesPerRequest 1, enableOfflineQueue false; reconnect 500 ms đến 15000 ms, jitter +-25%.
- sharedBetween: Chỉ ConnectionQuotaService (acquire/refresh/release); tách khỏi connection subscriber. Shutdown gọi QUIT (lỗi thì disconnect).
- evidence: apps/signaling-gateway/src/app/connection-quota.service.ts:82
- moreEvidence (evidence): apps/signaling-gateway/src/app/redis-client-options.ts:3, apps/signaling-gateway/src/app/redis-client-options.ts:25, deploy/hosted/signaling-gateway.env.example:6, apps/signaling-gateway/src/app/connection-quota.service.ts:162

### signaling PSUBSCRIBE subscriber

- createdIn: apps/signaling-gateway/src/app/signaling.gateway.ts (afterInit)
- urlEnv: REDIS_URL của signaling-gateway
- tls: Không đặt option tls; theo scheme REDIS_URL.
- retryAndTimeouts: Cùng signalingRedisClientOptions(); ready thì ensureSubscribed gọi PSUBSCRIBE, close/end thì subscriptionReady=false.
- sharedBetween: Chỉ SignalingGateway (subscriber mode không dùng được lệnh khác). onModuleDestroy gọi QUIT, lỗi thì disconnect cưỡng bức.
- evidence: apps/signaling-gateway/src/app/signaling.gateway.ts:103
- moreEvidence (evidence): apps/signaling-gateway/src/app/signaling.gateway.ts:140

### signaling cluster adapter pub/sub

- createdIn: apps/signaling-gateway/src/app/redis-adapter.service.ts (connect)
- urlEnv: REDIS_URL truyền vào connect(redisUrl)
- tls: Không đặt option tls; theo scheme REDIS_URL.
- retryAndTimeouts: pubClient dùng signalingRedisClientOptions(); subClient là pubClient.duplicate(); cả hai có listener error để không crash khi reconnect.
- sharedBetween: Chỉ SignalingRedisAdapterService (2 connection riêng, không chung với PSUBSCRIBE). Mỗi pod signaling giữ tổng 4 connection Redis.
- evidence: apps/signaling-gateway/src/app/redis-adapter.service.ts:25
