# Async flows: realtime, outbox, jobs, queues: jobs

id: system:async:jobs

Written by an AI agent from the code; every file:line quote was checked automatically (exists within ±3 lines) but NO independent verification pass has run yet. Treat claims as strong leads; open the cited file before editing.

### CallTickerService

- jobName: calling-ticker
- module: calling
- intervalExpression: this.config.getOrThrow('CALLING_TICKER_INTERVAL_MS')
- intervalEnvVarAndDefault: CALLING_TICKER_INTERVAL_MS, mặc định 1000 ms (tối thiểu 200)
- skipWhenIdle: false
- clusterSingleton: true
- whatItDoes: Mỗi tick (lô tối đa 100) kết thúc call `pending` quá CALLING_PENDING_TIMEOUT_SECONDS (60s) bằng `pending_timeout`, call `active` quá cửa sổ reconnect (30s) bằng `completed`, và Voice Match `active` quá CALLING_FREE_CALL_SECONDS (420s) bằng `free_limit`. Không gọi Economy, không trừ Diamond.
- touches: call_sessions; match_sessions (qua MatchingService.endVoiceSessionForCall); LiveKit RoomService.deleteRoom (external); Redis PUBLISH realtime:user:{userId} (call.ended)
- concurrencyControl: ManagedInterval chặn chồng tick trong process + pg_try_advisory_xact_lock giữa các pod; endById khóa dòng call FOR UPDATE và chỉ transition `ended` một lần.
- failureMode: Lỗi ở sweepPending/sweepReconnectTimeouts (không try/catch từng call) làm dừng phần còn lại của tick; processActiveCalls bắt lỗi từng call và thử lại ở tick sau. Mọi lỗi/follower đặt clusterRetryAfter >= 5s nên failover sang pod khác có thể trễ vài giây.
- evidence (evidence): apps/core-api/src/modules/calling/jobs/call-ticker.service.ts:22, apps/core-api/src/modules/calling/jobs/call-ticker.service.ts:51, apps/core-api/src/modules/calling/jobs/call-ticker.service.ts:57, apps/core-api/src/modules/calling/jobs/call-ticker.service.ts:90, apps/core-api/src/modules/calling/jobs/call-ticker.service.ts:132, apps/core-api/src/config/env.validation.ts:562, docs/runbooks/hosted-free-release.md:63, docs/services/calling-service.md:74

### IapRefundPollService

- jobName: economy-iap-refund-poll
- module: economy
- intervalExpression: this.config.getOrThrow('ECONOMY_REFUND_POLL_INTERVAL_MS')
- intervalEnvVarAndDefault: ECONOMY_REFUND_POLL_INTERVAL_MS, mặc định 3_600_000 ms (1 giờ, tối thiểu 60_000); chỉ đăng ký khi ECONOMY_REFUND_POLL_ENABLED=true (mặc định false)
- skipWhenIdle: false
- clusterSingleton: false
- whatItDoes: Backstop cho webhook hoàn tiền bị mất: claim lô <= 200 `iap_receipts` còn `credited` trong ECONOMY_REFUND_POLL_WINDOW_DAYS (60 ngày), hỏi Apple Get Refund History từng receipt và Google Voided Purchases một lần/run, rồi gọi RefundService.refundIapPurchase.
- touches: iap_receipts (watermark refund_checked_at); transactions, ledger_entries, wallets, outbox_events (qua ledger.reverse); Apple Server API (external); Google Play Voided Purchases API (external)
- concurrencyControl: Không clusterSingleton: nhiều pod claim lô bằng SELECT ... FOR UPDATE SKIP LOCKED rồi COMMIT ngay + đóng dấu refund_checked_at; hoàn tiền idempotent qua key `refund:{provider}:{providerTransactionId}`.
- failureMode: Lỗi gateway Apple/Google không có try/catch từng receipt nên làm dừng run đó (log ở ManagedInterval); watermark đã được đóng dấu nên lô lỗi bị xoay xuống cuối hàng, thử lại ở run sau. Khi lô đầy (200) chỉ log warn.
- evidence (evidence): apps/core-api/src/modules/economy/jobs/iap-refund-poll.service.ts:25, apps/core-api/src/modules/economy/jobs/iap-refund-poll.service.ts:64, apps/core-api/src/modules/economy/jobs/iap-refund-poll.service.ts:68, apps/core-api/src/modules/economy/jobs/iap-refund-poll.service.ts:97, apps/core-api/src/modules/economy/jobs/iap-refund-poll.service.ts:100, apps/core-api/src/config/env.validation.ts:457, apps/core-api/src/config/env.validation.ts:458

