# Cache and state: Redis, locks, idempotency, client state: redisKeys

id: system:state:redisKeys

Written by an AI agent from the code; every file:line quote was checked automatically (exists within ±3 lines) but NO independent verification pass has run yet. Treat claims as strong leads; open the cited file before editing.

### realtime-user-channel

- pattern: realtime:user:{userId}
- structure: pubsub-channel
- purpose: Kênh fanout theo người nhận: core-api publish envelope đã tính sẵn quyền, signaling-gateway PSUBSCRIBE realtime:user:* rồi relay nguyên văn xuống socket.
- owner: libs/common-dtos (hợp đồng); core-api(common) publish, signaling-gateway subscribe
- ttl: none (pub/sub không lưu, at-most-once)
- writers:
  - module: core-api(common); op: PUBLISH qua publishRealtimeEvent (mọi module nghiệp vụ dùng chung helper) [evidence: apps/core-api/src/common/realtime/publish-realtime.ts:24]
  - module: core-api(common); op: RedisRealtimePublisher.publish gọi PUBLISH channel payload [evidence: apps/core-api/src/common/realtime/redis-realtime-publisher.adapter.ts:11]
  - module: matching; op: dựng RedisRealtimePublisher trên client dùng chung của module [evidence: apps/core-api/src/modules/matching/redis/matching-redis.provider.ts:79]
- readers:
  - module: signaling-gateway; op: PSUBSCRIBE realtime:user:* trên connection subscriber riêng [evidence: apps/signaling-gateway/src/app/signaling.gateway.ts:346]
  - module: signaling-gateway; op: relay chỉ emit tới room cục bộ user:{userId} của pod (server.local) [evidence: apps/signaling-gateway/src/app/signaling.gateway.ts:332]
- failureMode: fail-open ở phía publish (nuốt lỗi, log warn, client còn REST polling); pod chưa subscribe được thì isReady() false và socket của pod đó không nhận event.
- cardinality: Không lưu key; mỗi user nhận event là một channel logic.
- evidence: libs/common-dtos/src/lib/realtime-events.ts:13
- moreEvidence (evidence): apps/core-api/src/common/realtime/publish-realtime.ts:12, apps/signaling-gateway/src/app/signaling.gateway.ts:153

### realtime-presence

- pattern: realtime:presence:{userId}
- structure: zset
- purpose: Presence dẫn xuất: mỗi lease socket là một member (score = thời điểm hết hạn, ms); core-api đọc để biết user có online không.
- owner: signaling-gateway (ghi) / user (đọc); tiền tố khai báo ở libs/common-dtos
- ttl: PEXPIRE = WS_CONNECTION_LEASE_MS (mặc định 90000, khoảng 10000-300000); member quá hạn bị ZREMRANGEBYSCORE khi đọc/acquire/refresh
- writers:
  - module: signaling-gateway; op: ZADD + PEXPIRE trong ACQUIRE_SCRIPT/REFRESH_SCRIPT, ZREM trong RELEASE_SCRIPT [evidence: apps/signaling-gateway/src/app/connection-quota.service.ts:25]
  - module: signaling-gateway; op: refresh lease mỗi leaseMs/3 khi socket còn sống [evidence: apps/signaling-gateway/src/app/signaling.gateway.ts:300]
- readers:
  - module: user; op: ZREMRANGEBYSCORE -inf now để dọn member hết hạn [evidence: apps/core-api/src/modules/user/redis/redis-presence-reader.adapter.ts:15]
  - module: user; op: ZCARD > 0 nghĩa là online [evidence: apps/core-api/src/modules/user/redis/redis-presence-reader.adapter.ts:16]
