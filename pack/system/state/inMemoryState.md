# Cache and state: Redis, locks, idempotency, client state: inMemoryState

id: system:state:inMemoryState

Written by an AI agent from the code; every file:line quote was checked automatically (exists within ±3 lines) but NO independent verification pass has run yet. Treat claims as strong leads; open the cited file before editing.

### sharedClientsByUrl (Map), sharedEntryByClient (WeakMap), fullyReleasedClients (WeakSet): registry c…

- where: apps/core-api/src/common/redis/core-redis-client.ts
- scope: per process
- multiPodConsequence: Mỗi pod giữ đúng 1 connection command cho mọi module; N pod là N connection tới Redis, không có phối hợp giữa pod.
- evidence: apps/core-api/src/common/redis/core-redis-client.ts:12
- moreEvidence (evidence): apps/core-api/src/common/redis/core-redis-client.ts:69, apps/core-api/src/common/redis/core-redis-client.ts:81

### fallback (ThrottlerStorageService đếm trong RAM), redisRetryAt, lastWarnAt.

- where: apps/core-api/src/common/throttle/redis-throttler-storage.ts
- scope: per process
- multiPodConsequence: Khi Redis lỗi hoặc THROTTLE_STORAGE=memory (mặc định trong .env.example) mỗi pod đếm riêng: giới hạn thực tế gấp N lần và reset mỗi lần deploy; k8s base đặt THROTTLE_STORAGE=redis.
- evidence: apps/core-api/src/common/throttle/redis-throttler-storage.ts:54
- moreEvidence (evidence): apps/core-api/src/config/env.validation.ts:757, k8s/base/core-api/configmap.yaml:194, .env.example:414

### ManagedInterval.running, scheduledRunInFlight, clusterRetryAfter: cờ chống chồng lấn tick và hẹn gi…

- where: apps/core-api/src/common/scheduling/managed-interval.ts
- scope: per process
- multiPodConsequence: Chỉ chống chồng lấn trong một process; giữa các pod chỉ job bật clusterSingleton (pg_try_advisory_xact_lock) mới chạy ở một pod. Outbox-relay và iap-refund-poll không bật clusterSingleton nên chạy ở mọi pod khi được bật và dựa vào SKIP LOCKED.
- evidence: apps/core-api/src/common/scheduling/managed-interval.ts:26
- moreEvidence (evidence): apps/core-api/src/common/scheduling/managed-interval.ts:23

### lastMeaningfulRequestAt, meaningfulRequestCount, backgroundSkipCount, backgroundSkipsByJob: tín hiệ…

- where: apps/core-api/src/common/runtime/runtime-activity.ts
- scope: per process
- multiPodConsequence: Pod không nhận request nghiệp vụ trong 5 phút bỏ qua các tick có skipWhenIdle dù pod khác đang bận; số đếm skip/request là riêng từng pod.
- evidence: apps/core-api/src/common/runtime/runtime-activity.ts:10
- moreEvidence (evidence): apps/core-api/src/common/runtime/runtime-activity.ts:15

### MatcherWakeup.listeners (Set): tín hiệu đánh thức matcher trong cùng process.

- where: apps/core-api/src/modules/matching/matcher-wakeup.ts
- scope: per process
- multiPodConsequence: Wake-up chỉ tới matcher cùng pod; pod khác thấy ticket mới nhờ Redis queue và backstop interval của worker.
- evidence: apps/core-api/src/modules/matching/matcher-wakeup.ts:12
- moreEvidence (evidence): apps/core-api/src/modules/matching/matcher-wakeup.ts:7

### activeQuotaLeases (Map leaseId -> {userId, timer, client}), subscriptionReady, subscriptionInFlight…

- where: apps/signaling-gateway/src/app/signaling.gateway.ts
- scope: per process
- multiPodConsequence: Socket và timer refresh chỉ tồn tại ở pod giữ socket; pod chết thì lease trong Redis tự hết hạn sau WS_CONNECTION_LEASE_MS (mặc định 90 s).
- evidence: apps/signaling-gateway/src/app/signaling.gateway.ts:59

### Room Socket.IO user:{userId} chứa các socket cục bộ của pod.

- where: apps/signaling-gateway/src/app/signaling.gateway.ts
- scope: per process
- multiPodConsequence: Relay dùng server.local nên mỗi pod chỉ emit cho socket của chính nó; mọi pod đều PSUBSCRIBE và nhận cùng event, pod không có socket của user thì bỏ qua.
- evidence: apps/signaling-gateway/src/app/signaling.gateway.ts:33
- moreEvidence (evidence): apps/signaling-gateway/src/app/signaling.gateway.ts:332

### ConnectionQuotaService.unavailableListeners (Set) và trạng thái connection Redis của quota.

- where: apps/signaling-gateway/src/app/connection-quota.service.ts
- scope: per process
- multiPodConsequence: Khi connection quota đóng/kết thúc, pod ngắt hết socket đang giữ lease (fail-closed); không ảnh hưởng socket ở pod khác.
- evidence: apps/signaling-gateway/src/app/connection-quota.service.ts:71