### OutboxRelayService

- jobName: economy-outbox-relay
- module: economy
- intervalExpression: this.config.getOrThrow('ECONOMY_OUTBOX_RELAY_INTERVAL_MS')
- intervalEnvVarAndDefault: ECONOMY_OUTBOX_RELAY_INTERVAL_MS, mặc định 2000 ms (tối thiểu 200); chỉ đăng ký khi ECONOMY_OUTBOX_RELAY_ENABLED=true (mặc định false)
- skipWhenIdle: false
- clusterSingleton: false
- whatItDoes: Đọc tối đa 100 dòng `outbox_events` chưa publish và chưa dead-letter, publish từng dòng lên Kafka qua EventBusPort, đánh dấu `published_at`; lỗi thì tăng `attempts`, đủ ECONOMY_OUTBOX_MAX_ATTEMPTS thì chuyển DLQ.
- touches: outbox_events; Kafka topic litmatch.economy.events và litmatch.economy.events.DLQ (external)
- concurrencyControl: Không clusterSingleton: runExclusive trong process + SELECT ... FOR UPDATE SKIP LOCKED (pessimistic_write) trong một DB transaction bao trọn các lần publish của lô.
- failureMode: Kafka lỗi: mỗi tick mỗi dòng +1 attempts (không backoff riêng); đủ maxAttempts thì dòng bị đánh dấu dead_lettered_at (kể cả khi gửi DLQ cũng lỗi) và không được relay tự động nữa. Crash sau khi publish nhưng trước COMMIT sẽ publish lại (at-least-once).
- evidence (evidence): apps/core-api/src/modules/economy/jobs/outbox-relay.service.ts:17, apps/core-api/src/modules/economy/jobs/outbox-relay.service.ts:44, apps/core-api/src/modules/economy/jobs/outbox-relay.service.ts:50, apps/core-api/src/modules/economy/jobs/outbox-relay.service.ts:72, apps/core-api/src/modules/economy/jobs/outbox-relay.service.ts:18, apps/core-api/src/config/env.validation.ts:372, apps/core-api/src/config/env.validation.ts:373

### ReconciliationService

- jobName: economy-reconciliation-fast
- module: economy
- intervalExpression: this.config.getOrThrow('ECONOMY_RECONCILIATION_FAST_INTERVAL_MS')
- intervalEnvVarAndDefault: ECONOMY_RECONCILIATION_FAST_INTERVAL_MS, mặc định 60_000 ms (tối thiểu 10_000); cả hai tier bị tắt bởi ECONOMY_RECONCILIATION_ENABLED=false (mặc định true)
- skipWhenIdle: false
- clusterSingleton: true
- whatItDoes: Tier nhanh, chỉ đọc: kiểm tổng Nợ = tổng Có theo currency trên `ledger_entries` và đếm `iap_receipts` đã credited nhưng thiếu transaction. Lệch thì log error + metric economy_reconciliation_mismatch_total; không tự sửa.
- touches: ledger_entries; iap_receipts; metric economy_reconciliation_*
- concurrencyControl: pg_try_advisory_xact_lock theo jobName `economy-reconciliation-fast`; tick chồng bị chặn bởi scheduledRunInFlight.
- failureMode: scheduledRun nuốt lỗi và đặt gauge economy_reconciliation_last_run_status{tier}=0 để alert thấy; vì lỗi không nổi lên ManagedInterval nên không kích hoạt backoff follower.
- evidence (evidence): apps/core-api/src/modules/economy/jobs/reconciliation.service.ts:19, apps/core-api/src/modules/economy/jobs/reconciliation.service.ts:85, apps/core-api/src/modules/economy/jobs/reconciliation.service.ts:88, apps/core-api/src/modules/economy/jobs/reconciliation.service.ts:94, apps/core-api/src/modules/economy/jobs/reconciliation.service.ts:121, apps/core-api/src/config/env.validation.ts:382, apps/core-api/src/config/env.validation.ts:390

