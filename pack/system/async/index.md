# Async flows: realtime, outbox, jobs, queues

id: system:async

Realtime là best-effort: core-api PUBLISH realtime:user:{id} sau commit → signaling-gateway PSUBSCRIBE, relay server.local → Socket.IO → web (REST là nguồn sự thật). Event bền duy nhất là Economy outbox → Kafka (relay tắt mặc định, chưa có consumer). 15 job interval (advisory lock, 6 job bỏ tick khi idle); ghép cặp dùng Redis ZSET + Postgres.

Written by an AI agent from the code; every file:line quote was checked automatically (exists within ±3 lines) but NO independent verification pass has run yet. Treat claims as strong leads; open the cited file before editing.

## Files

- [limits](limits.md): 10 items
- [docDrift](docDrift.md): 12 items
- [transports](transports.md): 9 items
- [realtimeEvents](realtimeEvents.md): 26 items
- [flows](flows.md): 8 items
- [outbox](outbox.md): 7 fields
- [jobs](jobs.md): 15 items
- [queues](queues.md): 9 items
- [idleGating](idleGating.md): 4 fields
