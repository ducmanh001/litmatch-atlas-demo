# Cache and state: Redis, locks, idempotency, client state: docDrift

id: system:state:docDrift

Written by an AI agent from the code; every file:line quote was checked automatically (exists within ±3 lines) but NO independent verification pass has run yet. Treat claims as strong leads; open the cited file before editing.

### #1

- doc: docs/services/realtime-gateway.md:56
- claim: Mỗi module publish bằng Redis client riêng của mình.
- actual: createCoreRedisClient trả cùng một ioredis connection cho mọi module trong một process (khoá theo REDIS_URL, đếm tham chiếu); chỉ token inject là riêng theo module.
- evidence: apps/core-api/src/common/redis/core-redis-client.ts:36

### #2

- doc: apps/core-api/src/modules/discovery/redis/discovery-redis.provider.ts:7 (comment trong mã)
- claim: Client ioredis riêng của module Discovery, không dùng chung connection với module khác.
- actual: Provider gọi createCoreRedisClient nên dùng chung một connection với các module khác trong cùng process.
- evidence: apps/core-api/src/modules/discovery/redis/discovery-redis.provider.ts:7

### #3

- doc: apps/core-api/src/modules/economy/entities/wallet.entity.ts:11 (comment trong mã)
- claim: Wallet rebuild được bằng EconomyService.rebuildWallet().
- actual: rebuildWallet được cài ở LedgerService (và LedgerPersistencePort); EconomyService không có phương thức này (grep trong modules/economy).
- evidence: apps/core-api/src/modules/economy/services/ledger.service.ts:277