- failureMode: fail-closed: RedisPresenceReader trả false (offline) khi Redis lỗi; phía ghi, acquire lỗi làm handshake bị từ chối.
- cardinality: 1 zset mỗi user đang có socket, tối đa WS_MAX_CONNECTIONS_PER_USER (Joi max 3) member.
- evidence: libs/common-dtos/src/lib/realtime-presence.ts:6
- moreEvidence (evidence): apps/core-api/src/modules/user/redis/redis-presence-reader.adapter.ts:11

### signaling-connection-quota

- pattern: signaling:connection-quota:{userId}
- structure: zset
- purpose: Lease số kết nối socket mỗi user trên toàn cụm (member = leaseId UUID); acquire/refresh/release chạy atomic bằng Lua.
- owner: signaling-gateway
- ttl: PEXPIRE = WS_CONNECTION_LEASE_MS (mặc định 90000, khoảng 10000-300000); k8s base đặt 90000
- writers:
  - module: signaling-gateway; op: acquire (ACQUIRE_SCRIPT) khi handshake [evidence: apps/signaling-gateway/src/app/connection-quota.service.ts:126]
  - module: signaling-gateway; op: refresh (REFRESH_SCRIPT) định kỳ [evidence: apps/signaling-gateway/src/app/connection-quota.service.ts:139]
  - module: signaling-gateway; op: release (RELEASE_SCRIPT) khi disconnect [evidence: apps/signaling-gateway/src/app/connection-quota.service.ts:151]
- readers:
  - module: signaling-gateway; op: ZSCORE/ZCARD trong ACQUIRE_SCRIPT để so với WS_MAX_CONNECTIONS_PER_USER [evidence: apps/signaling-gateway/src/app/connection-quota.service.ts:29]
- failureMode: fail-closed: acquire lỗi thì handshake bị từ chối (CONNECTION_QUOTA_UNAVAILABLE); refresh lỗi thì ngắt socket; release lỗi thì lease tự hết hạn theo TTL.
- cardinality: 1 zset mỗi user đang có socket, tối đa WS_MAX_CONNECTIONS_PER_USER (Joi max 3) member.
- evidence: apps/signaling-gateway/src/app/connection-quota.service.ts:15
- moreEvidence (evidence): apps/signaling-gateway/src/config/env.validation.ts:43, apps/signaling-gateway/src/config/env.validation.ts:48, k8s/base/signaling-gateway/configmap.yaml:18, apps/signaling-gateway/src/app/signaling.gateway.ts:258

### matching-queue-shard

- pattern: matching:queue:{matchType}:{region}:{ageBand}
- structure: zset
- purpose: Hàng đợi ghép đôi theo shard; member = ticketId, score = enqueuedAt + trustPenaltyMs - priorityBoostMs (nhỏ hơn được ghép trước).
- owner: matching
- ttl: none (mã không đặt TTL; xoá bằng ZPOPMIN/ZREM)
- writers:
  - module: matching; op: ZADD NX|XX (enqueue; requeue giữ score gốc với NX) [evidence: apps/core-api/src/modules/matching/redis/redis-matching-queue.adapter.ts:18]
  - module: matching; op: ZREM ticketId khi huỷ/hết hạn [evidence: apps/core-api/src/modules/matching/redis/redis-matching-queue.adapter.ts:22]
  - module: matching; op: ZPOPMIN shard 2 (matcher lấy 2 ticket điểm thấp nhất, atomic) [evidence: apps/core-api/src/modules/matching/redis/redis-matching-queue.adapter.ts:26]
- readers:
  - module: matching; op: ZCARD > 0 để dọn shard rỗng khỏi tập active [evidence: apps/core-api/src/modules/matching/redis/redis-matching-queue.adapter.ts:47]
- failureMode: degrades to Postgres: queue chỉ là bản dẫn xuất, lệnh Redis reject sau 1 s (không xếp hàng offline) nên không ghép được tới khi Redis phục hồi; trạng thái ticket bền vẫn ở Postgres.
- cardinality: 1 zset mỗi tổ hợp (matchType, region, ageBand) đang có ticket queued.
- evidence: apps/core-api/src/modules/matching/redis/matching-redis.provider.ts:29
- moreEvidence (evidence): apps/core-api/src/modules/matching/redis/matching-redis.provider.ts:13, apps/core-api/src/modules/matching/redis/matching-redis.provider.ts:50, apps/core-api/src/modules/matching/jobs/matcher-worker.service.ts:361