### ReconciliationService

- jobName: economy-reconciliation-deep
- module: economy
- intervalExpression: this.config.getOrThrow('ECONOMY_RECONCILIATION_INTERVAL_MS')
- intervalEnvVarAndDefault: ECONOMY_RECONCILIATION_INTERVAL_MS, mặc định 300_000 ms (tối thiểu 10_000)
- skipWhenIdle: false
- clusterSingleton: true
- whatItDoes: Tier sâu, chỉ đọc: lấy mẫu 100 ví cập nhật gần nhất (ORDER BY updated_at DESC) và so `wallets.balance` với số dư suy ra từ `ledger_entries` của tài khoản `user_wallet`.
- touches: wallets; ledger_entries; ledger_accounts; metric economy_reconciliation_*
- concurrencyControl: pg_try_advisory_xact_lock theo jobName `economy-reconciliation-deep`; tick chồng bị chặn bởi scheduledRunInFlight.
- failureMode: Giống tier fast: lỗi bị nuốt, gauge tier=0; chỉ kiểm mẫu 100 ví nên ví ngoài mẫu có thể lệch mà chưa bị phát hiện.
- evidence (evidence): apps/core-api/src/modules/economy/jobs/reconciliation.service.ts:20, apps/core-api/src/modules/economy/jobs/reconciliation.service.ts:96, apps/core-api/src/modules/economy/jobs/reconciliation.service.ts:98, apps/core-api/src/modules/economy/jobs/reconciliation.service.ts:104, apps/core-api/src/modules/economy/jobs/reconciliation.service.ts:22, apps/core-api/src/config/env.validation.ts:383

### StorySweeperService

- jobName: story-sweeper
- module: feed
- intervalExpression: this.config.getOrThrow('STORY_SWEEPER_INTERVAL_MS')
- intervalEnvVarAndDefault: STORY_SWEEPER_INTERVAL_MS, mặc định 3_600_000 ms (1 giờ, tối thiểu 60_000)
- skipWhenIdle: true
- clusterSingleton: true
- whatItDoes: Hard-delete tối đa 500 story đã hết hạn (`expires_at <= now()`) mỗi tick; `story_views` tự cascade. Chỉ dọn rác, read-path đã tự lọc story hết hạn.
- touches: stories; story_views (ON DELETE CASCADE)
- concurrencyControl: pg_try_advisory_xact_lock theo jobName `story-sweeper`; DELETE theo lô trong một câu SQL.
- failureMode: Lỗi chỉ log; tick sau xóa tiếp phần tồn đọng. Bỏ tick khi runtime idle nên story hết hạn có thể nằm lại lâu nhưng không hiển thị.
- evidence (evidence): apps/core-api/src/modules/feed/jobs/story-sweeper.service.ts:16, apps/core-api/src/modules/feed/jobs/story-sweeper.service.ts:42, apps/core-api/src/modules/feed/jobs/story-sweeper.service.ts:48, apps/core-api/src/modules/feed/jobs/story-sweeper.service.ts:49, apps/core-api/src/modules/feed/jobs/story-sweeper.service.ts:65, apps/core-api/src/config/env.validation.ts:722

### StreakWarningJob

