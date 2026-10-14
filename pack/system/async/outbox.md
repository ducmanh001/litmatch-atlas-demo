# Async flows: realtime, outbox, jobs, queues: outbox

id: system:async:outbox

Written by an AI agent from the code; every file:line quote was checked automatically (exists within ±3 lines) but NO independent verification pass has run yet. Treat claims as strong leads; open the cited file before editing.

### table

- name: outbox_events

### columns

- id uuid PK
- topic varchar(128)
- event_type varchar(64)
- payload jsonb
- attempts int DEFAULT 0
- published_at timestamptz NULL
- dead_lettered_at timestamptz NULL
- last_error text NULL
- created_at timestamptz

### indexes

- idx_outbox_events_relayable (created_at) WHERE published_at IS NULL AND dead_lettered_at IS NULL
- idx_outbox_events_unpublished (created_at) WHERE published_at IS NULL (khai ở entity và migration economy-ledger)

- retention: Không có job dọn: khi relay tắt dòng giữ published_at = NULL và tích lũy; khi relay bật dòng đã publish cũng được giữ vĩnh viễn; dòng dead-letter giữ lại làm nguồn replay thủ công (theo docs/services/economy-service.md § 4).

### evidence

### #1

- file: apps/core-api/src/modules/economy/entities/outbox-event.entity.ts
- line: 10
- quote: @Entity({ name: 'outbox_events' })

### #2

- file: apps/core-api/src/modules/economy/entities/outbox-event.entity.ts
- line: 33
- quote: /** Terminal relay state; the original payload remains available for inspection/replay. */

### #3

- file: apps/core-api/src/database/migrations/1757000000000-outbox-reliability.ts
- line: 15
- quote: CREATE INDEX idx_outbox_events_relayable ON outbox_events(created_at) WHERE published_at IS NULL AND dead_lettered_at IS NULL

### #4

- file: apps/core-api/src/database/migrations/1752000000000-economy-ledger.ts
- line: 143
- quote: CREATE INDEX idx_outbox_events_unpublished ON outbox_events(created_at) WHERE published_at IS NULL

### #5

- file: docs/services/economy-service.md
- line: 91
- quote: Giới hạn đã biết — chưa có consumer và chưa có retention

### writers

### ECONOMY_EVENTS_TOPIC = litmatch.economy.events

- where: EconomyService.purchaseVip — callback withinTransaction của LedgerService.record (economy.service.ts)
- eventType: economy.vip.purchased
- keyedBy: userId — payload không có transactionId nên relay dùng payload.userId làm khóa Kafka
- payloadFields: version (= 1); userId; planId; tier; vipExpiresAt (ISO string)
- sameTransactionAs: Bút toán ledger TransactionType.VipPurchase (Nợ user_wallet / Có system_revenue) và UPDATE wallets.vip_tier/vip_expires_at; ledger còn tự ghi thêm một event economy.diamond.debited cho ví người mua.
- evidence (evidence): apps/core-api/src/modules/economy/economy.service.ts:429, apps/core-api/src/modules/economy/economy.service.ts:431, apps/core-api/src/modules/economy/economy.service.ts:388, apps/core-api/src/modules/economy/economy.service.ts:430

### ECONOMY_EVENTS_TOPIC = litmatch.economy.events

- where: EconomyService.sendGift — callback withinTransaction của LedgerService.record (economy.service.ts); mọi đường tặng quà (party room, video, profile) của GiftService đều đi qua hàm này
- eventType: economy.gift.sent
- keyedBy: transactionId (relay ưu tiên payload.transactionId)
- payloadFields: version (= 1); transactionId; senderUserId; receiverUserId; priceDiamond (string); pointsAwarded (string); ...metadata do GiftService truyền (giftId, giftCode, roomId hoặc ngữ cảnh khác, pointsRatePercent, receiverIsGuest)
- sameTransactionAs: Bút toán ledger TransactionType.GiftSend (Nợ user_wallet người tặng / Có system_gift_pool, thêm chân PTS khi có điểm), gift_events và notification của người nhận (callback withinTransaction); ledger cũng ghi economy.diamond.debited cho ví người tặng.
- evidence (evidence): apps/core-api/src/modules/economy/economy.service.ts:648, apps/core-api/src/modules/economy/economy.service.ts:650, apps/core-api/src/modules/economy/economy.service.ts:639, apps/core-api/src/modules/economy/economy.service.ts:645, apps/core-api/src/modules/gift/gift.service.ts:210

### ECONOMY_EVENTS_TOPIC = litmatch.economy.events

