# Async flows: realtime, outbox, jobs, queues: queues

id: system:async:queues

Written by an AI agent from the code; every file:line quote was checked automatically (exists within ±3 lines) but NO independent verification pass has run yet. Treat claims as strong leads; open the cited file before editing.

### matching:queue:{matchType}:{region}:{ageBand} (ZSET) + matching:shards:active (SET)

- medium: Redis sorted set + set (chỉ mục dẫn xuất từ Postgres match_tickets)
- module: matching
- producer: MatchingService.ensureEnqueued (ZADD NX khi join/replay), MatchingService.speedup (ZADD XX score tuyệt đối), TicketSweeperService và MatcherWorkerService (requeue)
- consumer: MatcherWorkerService (ZPOPMIN 2 mỗi lần; kích hoạt bởi wake-up trong process và interval backstop)
- ordering: Score tăng dần = enqueuedAt + trustPenaltyMs − priorityBoostMs; hòa thì theo member (ticketId).
- ackOrRetry: Không có ACK: pop là xóa khỏi Redis. Ticket không ghép được thì requeue với score gốc; ticket vắng khỏi Redis phục hồi bằng retry join cùng Idempotency-Key (ensureEnqueued) hoặc bị sweeper expire.
- whatIfWorkerDies: Matcher stateless nên pod khác tiếp tục (wake-up của pod đó hoặc interval backstop qua advisory lock). Ticket đã pop mà chưa commit/requeue có thể mất khỏi Redis dù DB còn queued, tới khi hết MATCHING_QUEUE_MAX_WAIT_SECONDS (120s).
- evidence (evidence): apps/core-api/src/modules/matching/redis/matching-redis.provider.ts:21, apps/core-api/src/modules/matching/redis/redis-matching-queue.adapter.ts:26, apps/core-api/src/modules/matching/matching.service.ts:469, apps/core-api/src/modules/matching/jobs/matcher-worker.service.ts:361

### match_tickets

- medium: Bảng Postgres (state machine queued → confirmed | expired | cancelled; matched/pending_confirm chỉ còn cho luồng legacy)
- module: matching
- producer: MatchingService.joinQueue (INSERT queued, partial unique 1 ticket active/user), TicketSweeperService (ticket requeue mới)
- consumer: MatcherWorkerService.tryPair (khóa FOR UPDATE rồi chuyển confirmed), TicketSweeperService (expire), cancelTicket/confirmTicket
- ordering: Không có thứ tự xử lý riêng; thứ tự ghép do ZSET quyết định; sweeper duyệt enqueued_at ASC, id ASC.
- ackOrRetry: Chuyển trạng thái trong transaction có khóa dòng; sweeper là chốt chặn expire; không retry tự động ngoài sweeper.
- whatIfWorkerDies: Trạng thái nằm ở Postgres nên không mất; ticket queued quá 120s được sweeper expire bởi bất kỳ pod nào giành khóa.
- evidence (evidence): apps/core-api/src/modules/matching/jobs/matcher-worker.service.ts:63, apps/core-api/src/modules/matching/jobs/ticket-sweeper.service.ts:111

### movie_match_queue_entries

- medium: Bảng Postgres (PK user_id, cột queued_at)
- module: movie-match
- producer: MovieMatchService.joinAnonQueue (INSERT ... ON CONFLICT DO NOTHING) — POST /api/v1/movie-match/anon/queue
- consumer: Chính request join của người vào sau (ghép inline với tối đa 20 entry cũ nhất, dưới pg_advisory_xact_lock); không có worker
- ordering: queued_at ASC, user_id ASC.
- ackOrRetry: Ghép xong xóa cả hai entry trong transaction; entry quá MOVIE_MATCH_QUEUE_MAX_WAIT_SECONDS (120s) bị xóa lazily ở lần join/GET current kế tiếp.
- whatIfWorkerDies: Không có worker để chết; nếu không ai gọi join/GET current thì entry hết hạn nằm lại cho tới lượt đọc sau. Người chờ biết kết quả bằng poll GET /movie-match/anon/current (5s), không có realtime khi ghép được cặp.
- evidence (evidence): apps/core-api/src/modules/movie-match/movie-match.service.ts:308, apps/core-api/src/modules/movie-match/movie-match.service.ts:321, apps/core-api/src/modules/movie-match/movie-match.constants.ts:11

### palm_match_queue_entries

- medium: Bảng Postgres (PK user_id, cột queued_at; index idx_palm_match_queue_order)
- module: palm-match
- producer: PalmMatchService.joinQueue (INSERT ... ON CONFLICT DO NOTHING) — POST /api/v1/palm-match/queue
- consumer: Chính request join của người vào sau (ghép inline dưới pg_advisory_xact_lock); không có worker
- ordering: queued_at ASC, user_id ASC, LIMIT 20.
- ackOrRetry: Ghép xong xóa hai entry; entry quá PALM_MATCH_QUEUE_MAX_WAIT_SECONDS (120s) bị xóa lazily khi có join/GET current.
- whatIfWorkerDies: Không có worker; không có realtime event cho Palm Match nên người chờ chỉ biết bằng poll GET /palm-match/current (5s).
- evidence (evidence): apps/core-api/src/modules/palm-match/palm-match.service.ts:128, apps/core-api/src/modules/palm-match/palm-match.constants.ts:34, apps/web/src/features/palm-match/api.ts:10

### outbox_events