- jobName: friend-streak-warning
- module: friend
- intervalExpression: this.config.getOrThrow('STREAK_WARNING_CHECK_INTERVAL_MS')
- intervalEnvVarAndDefault: STREAK_WARNING_CHECK_INTERVAL_MS, mặc định 3_600_000 ms (1 giờ, tối thiểu 60_000); chỉ cảnh báo sau giờ UTC STREAK_WARNING_HOURS (mặc định 20)
- skipWhenIdle: false
- clusterSingleton: true
- whatItDoes: Tìm conversation còn streak > 0, chưa nhắn hôm nay và chưa cảnh báo hôm nay; publish `friend.streak.at_risk` cho cả hai bạn, tạo notification + push, rồi đánh dấu lastWarningSentAt. Không bao giờ ghi currentStreak/longestStreak.
- touches: conversation_streaks; conversations; notifications; Redis PUBLISH realtime:user:{userId} (friend.streak.at_risk); Push provider / Web Push (external, best-effort)
- concurrencyControl: pg_try_advisory_xact_lock theo jobName `friend-streak-warning`; idempotent theo lastWarningSentAt (tối đa một lần/ngày UTC/conversation).
- failureMode: Lỗi từng conversation được log và thử lại ở tick sau; lỗi tạo notification/push chỉ warn; markWarningSent chạy sau publish nên lỗi giữa chừng có thể cảnh báo lặp.
- evidence (evidence): apps/core-api/src/modules/friend/jobs/streak-warning.job.ts:28, apps/core-api/src/modules/friend/jobs/streak-warning.job.ts:56, apps/core-api/src/modules/friend/jobs/streak-warning.job.ts:62, apps/core-api/src/modules/friend/jobs/streak-warning.job.ts:94, apps/core-api/src/modules/friend/jobs/streak-warning.job.ts:121, apps/core-api/src/modules/friend/services/streak.service.ts:137, apps/core-api/src/config/env.validation.ts:526, apps/core-api/src/config/env.validation.ts:525

### InviteSweeperService

- jobName: matching-invite-sweeper
- module: matching
- intervalExpression: this.config.getOrThrow('MATCHING_INVITE_SWEEPER_INTERVAL_MS')
- intervalEnvVarAndDefault: MATCHING_INVITE_SWEEPER_INTERVAL_MS, mặc định 60_000 ms (tối thiểu 10_000)
- skipWhenIdle: true
- clusterSingleton: true
- whatItDoes: Chuyển tối đa 200 `match_invites` Pending quá `expires_at` sang Expired. Chỉ là dọn dẹp: accept/decline/cancel tự lazy-expire tại thời điểm hành động.
- touches: match_invites
- concurrencyControl: pg_try_advisory_xact_lock theo jobName `matching-invite-sweeper`; UPDATE có điều kiện status=Pending.
- failureMode: Lỗi chỉ log, tick sau thử lại; bỏ tick khi idle không ảnh hưởng đúng đắn vì đã có lazy-expire.
- evidence (evidence): apps/core-api/src/modules/matching/jobs/invite-sweeper.service.ts:17, apps/core-api/src/modules/matching/jobs/invite-sweeper.service.ts:51, apps/core-api/src/modules/matching/jobs/invite-sweeper.service.ts:52, apps/core-api/src/modules/matching/jobs/invite-sweeper.service.ts:64, apps/core-api/src/modules/matching/jobs/invite-sweeper.service.ts:18, apps/core-api/src/config/env.validation.ts:710

### MatcherWorkerService