- where: LedgerService.applyWalletDeltas — gọi trong recordInManager của MỌI giao dịch ledger (ledger.service.ts); writer mặc định cho nạp/tiêu Diamond
- eventType: economy.diamond.credited khi balanceDelta > 0, economy.diamond.debited khi balanceDelta < 0; ghi đè bằng outboxEventTypeOverride = economy.diamond.refunded khi reverse từ refund.service.ts và economy.service.ts (admin refund).
- keyedBy: transactionId (relay ưu tiên payload.transactionId); mỗi ví có balanceDelta != 0 sinh một dòng riêng
- payloadFields: version (= 1); transactionId; transactionType; userId; amount (string, giá trị tuyệt đối của balanceDelta)
- sameTransactionAs: Cùng transaction với INSERT transactions (idempotency key unique), INSERT ledger_entries và UPDATE wallets (ví đã khóa FOR UPDATE); replay idempotent trả về sớm nên không ghi event lần hai.
- evidence (evidence): apps/core-api/src/modules/economy/services/ledger.service.ts:478, apps/core-api/src/modules/economy/services/ledger.service.ts:475, apps/core-api/src/modules/economy/services/ledger.service.ts:483, apps/core-api/src/modules/economy/services/ledger.service.ts:484, apps/core-api/src/modules/economy/services/ledger.service.ts:172, apps/core-api/src/modules/economy/services/refund.service.ts:72, apps/core-api/src/modules/economy/economy.service.ts:756

### relay

- class: OutboxRelayService (apps/core-api/src/modules/economy/jobs/outbox-relay.service.ts)

- batchSize: 100 dòng mỗi tick (const BATCH_SIZE), chọn theo created_at ASC

- locking: Một DB transaction mỗi tick: SELECT ... FOR UPDATE SKIP LOCKED (pessimistic_write + skip_locked) trên dòng chưa publish và chưa dead-letter, giữ khóa suốt các lần publish Kafka và chỉ COMMIT sau manager.save(events). runExclusive chặn chồng tick trong process; không dùng advisory lock cụm.

- ordering: Khóa Kafka = payload.transactionId, nếu không có thì payload.userId, nếu không thì id dòng; producer idempotent với maxInFlightRequests=1 giữ thứ tự theo khóa. Không có thứ tự toàn cục: nhiều pod lấy lô khác nhau, dòng lỗi không chặn dòng sau, và các giao dịch khác nhau của một user có khóa khác nhau.

- maxAttempts: ECONOMY_OUTBOX_MAX_ATTEMPTS, mặc định 5 (1–100); so sánh attempts >= maxAttempts sau mỗi lần publish lỗi

- backoff: Không có backoff theo dòng: dòng lỗi được thử lại ở tick kế tiếp (ECONOMY_OUTBOX_RELAY_INTERVAL_MS, mặc định 2000 ms). Chỉ kafkajs tự retry bên trong một lần publish: EVENT_BUS_KAFKA_RETRIES (mặc định 1) và EVENT_BUS_KAFKA_REQUEST_TIMEOUT_MS (mặc định 5000).

- deadLetter: Khi attempts đạt ngưỡng: publishDeadLetter gửi sang topic `<topic>.DLQ` với payload { original, deadLetter: { error, attempts } }, rồi đặt dead_lettered_at (cả khi gửi DLQ lỗi — dòng giữ lại làm nguồn replay thủ công và không được relay lại).

- onBrokerDown: publish ném lỗi thì attempts + 1 và lưu last_error, dòng vẫn published_at NULL. Dòng bị thử đủ ECONOMY_OUTBOX_MAX_ATTEMPTS tick liên tiếp thành dead_lettered_at, và khi broker down hẳn thì không có bản nào trên DLQ; không có circuit-breaker nên mỗi dòng trong lô vẫn thử publish tuần tự khi transaction giữ khóa.

### evidence

### #1

- file: apps/core-api/src/modules/economy/jobs/outbox-relay.service.ts
- line: 18
- quote: const BATCH_SIZE = 100;

### #2

- file: apps/core-api/src/modules/economy/jobs/outbox-relay.service.ts
- line: 71
- quote: .setLock('pessimistic_write')

### #3

- file: apps/core-api/src/modules/economy/jobs/outbox-relay.service.ts
- line: 72
- quote: .setOnLocked('skip_locked')

### #4

- file: apps/core-api/src/modules/economy/jobs/outbox-relay.service.ts
- line: 75
- quote: .orderBy('e.created_at', 'ASC')

### #5

- file: apps/core-api/src/modules/economy/jobs/outbox-relay.service.ts
- line: 20
- quote: const ORDERING_KEY_FIELDS = ['transactionId', 'userId'] as const;

### #6

