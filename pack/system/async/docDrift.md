# Async flows: realtime, outbox, jobs, queues: docDrift

id: system:async:docDrift

Written by an AI agent from the code; every file:line quote was checked automatically (exists within ±3 lines) but NO independent verification pass has run yet. Treat claims as strong leads; open the cited file before editing.

### #1

- doc: docs/services/realtime-gateway.md § 2
- claim: Danh sách "Event hiện có" gồm 22 tên, trong đó có `match.matched`.
- actual: RealtimeEvents có 26 tên: thiếu trong doc là party.member.disconnected, party.member.reconnected, party.speaker.invite.received, party.comment.created; ngược lại match.matched không có producer nào.
- evidence (evidence): docs/services/realtime-gateway.md:23, libs/common-dtos/src/lib/realtime-events.ts:75, libs/common-dtos/src/lib/realtime-events.ts:63, libs/common-dtos/src/lib/realtime-events.ts:42

### #2

- doc: docs/services/realtime-gateway.md § 3
- claim: Publish luôn chạy SAU khi DB transaction commit.
- actual: MovieMatchService.publishAnonPlayback (phiên Movie Match ẩn danh) được gọi bên trong dataSource.transaction ở updateAnonState và endAnonWatch, tức trước COMMIT.
- evidence (evidence): docs/services/realtime-gateway.md:35, apps/core-api/src/modules/movie-match/movie-match.service.ts:438, apps/core-api/src/modules/movie-match/movie-match.service.ts:421

### #3

- doc: docs/services/realtime-gateway.md § 4
- claim: Bảng "Producer hiện có" gồm matching, soul-match, calling, friend (message), party-room (join/leave/role/close) và gift.
- actual: Còn có producer movie-match (4 event), mini-game (2 event), friend.streak.increased/at_risk (job friend-streak-warning), party member disconnect/reconnect, speaker invite, comment, host disconnect/reconnect và invite.service phát match.confirmed.
- evidence (evidence): docs/services/realtime-gateway.md:53, apps/core-api/src/modules/movie-match/movie-match.service.ts:173, apps/core-api/src/modules/mini-game/mini-game.service.ts:227, apps/core-api/src/modules/friend/jobs/streak-warning.job.ts:94

### #4

- doc: specs/realtime.asyncapi.yaml
- claim: Là tài liệu AsyncAPI của kênh realtime `/signaling` (nhận event gửi tới user đã xác thực).
- actual: Chỉ liệt kê 8 message (soul.message, match.confirmed, call.ended, friend.message, party.room.closed, gift.sent, movie.state.changed, minigame.session.resolved) trong 26 event; nguồn đầy đủ là libs/common-dtos/src/lib/realtime-events.ts.
- evidence (evidence): specs/realtime.asyncapi.yaml:32, libs/common-dtos/src/lib/realtime-events.ts:90

### #5

- doc: docs/services/economy-service.md § 4
- claim: Bốn loại event (credited/debited/vip.purchased/refunded) cùng payload `{version: 1, transactionId, userId, amount, transactionType}`.
- actual: Có năm loại: thêm `economy.gift.sent`; payload của economy.vip.purchased là { version, userId, planId, tier, vipExpiresAt } (không có transactionId/amount) và của economy.gift.sent là { version, transactionId, senderUserId, receiverUserId, priceDiamond, pointsAwarded, ...metadata }.
- evidence (evidence): docs/services/economy-service.md:89, apps/core-api/src/modules/economy/economy.service.ts:650, apps/core-api/src/modules/economy/economy.service.ts:437

### #6

- doc: docs/services/economy-service.md § 4
- claim: Adapter Kafka có sẵn đường consumer + inbox/dedup nhưng chưa được dùng.
- actual: KafkaEventBusAdapter.subscribe có consumer group, retry và DLQ nhưng không có inbox/dedup nào: chỉ có envelope.id ổn định để consumer tương lai tự dedupe.
- evidence (evidence): docs/services/economy-service.md:91, apps/core-api/src/common/events/kafka-event-bus.adapter.ts:113

### #7

- doc: docs/adr/0011-hybrid-modular-layered-event-driven.md
- claim: Envelope integration event tối thiểu gồm `id, type, version, occurredAt, payload` (tùy chọn correlationId, causationId).
- actual: EventEnvelope trong code gồm id, topic, type, version, key, payload: không có occurredAt/correlationId/causationId, thêm topic và key.
- evidence (evidence): docs/adr/0011-hybrid-modular-layered-event-driven.md:44, apps/core-api/src/common/events/event-bus.port.ts:6, apps/core-api/src/common/events/event-bus.port.ts:11

### #8

- doc: docs/services/matching-service.md § 2
- claim: Score sorted set = `enqueuedAtMs - speedupBoostMs`.
- actual: Score = enqueuedAt + trustPenaltyMs − priorityBoostMs (có cộng phạt trust).
- evidence (evidence): docs/services/matching-service.md:27, apps/core-api/src/modules/matching/redis/matching-redis.provider.ts:50

### #9

- doc: docs/services/matching-service.md § 3
- claim: Sweeper expire ticket `queued` quá MATCHING_QUEUE_MAX_WAIT_SECONDS kể từ `createdAt`.
- actual: TicketSweeperService so sánh với `enqueued_at` (ticket requeue là ticket mới nên enqueued_at = created_at).
- evidence (evidence): docs/services/matching-service.md:48, apps/core-api/src/modules/matching/jobs/ticket-sweeper.service.ts:111

### #10

- doc: libs/common-dtos/src/lib/realtime-events.ts (comment CallEndedEventData.reason)
- claim: reason gồm completed | free_limit | insufficient_balance | pending_timeout.
- actual: Enum CallEndReason có insufficient_balance nhưng không có nhánh code nào kết thúc call bằng lý do này; docs/services/calling-service.md ghi Voice Match không tính Diamond.
- evidence (evidence): libs/common-dtos/src/lib/realtime-events.ts:139, apps/core-api/src/modules/calling/entities/call-session.entity.ts:25, docs/services/calling-service.md:74

### #11

- doc: libs/common-dtos/src/lib/realtime-events.ts (comment PartyRoomClosedEventData.reason)
- claim: reason là host_left | empty | swept.
- actual: Enum PartyRoomCloseReason gồm host_left | finished | swept | error; không có giá trị `empty`.
- evidence (evidence): libs/common-dtos/src/lib/realtime-events.ts:199, apps/core-api/src/modules/party-room/entities/party-room.entity.ts:27

### #12

- doc: apps/core-api/src/modules/matching/matching.metrics.ts (comment observeMatched)
- claim: observeMatched chạy trước vòng publish realtime `match.matched`.
- actual: matcher-worker publish `match.confirmed`; `match.matched` không được publish ở đâu cả.
- evidence (evidence): apps/core-api/src/modules/matching/matching.metrics.ts:31, apps/core-api/src/modules/matching/jobs/matcher-worker.service.ts:374