- jobName: matching-matcher
- module: matching
- intervalExpression: this.config.getOrThrow('MATCHING_MATCHER_INTERVAL_MS')
- intervalEnvVarAndDefault: MATCHING_MATCHER_INTERVAL_MS, mặc định 10_000 ms (tối thiểu 50); chỉ là backstop, đường chính là wake-up khi enqueue
- skipWhenIdle: true
- clusterSingleton: true
- whatItDoes: Quét `matching:shards:active`, mỗi shard ZPOPMIN 2 ticket rồi tryPair (xác minh lại trong Postgres, tạo `match_sessions` confirmed, publish `match.confirmed`), tối đa MATCHING_MATCHER_BATCH_SIZE (20) cặp/shard/tick.
- touches: Redis ZSET matching:queue:{matchType}:{region}:{ageBand} + SET matching:shards:active; match_tickets; match_sessions; users; notifications; Redis PUBLISH realtime:user:{userId} (match.confirmed); Push provider (external, best-effort)
- concurrencyControl: ZPOPMIN atomic giữa các pod; tryPair khóa 2 ticket FOR UPDATE theo thứ tự id và runExclusive chặn chồng tick trong process. clusterSingleton chỉ áp cho tick interval, còn đường wake-up (debounce 50ms) chạy theo từng pod.
- failureMode: Crash giữa ZPOPMIN và requeue làm ticket mất khỏi Redis dù DB còn `queued` (chờ client retry join hoặc sweeper expire sau 120s). Cặp bị block/report ở đầu shard làm dừng shard đó trong tick.
- evidence (evidence): apps/core-api/src/modules/matching/jobs/matcher-worker.service.ts:46, apps/core-api/src/modules/matching/jobs/matcher-worker.service.ts:102, apps/core-api/src/modules/matching/jobs/matcher-worker.service.ts:109, apps/core-api/src/modules/matching/jobs/matcher-worker.service.ts:111, apps/core-api/src/modules/matching/jobs/matcher-worker.service.ts:200, apps/core-api/src/config/env.validation.ts:465, apps/core-api/src/config/env.validation.ts:466

### TicketSweeperService

- jobName: matching-ticket-sweeper
- module: matching
- intervalExpression: this.config.getOrThrow('MATCHING_SWEEPER_INTERVAL_MS')
- intervalEnvVarAndDefault: MATCHING_SWEEPER_INTERVAL_MS, mặc định 5000 ms (tối thiểu 500)
- skipWhenIdle: true
- clusterSingleton: true
- whatItDoes: Expire tối đa 200 ticket `queued` quá MATCHING_QUEUE_MAX_WAIT_SECONDS (120s, tính từ enqueued_at) và ZREM khỏi Redis; expire session `pending_confirm` cũ quá MATCHING_CONFIRM_TIMEOUT_SECONDS và requeue bên đã confirm bằng ticket mới.
- touches: match_tickets; match_sessions; Redis ZREM/ZADD/SADD hàng đợi matching
- concurrencyControl: pg_try_advisory_xact_lock theo jobName `matching-ticket-sweeper`; UPDATE có điều kiện status=queued; mỗi session một transaction riêng, khóa session rồi tickets.
- failureMode: Redis lỗi sau commit chỉ để lại ticket requeue là zombie DB, chính sweeper này expire nó khi quá hạn. Bỏ tick khi idle nên ticket zombie có thể tồn tại lâu hơn nhưng không có ai chờ.
- evidence (evidence): apps/core-api/src/modules/matching/jobs/ticket-sweeper.service.ts:32, apps/core-api/src/modules/matching/jobs/ticket-sweeper.service.ts:65, apps/core-api/src/modules/matching/jobs/ticket-sweeper.service.ts:71, apps/core-api/src/modules/matching/jobs/ticket-sweeper.service.ts:72, apps/core-api/src/modules/matching/jobs/ticket-sweeper.service.ts:111, apps/core-api/src/modules/matching/jobs/ticket-sweeper.service.ts:133, apps/core-api/src/config/env.validation.ts:467

### PartyRoomSweeperService