### matching-active-shards

- pattern: matching:shards:active
- structure: set
- purpose: Tập shard đang có ticket chờ để matcher chỉ quét các shard này, không quét keyspace.
- owner: matching
- ttl: none
- writers:
  - module: matching; op: SADD shard (markActive khi enqueue) [evidence: apps/core-api/src/modules/matching/redis/redis-matching-queue.adapter.ts:39]
  - module: matching; op: SREM shard (unmarkActive khi shard rỗng) [evidence: apps/core-api/src/modules/matching/redis/redis-matching-queue.adapter.ts:43]
- readers:
  - module: matching; op: SMEMBERS để liệt kê shard cần quét mỗi tick [evidence: apps/core-api/src/modules/matching/redis/redis-matching-queue.adapter.ts:35]
- failureMode: degrades to Postgres: cùng chế độ lỗi với queue shard (Redis lỗi thì matcher không có shard để quét).
- cardinality: 1 key duy nhất; số member = số shard đang có ticket.
- evidence: apps/core-api/src/modules/matching/redis/matching-redis.provider.ts:21

### throttle-hits

- pattern: throttle:{{throttlerName}:{key}}:hits
- structure: string
- purpose: Bộ đếm cửa sổ cố định của @nestjs/throttler dùng chung giữa các pod khi THROTTLE_STORAGE=redis; cặp ngoặc ngoài cùng là hash tag Redis Cluster.
- owner: core-api(common/throttle)
- ttl: PEXPIRE = ttl cửa sổ (THROTTLE_TTL_SECONDS mặc định 60 s hoặc ttl của @Throttle); rút còn blockDuration khi vượt limit
- writers:
  - module: core-api(common); op: INCR + PEXPIRE trong THROTTLE_INCREMENT_LUA [evidence: apps/core-api/src/common/throttle/redis-throttler-storage.ts:36]
- readers:
  - module: core-api(common); op: GET/PTTL cùng script (khi đang bị block trả số hits hiện có) [evidence: apps/core-api/src/common/throttle/redis-throttler-storage.ts:91]
- failureMode: fail-open: lỗi Redis thì dùng bộ đếm trong RAM của pod 5 s (REDIS_RETRY_AFTER_MS) rồi thử lại; request không bị lỗi.
- cardinality: 1 key mỗi tổ hợp (throttler, khoá ThrottlerGuard theo req.ip và route) trong cửa sổ.
- evidence: apps/core-api/src/common/throttle/redis-throttler-storage.ts:89
- moreEvidence (evidence): apps/core-api/src/common/throttle/redis-throttler-storage.ts:16, apps/core-api/src/config/env.validation.ts:753

### throttle-block

- pattern: throttle:{{throttlerName}:{key}}:block
- structure: string
- purpose: Cờ chặn đặt khi vượt limit; hits được rút ngắn bằng blockDuration nên bộ đếm khởi động lại đúng lúc hết chặn.
- owner: core-api(common/throttle)
- ttl: PX = blockDuration của throttler
- writers:
  - module: core-api(common); op: SET '1' PX blockDuration trong THROTTLE_INCREMENT_LUA [evidence: apps/core-api/src/common/throttle/redis-throttler-storage.ts:43]
- readers:
  - module: core-api(common); op: PTTL KEYS[2] đầu script để biết còn bị chặn [evidence: apps/core-api/src/common/throttle/redis-throttler-storage.ts:30]
- failureMode: fail-open: như throttle-hits (fallback bộ nhớ pod khi Redis lỗi).
- cardinality: Chỉ tồn tại cho khoá đang bị chặn.
- evidence: apps/core-api/src/common/throttle/redis-throttler-storage.ts:90