- medium: Bảng Postgres (transactional outbox của Economy)
- module: economy
- producer: LedgerService.applyWalletDeltas, EconomyService.purchaseVip, EconomyService.sendGift (cùng transaction với ledger)
- consumer: OutboxRelayService (nếu ECONOMY_OUTBOX_RELAY_ENABLED=true) — publish lên Kafka
- ordering: created_at ASC trong mỗi lô 100; nhiều pod lấy lô khác nhau song song (SKIP LOCKED) nên không có thứ tự toàn cục.
- ackOrRetry: published_at đặt sau khi Kafka ack và transaction relay commit; lỗi thì attempts + 1 mỗi tick, tới ECONOMY_OUTBOX_MAX_ATTEMPTS thì DLQ + dead_lettered_at.
- whatIfWorkerDies: Relay tắt mặc định nên dòng tích lũy với published_at NULL. Pod relay chết giữa lô thì transaction rollback và dòng được xử lý lại (có thể publish trùng).
- evidence (evidence): apps/core-api/src/modules/economy/jobs/outbox-relay.service.ts:74, apps/core-api/src/modules/economy/jobs/outbox-relay.service.ts:103, apps/core-api/src/modules/economy/services/ledger.service.ts:478

### litmatch.economy.events (+ litmatch.economy.events.DLQ)

- medium: Kafka topic (broker ngoài repo; docker-compose dev chạy Kafka KRaft 1 node)
- module: economy (common/events)
- producer: OutboxRelayService qua KafkaEventBusAdapter.publish / publishDeadLetter (producer idempotent, maxInFlightRequests 1)
- consumer: Không có consumer trong repo; KafkaEventBusAdapter.subscribe có sẵn nhưng không được gọi
- ordering: Theo khóa Kafka (transactionId | userId | id) trong một partition; không có thứ tự toàn cục.
- ackOrRetry: Producer: kafkajs retry EVENT_BUS_KAFKA_RETRIES (1) rồi lỗi trả về relay. Consumer (chưa dùng): tối đa EVENT_BUS_CONSUMER_MAX_ATTEMPTS (3) lần cách 250 ms rồi DLQ; DLQ lỗi thì không commit offset.
- whatIfWorkerDies: Không có consumer nên không có gì để chết; event nằm lại trong outbox_events (relay tắt) hoặc trong topic (relay bật).
- evidence (evidence): apps/core-api/src/modules/economy/economy.constants.ts:11, apps/core-api/src/common/events/kafka-event-bus.adapter.ts:151, docker-compose.yml:1

### realtime:user:{userId}

- medium: Redis Pub/Sub (không lưu, không queue)
- module: common/realtime (publisher) → signaling-gateway (subscriber)
- producer: publishRealtimeEvent từ 7 module core-api và job streak-warning
- consumer: SignalingGateway.relay trên mọi pod (PSUBSCRIBE realtime:user:*)
- ordering: Không có; envelope không mang số thứ tự.
- ackOrRetry: Không có ACK/retry: publish lỗi chỉ log warn; subscriber không nhận khi offline.
- whatIfWorkerDies: Pod gateway chết/mất subscription thì socket của pod đó không nhận event cho tới khi phục hồi; web dựa vào poll REST và refetch sau reconnect.
- evidence (evidence): apps/core-api/src/common/realtime/redis-realtime-publisher.adapter.ts:6, apps/signaling-gateway/src/app/signaling.gateway.ts:346

### iap_receipts (hàng đợi quét hoàn tiền theo refund_checked_at)

- medium: Bảng Postgres dùng làm work queue (claim bằng FOR UPDATE SKIP LOCKED)
- module: economy
- producer: EconomyService.creditFromIap ghi receipt `credited` cùng transaction ledger
- consumer: IapRefundPollService (nếu ECONOMY_REFUND_POLL_ENABLED=true): claim 200 dòng/lần rồi COMMIT ngay
- ordering: refund_checked_at ASC NULLS FIRST để mỗi lần quét tiến sang receipt khác.
- ackOrRetry: Đóng dấu refund_checked_at ngay lúc claim (trước khi gọi Apple/Google); hoàn tiền idempotent theo key refund:{provider}:{id}; không retry riêng ngoài vòng quét kế tiếp.
- whatIfWorkerDies: Pod chết sau khi claim thì receipt đã được đóng dấu nhưng chưa kiểm tra; chúng chỉ được quét lại sau khi các receipt khác được xoay (mặc định interval 1 giờ).
- evidence (evidence): apps/core-api/src/modules/economy/jobs/iap-refund-poll.service.ts:100, apps/core-api/src/modules/economy/jobs/iap-refund-poll.service.ts:107

### videos (hàng đợi dọn object: status = failed AND storage_cleaned_at IS NULL)

- medium: Bảng Postgres dùng làm work queue (quét theo updated_at)
- module: short-video
- producer: VideoSweeperService (video kẹt uploading quá VIDEO_UPLOAD_TIMEOUT_SECONDS → failed) và ShortVideoService.finalizeUpload (transcode lỗi → failed)
- consumer: VideoSweeperService (xóa object qua VideoStoragePort rồi đặt storage_cleaned_at)
- ordering: updated_at ASC, id ASC; hàng xóa lỗi bị đẩy updated_at về cuối.
- ackOrRetry: storage_cleaned_at là dấu hoàn tất; lỗi xóa thì warn và deferRetry để không chặn các hàng sau.
- whatIfWorkerDies: Hàng còn trong bảng; tick kế tiếp của pod nào giành khóa sẽ làm tiếp (bỏ tick khi runtime idle).
- evidence (evidence): apps/core-api/src/modules/short-video/jobs/video-sweeper.service.ts:90, apps/core-api/src/modules/short-video/jobs/video-sweeper.service.ts:119, apps/core-api/src/modules/short-video/short-video.service.ts:163