- jobName: party-room-sweeper
- module: party-room
- intervalExpression: this.config.getOrThrow('PARTY_SWEEPER_INTERVAL_MS')
- intervalEnvVarAndDefault: PARTY_SWEEPER_INTERVAL_MS, mặc định 30_000 ms (tối thiểu 1000)
- skipWhenIdle: false
- clusterSingleton: true
- whatItDoes: Quét phòng `active` cũ hơn PARTY_STALE_ROOM_SECONDS (120s) và đóng với reason `swept` khi không còn member active hoặc khi LiveKit không còn room (roomExists=false); LiveKit lỗi thì bỏ qua phòng đó.
- touches: party_rooms; party_room_members; LiveKit RoomService roomExists/deleteRoom (external); Redis PUBLISH realtime:user:{userId} (party.room.closed)
- concurrencyControl: pg_try_advisory_xact_lock theo jobName `party-room-sweeper`; closeRoomById idempotent dưới khóa dòng phòng.
- failureMode: Lỗi từng phòng được log, thử lại ở tick sau, không đóng oan khi SFU/DB lỗi tạm thời. Là backstop khi webhook LiveKit bị mất.
- evidence (evidence): apps/core-api/src/modules/party-room/jobs/party-room-sweeper.service.ts:25, apps/core-api/src/modules/party-room/jobs/party-room-sweeper.service.ts:62, apps/core-api/src/modules/party-room/jobs/party-room-sweeper.service.ts:70, apps/core-api/src/modules/party-room/jobs/party-room-sweeper.service.ts:206, apps/core-api/src/config/env.validation.ts:573, apps/core-api/src/config/env.validation.ts:575

### PartyRoomSweeperService

- jobName: party-room-host-grace-check
- module: party-room
- intervalExpression: this.config.getOrThrow('PARTY_HOST_GRACE_CHECK_INTERVAL_MS')
- intervalEnvVarAndDefault: PARTY_HOST_GRACE_CHECK_INTERVAL_MS, mặc định 5000 ms (tối thiểu 1000)
- skipWhenIdle: false
- clusterSingleton: true
- whatItDoes: Đóng phòng có `host_disconnected_at` quá PARTY_HOST_DISCONNECT_GRACE_SECONDS (15s) với reason `host_left`; chốt member rớt mạng quá PARTY_MEMBER_DISCONNECT_GRACE_SECONDS (15s) là đã rời và publish `party.member.left`.
- touches: party_rooms; party_room_members; Redis PUBLISH realtime:user:{userId} (party.room.closed, party.member.left); LiveKit RoomService.deleteRoom (external)
- concurrencyControl: pg_try_advisory_xact_lock theo jobName `party-room-host-grace-check`; guard của closeRoomById re-check hostDisconnectedAt trên dòng đã khóa nên host rejoin đúng lúc không bị đóng nhầm.
- failureMode: Lỗi từng phòng/member được log và thử lại ở tick sau (5s). Nếu job này dừng, phòng có host rớt chỉ còn được dọn bởi party-room-sweeper (30s, và chỉ khi hết member/SFU đóng).
- evidence (evidence): apps/core-api/src/modules/party-room/jobs/party-room-sweeper.service.ts:26, apps/core-api/src/modules/party-room/jobs/party-room-sweeper.service.ts:73, apps/core-api/src/modules/party-room/jobs/party-room-sweeper.service.ts:81, apps/core-api/src/modules/party-room/jobs/party-room-sweeper.service.ts:101, apps/core-api/src/config/env.validation.ts:591, apps/core-api/src/config/env.validation.ts:579

### VideoRankingService

- jobName: short-video-ranking
- module: short-video
- intervalExpression: this.config.getOrThrow('VIDEO_RANKING_JOB_INTERVAL_MS')
- intervalEnvVarAndDefault: VIDEO_RANKING_JOB_INTERVAL_MS, mặc định 1_800_000 ms (30 phút, tối thiểu 60_000)
- skipWhenIdle: true
- clusterSingleton: true
- whatItDoes: Tính lại `videos.rank_score` cho tối đa 1000 video `published` mỗi tick (view/like/comment có trọng số chia hệ số time-decay theo giờ). Giá trị dẫn xuất, `sort=recent` là fallback khi rank_score NULL.
- touches: videos
- concurrencyControl: pg_try_advisory_xact_lock theo jobName `short-video-ranking`; một câu UPDATE ... WHERE id IN (SELECT ... LIMIT 1000).
- failureMode: Lỗi chỉ log; rank_score cũ vẫn được dùng. Khi idle bỏ tick nên điểm xếp hạng không cập nhật cho tới khi có traffic.
- evidence (evidence): apps/core-api/src/modules/short-video/jobs/video-ranking.service.ts:17, apps/core-api/src/modules/short-video/jobs/video-ranking.service.ts:43, apps/core-api/src/modules/short-video/jobs/video-ranking.service.ts:49, apps/core-api/src/modules/short-video/jobs/video-ranking.service.ts:50, apps/core-api/src/modules/short-video/jobs/video-ranking.service.ts:18, apps/core-api/src/config/env.validation.ts:748