### ratelimit-discovery-location

- pattern: discovery:location-update:count:{userId}
- structure: string
- purpose: Bộ đếm giới hạn cập nhật vị trí mỗi giờ (DISCOVERY_LOCATION_UPDATE_RATE_LIMIT_PER_HOUR), chống dò hành trình.
- owner: discovery
- ttl: 3600 s (RATE_LIMIT_WINDOW_SECONDS), đặt khi INCR lần đầu
- writers:
  - module: discovery; op: checkRateLimit trong setLocation (consume Lua) [evidence: apps/core-api/src/modules/discovery/nearby.service.ts:76]
- readers:
  - module: common/redis; op: checkRateLimit dựng RedisRateLimitAdapter rồi consume [evidence: apps/core-api/src/common/redis/rate-limit.ts:12]
- failureMode: fail-closed theo mã: không có try/catch quanh checkRateLimit nên lỗi Redis ném ra khỏi service (request lỗi).
- cardinality: 1 key mỗi user có cập nhật vị trí trong giờ, kèm tối đa limit key reservation.
- evidence: apps/core-api/src/modules/discovery/redis/discovery-redis.provider.ts:12
- moreEvidence (evidence): apps/core-api/src/modules/discovery/nearby.service.ts:34

### ratelimit-discovery-nearby

- pattern: discovery:nearby-query:count:{userId}
- structure: string
- purpose: Bộ đếm giới hạn truy vấn nearby mỗi giờ (DISCOVERY_NEARBY_QUERY_RATE_LIMIT_PER_HOUR), chống dò trilateration.
- owner: discovery
- ttl: 3600 s (RATE_LIMIT_WINDOW_SECONDS), đặt khi INCR lần đầu
- writers:
  - module: discovery; op: checkRateLimit trong listNearby (consume Lua) [evidence: apps/core-api/src/modules/discovery/nearby.service.ts:128]
- readers:
  - module: common/redis; op: đọc/ghi bên trong RATE_LIMIT_CONSUME_LUA [evidence: apps/core-api/src/common/redis/redis-rate-limit.adapter.ts:79]
- failureMode: fail-closed theo mã: không có try/catch quanh checkRateLimit.
- cardinality: 1 key mỗi user có truy vấn nearby trong giờ, kèm tối đa limit key reservation.
- evidence: apps/core-api/src/modules/discovery/redis/discovery-redis.provider.ts:17

### ratelimit-matching-invite

- pattern: matching:invite:count:{userId}
- structure: string
- purpose: Bộ đếm giới hạn lời mời Voice/Soul Match mỗi giờ (MATCHING_INVITE_RATE_LIMIT_PER_HOUR).
- owner: matching
- ttl: 3600 s (RATE_LIMIT_WINDOW_SECONDS), đặt khi INCR lần đầu
- writers:
  - module: matching; op: RateLimitPort.consume khi tạo invite [evidence: apps/core-api/src/modules/matching/services/invite.service.ts:109]
- readers:
  - module: common/redis; op: đọc/ghi bên trong RATE_LIMIT_CONSUME_LUA [evidence: apps/core-api/src/common/redis/redis-rate-limit.adapter.ts:79]
- failureMode: fail-closed theo mã: consume nằm ngoài try/catch nên lỗi Redis làm request tạo invite lỗi.
- cardinality: 1 key mỗi user có gửi invite trong giờ, kèm tối đa limit key reservation.
- evidence: apps/core-api/src/modules/matching/services/invite.service.ts:62

### ratelimit-window

