# Cache and state: Redis, locks, idempotency, client state

id: system:state

Trạng thái bền nằm ở Postgres (khoá dòng, unique, advisory lock); Redis chỉ giữ dữ liệu tạm/dẫn xuất (queue ghép đôi, lease kết nối và presence, rate limit, pub/sub realtime). Lỗi Redis fail-open ở throttle/publish, fail-closed ở lease/presence/rate-limit. Mỗi process còn có Map/cờ riêng; phiên là JWT ngắn + refresh xoay vòng trong DB.

Written by an AI agent from the code; every file:line quote was checked automatically (exists within ±3 lines) but NO independent verification pass has run yet. Treat claims as strong leads; open the cited file before editing.

## Files

- [limits](limits.md): 8 items
- [docDrift](docDrift.md): 3 items
- [redisKeys](redisKeys.md): 19 items
- [redisClients](redisClients.md): 4 items
- [inMemoryState](inMemoryState.md): 8 items
- [locksAndLeases](locksAndLeases.md): 25 items
- [idempotency](idempotency.md): 24 items
- [consistencyPatterns](consistencyPatterns.md): 11 items
- [serverSessionState](serverSessionState.md): 6 fields
- [clientState](clientState.md): 2 fields
- [httpAndEdgeCaching](httpAndEdgeCaching.md): 2 items
- [cacheInvalidationRules](cacheInvalidationRules.md): 9 items