### VideoSweeperService

- jobName: short-video-sweeper
- module: short-video
- intervalExpression: this.config.getOrThrow('VIDEO_SWEEPER_INTERVAL_MS')
- intervalEnvVarAndDefault: VIDEO_SWEEPER_INTERVAL_MS, mặc định 3_600_000 ms (1 giờ, tối thiểu 60_000)
- skipWhenIdle: true
- clusterSingleton: true
- whatItDoes: Chuyển video kẹt `uploading` quá VIDEO_UPLOAD_TIMEOUT_SECONDS (3600s) sang `failed` (lô 200), rồi xóa object trên storage của video `failed` và đóng dấu `storage_cleaned_at`; xóa lỗi thì đẩy `updated_at` về cuối hàng đợi.
- touches: videos; Object storage qua VideoStoragePort.delete (external)
- concurrencyControl: pg_try_advisory_xact_lock theo jobName `short-video-sweeper`; UPDATE có điều kiện status=uploading, không khóa dòng.
- failureMode: Xóa object lỗi chỉ warn và thử lại ở tick sau (deferRetry xoay hàng đợi). Idle bỏ tick nên object rác tồn tại lâu hơn.
- evidence (evidence): apps/core-api/src/modules/short-video/jobs/video-sweeper.service.ts:18, apps/core-api/src/modules/short-video/jobs/video-sweeper.service.ts:47, apps/core-api/src/modules/short-video/jobs/video-sweeper.service.ts:53, apps/core-api/src/modules/short-video/jobs/video-sweeper.service.ts:54, apps/core-api/src/modules/short-video/jobs/video-sweeper.service.ts:101, apps/core-api/src/config/env.validation.ts:737

### SignalingGateway

- jobName: (không có tên job — setInterval theo từng socket, không qua ManagedInterval)
- module: signaling-gateway
- intervalExpression: Math.floor(this.connectionQuota.leaseMs / 3)
- intervalEnvVarAndDefault: WS_CONNECTION_LEASE_MS / 3; WS_CONNECTION_LEASE_MS mặc định 90_000 ms (10_000–300_000) nên chu kỳ mặc định 30_000 ms
- skipWhenIdle: false
- clusterSingleton: false
- whatItDoes: Mỗi socket đã admit có một timer gia hạn lease quota `signaling:connection-quota:{userId}` (và `realtime:presence:{userId}`) bằng Lua REFRESH_SCRIPT; hết lease hoặc lỗi thì ngắt socket (fail closed).
- touches: Redis ZSET signaling:connection-quota:{userId}; Redis ZSET realtime:presence:{userId}
- concurrencyControl: Lua script atomic trên Redis (ACQUIRE/REFRESH/RELEASE); timer.unref() và bị clearInterval khi socket disconnect.
- failureMode: refresh trả 0 hoặc ném lỗi thì client.disconnect(true) và gửi Sentry; pod chết thì lease tự hết hạn theo TTL nên slot không bị giữ vĩnh viễn.
- evidence (evidence): apps/signaling-gateway/src/app/signaling.gateway.ts:287, apps/signaling-gateway/src/app/signaling.gateway.ts:300, apps/signaling-gateway/src/app/signaling.gateway.ts:292, apps/signaling-gateway/src/app/signaling.gateway.ts:302, apps/signaling-gateway/src/config/env.validation.ts:44