- pattern: {rateLimitKey}:rate-limit-window
- structure: string
- purpose: Định danh cửa sổ hiện tại (UUID) để refund muộn không trừ nhầm cửa sổ sau dùng lại cùng rateLimitKey.
- owner: core-api(common/redis)
- ttl: EX = windowSeconds khi INCR=1; được dựng lại theo TTL còn lại của counter nếu mất
- writers:
  - module: core-api(common); op: SET window EX trong RATE_LIMIT_CONSUME_LUA [evidence: apps/core-api/src/common/redis/redis-rate-limit.adapter.ts:29]
- readers:
  - module: core-api(common); op: GET so khớp window trong RATE_LIMIT_REFUND_LUA [evidence: apps/core-api/src/common/redis/redis-rate-limit.adapter.ts:52]
- failureMode: fail-closed: cùng đường eval với counter, lỗi Redis ném ra cho caller.
- cardinality: 1 key cho mỗi rateLimitKey đang trong cửa sổ.
- evidence: apps/core-api/src/common/redis/redis-rate-limit.adapter.ts:14

### ratelimit-reservation

- pattern: {rateLimitKey}:rate-limit-reservation:{operationId|uuid}
- structure: string
- purpose: Dấu đã tiêu một slot: operationId lặp lại thì consume được coi là deduplicated; refund xoá dấu trước khi giảm counter.
- owner: core-api(common/redis)
- ttl: EX = TTL còn lại của counter (tối thiểu 1 s)
- writers:
  - module: core-api(common); op: SET reservation EX sau mỗi consume được phép [evidence: apps/core-api/src/common/redis/redis-rate-limit.adapter.ts:42]
- readers:
  - module: core-api(common); op: EXISTS reservation đầu script để trả deduplicated [evidence: apps/core-api/src/common/redis/redis-rate-limit.adapter.ts:22]
- failureMode: fail-closed: lỗi Redis ném ra cho caller.
- cardinality: Mỗi lượt consume được phép tạo 1 key (UUID ngẫu nhiên vì chưa caller nào truyền operationId), sống tới hết cửa sổ: tối đa limit key mỗi rateLimitKey mỗi cửa sổ.
- evidence: apps/core-api/src/common/redis/redis-rate-limit.adapter.ts:13
- confidence: high

### lua-ratelimit-consume

- pattern: RATE_LIMIT_CONSUME_LUA (KEYS: counter, reservation, window)
- structure: lua
- purpose: INCR + EXPIRE atomic; vượt limit thì DECR lại và không cấp reservation; reservation đã có thì trả deduplicated.
- owner: core-api(common/redis)
- ttl: n/a (đặt EXPIRE = windowSeconds trên counter)
- writers:
  - module: core-api(common); op: EVAL qua RedisRateLimitAdapter.consume [evidence: apps/core-api/src/common/redis/redis-rate-limit.adapter.ts:79]
- failureMode: fail-closed: adapter không bắt lỗi, caller nhận lỗi.
- cardinality: n/a
- evidence: apps/core-api/src/common/redis/redis-rate-limit.adapter.ts:21

### lua-ratelimit-refund

- pattern: RATE_LIMIT_REFUND_LUA (KEYS: counter, reservation, window)
- structure: lua
- purpose: Hoàn một slot đã tiêu: xoá reservation trước, chỉ DECR khi window còn khớp; hiện chưa có caller nào gọi refund.
- owner: core-api(common/redis)
- ttl: n/a
- writers:
  - module: core-api(common); op: EVAL qua RedisRateLimitAdapter.refund [evidence: apps/core-api/src/common/redis/redis-rate-limit.adapter.ts:103]
- failureMode: fail-closed: lỗi Redis ném ra cho caller (nếu có).
- cardinality: n/a
- evidence: apps/core-api/src/common/redis/redis-rate-limit.adapter.ts:47

### lua-throttle-increment

- pattern: THROTTLE_INCREMENT_LUA (KEYS: hits, block)
- structure: lua
- purpose: Tăng hits và đặt cờ block trong một lần gọi; hai key chung hash tag để cùng slot khi Redis Cluster.
- owner: core-api(common/throttle)
- ttl: n/a
- writers:
  - module: core-api(common); op: EVAL trong RedisThrottlerStorage.increment [evidence: apps/core-api/src/common/throttle/redis-throttler-storage.ts:91]