- file: apps/core-api/src/modules/economy/jobs/outbox-relay.service.ts
- line: 66
- quote: return this.job.runExclusive(async () => {

### #7

- file: apps/core-api/src/modules/economy/jobs/outbox-relay.service.ts
- line: 86
- quote: event.attempts += 1;

### #8

- file: apps/core-api/src/modules/economy/jobs/outbox-relay.service.ts
- line: 98
- quote: if (event.attempts >= this.maxAttempts()) {

### #9

- file: apps/core-api/src/modules/economy/jobs/outbox-relay.service.ts
- line: 117
- quote: await this.eventBus.publishDeadLetter(

### #10

- file: apps/core-api/src/modules/economy/jobs/outbox-relay.service.ts
- line: 144
- quote: event.deadLetteredAt = new Date();

### #11

- file: apps/core-api/src/modules/economy/jobs/outbox-relay.service.ts
- line: 103
- quote: await manager.save(events);

### #12

- file: apps/core-api/src/common/events/kafka-event-bus.adapter.ts
- line: 187
- quote: One in-flight request and Kafka idempotence preserve ordering for a stable key and make

### #13

- file: apps/core-api/src/common/events/kafka-event-bus.adapter.ts
- line: 188
- quote: The outbox still remains at-least-once across DB crashes.

### #14

- file: apps/core-api/src/common/events/kafka-event-bus.adapter.ts
- line: 141
- quote: topic: deadLetterTopic(event.topic),

### #15

- file: apps/core-api/src/common/events/events.constants.ts
- line: 3
- quote: export const EVENT_BUS_DEAD_LETTER_SUFFIX = '.DLQ';

### #16

- file: apps/core-api/src/config/env.validation.ts
- line: 377
- quote: ECONOMY_OUTBOX_MAX_ATTEMPTS: Joi.number()

### #17

- file: apps/core-api/src/config/env.validation.ts
- line: 270
- quote: EVENT_BUS_KAFKA_RETRIES: Joi.number().integer().min(0).max(10).default(1),

### #18

- file: apps/core-api/src/config/env.validation.ts
- line: 266
- quote: EVENT_BUS_KAFKA_REQUEST_TIMEOUT_MS: Joi.number()

### consumersInRepo

### (không có consumer nào trong repo)

- status: none
- note: EventBusPort.subscribe() chỉ được định nghĩa trong KafkaEventBusAdapter (consumer group, tối đa 3 lần thử cách 250 ms rồi gửi DLQ) và không có call site nào trong apps/ hay libs/ (đã grep). Chưa có inbox/dedup: envelope mang id để consumer tương lai tự dedupe.
- evidence (evidence): apps/core-api/src/common/events/kafka-event-bus.adapter.ts:151, apps/core-api/src/common/events/kafka-event-bus.adapter.ts:261, apps/core-api/src/common/events/kafka-event-bus.adapter.ts:113, docs/services/economy-service.md:91

### topics

### ECONOMY_EVENTS_TOPIC

- value: litmatch.economy.events
- dlq: litmatch.economy.events.DLQ (deadLetterTopic = topic + EVENT_BUS_DEAD_LETTER_SUFFIX)
- evidence (evidence): apps/core-api/src/modules/economy/economy.constants.ts:11, apps/core-api/src/common/events/events.constants.ts:3, docker-compose.yml:51

- profileSwitch: ECONOMY_OUTBOX_RELAY_ENABLED (mặc định false) quyết định relay có chạy: false thì outbox vẫn được ghi atomic nhưng không có interval relay và không kết nối Kafka. .env.example, k8s base, deploy/hosted và deploy/production/compose.yml đều đặt false; bật cần KAFKA_BROKERS hợp lệ và quyết định trước consumer + retention.

### profileSwitchEvidence

### #1

- file: apps/core-api/src/config/env.validation.ts
- line: 372
- quote: ECONOMY_OUTBOX_RELAY_ENABLED: Joi.boolean().default(false),

### #2

- file: .env.example
- line: 170
- quote: ECONOMY_OUTBOX_RELAY_ENABLED=false

### #3

- file: k8s/base/core-api/configmap.yaml
- line: 76
- quote: ECONOMY_OUTBOX_RELAY_ENABLED: 'false'

### #4

- file: deploy/hosted/core-api.env.example
- line: 37
- quote: ECONOMY_OUTBOX_RELAY_ENABLED=false

### #5

- file: deploy/production/compose.yml
- line: 88
- quote: ECONOMY_OUTBOX_RELAY_ENABLED: 'false'

### #6

- file: docs/runbooks/hosted-free-release.md
- line: 56
- quote: Kafka giữ `ECONOMY_OUTBOX_RELAY_ENABLED=false`; event vẫn nằm trong outbox để replay sau.

### #7

- file: apps/core-api/src/common/events/kafka-event-bus.adapter.ts
- line: 182
- quote: private async getProducer(): Promise<Producer> {

### #8

- file: docker-compose.yml
- line: 1
- quote: # Hạ tầng local cho dev — Postgres, Redis, Kafka (KRaft, 1 node)