- failureMode: fail-open: lỗi thì fallback bộ nhớ pod.
- cardinality: n/a
- evidence: apps/core-api/src/common/throttle/redis-throttler-storage.ts:29

### lua-connection-acquire

- pattern: ACQUIRE_SCRIPT (KEYS: quota, presence)
- structure: lua
- purpose: Dọn lease hết hạn, gia hạn nếu lease đã có, từ chối khi ZCARD >= WS_MAX_CONNECTIONS_PER_USER, ngược lại thêm lease vào cả quota và presence.
- owner: signaling-gateway
- ttl: n/a (PEXPIRE = leaseMs)
- writers:
  - module: signaling-gateway; op: EVAL từ ConnectionQuotaService.acquire [evidence: apps/signaling-gateway/src/app/connection-quota.service.ts:126]
- failureMode: fail-closed: lỗi thì handshake bị từ chối.
- cardinality: n/a
- evidence: apps/signaling-gateway/src/app/connection-quota.service.ts:17

### lua-connection-refresh

- pattern: REFRESH_SCRIPT (KEYS: quota, presence)
- structure: lua
- purpose: Gia hạn lease nếu còn tồn tại (trả 0 nếu đã mất) và cập nhật presence cùng điểm hết hạn.
- owner: signaling-gateway
- ttl: n/a (PEXPIRE = leaseMs)
- writers:
  - module: signaling-gateway; op: EVAL từ ConnectionQuotaService.refresh [evidence: apps/signaling-gateway/src/app/connection-quota.service.ts:139]
- failureMode: fail-closed: refresh lỗi hoặc trả 0 thì ngắt socket.
- cardinality: n/a
- evidence: apps/signaling-gateway/src/app/connection-quota.service.ts:39

### lua-connection-release

- pattern: RELEASE_SCRIPT (KEYS: quota, presence)
- structure: lua
- purpose: ZREM lease khỏi quota và presence, xoá key khi rỗng.
- owner: signaling-gateway
- ttl: n/a
- writers:
  - module: signaling-gateway; op: EVAL từ ConnectionQuotaService.release [evidence: apps/signaling-gateway/src/app/connection-quota.service.ts:151]
- failureMode: degrades to TTL: release lỗi chỉ log/Sentry, lease tự hết hạn theo leaseMs.
- cardinality: n/a
- evidence: apps/signaling-gateway/src/app/connection-quota.service.ts:53

### socketio-adapter-channels

- pattern: (tên do @socket.io/redis-adapter đặt, không khai báo trong repo)
- structure: pubsub-channel
- purpose: Bus liên pod của Socket.IO cluster adapter trên 2 kết nối pub/sub riêng; relay realtime dùng server.local nên không đi qua bus này.
- owner: signaling-gateway
- ttl: none (pub/sub)
- writers:
  - module: signaling-gateway; op: pubClient tạo adapter; subClient = pubClient.duplicate() [evidence: apps/signaling-gateway/src/app/redis-adapter.service.ts:26]
  - module: signaling-gateway; op: đóng cả hai client khi shutdown (QUIT, lỗi thì disconnect) [evidence: apps/signaling-gateway/src/app/redis-adapter.service.ts:75]
- readers:
  - module: signaling-gateway; op: subClient nhận broadcast không-local từ pod khác [evidence: apps/signaling-gateway/src/app/redis-adapter.service.ts:46]
- failureMode: readiness: isReady() cần cả pub và sub ở trạng thái ready; lỗi runtime chỉ log warn.
- cardinality: Do thư viện quyết định (chưa xác minh trong repo).
- evidence: apps/signaling-gateway/src/app/signaling.gateway.ts:317
- confidence: low
