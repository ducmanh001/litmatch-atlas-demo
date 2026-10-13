# Background jobs, outbox, Redis call sites

id: events:jobs

## Jobs

| class | module | kind | interval | idle skip | cluster singleton | file |
|---|---|---|---|---|---|---|
| CallTickerService | calling | ManagedInterval | this.config.getOrThrow('CALLING_TICKER_INTERVAL_MS', { infer: true, }) |  | yes | apps/core-api/src/modules/calling/jobs/call-ticker.service.ts:49 |
| IapRefundPollService | economy | ManagedInterval | this.config.getOrThrow('ECONOMY_REFUND_POLL_INTERVAL_MS', { infer: true, }) |  |  | apps/core-api/src/modules/economy/jobs/iap-refund-poll.service.ts:66 |
| OutboxRelayService | economy | ManagedInterval | this.config.getOrThrow('ECONOMY_OUTBOX_RELAY_INTERVAL_MS', { infer: true, }) |  |  | apps/core-api/src/modules/economy/jobs/outbox-relay.service.ts:48 |
| ReconciliationService | economy | ManagedInterval | this.config.getOrThrow( 'ECONOMY_RECONCILIATION_FAST_INTERVAL_MS', { infer: true }, ) |  | yes | apps/core-api/src/modules/economy/jobs/reconciliation.service.ts:85 |
| ReconciliationService | economy | ManagedInterval | this.config.getOrThrow('ECONOMY_RECONCILIATION_INTERVAL_MS', { infer: true, }) |  | yes | apps/core-api/src/modules/economy/jobs/reconciliation.service.ts:96 |
| StorySweeperService | feed | ManagedInterval | this.config.getOrThrow('STORY_SWEEPER_INTERVAL_MS', { infer: true, }) | true | yes | apps/core-api/src/modules/feed/jobs/story-sweeper.service.ts:40 |
| StreakWarningJob | friend | ManagedInterval | this.config.getOrThrow('STREAK_WARNING_CHECK_INTERVAL_MS', { infer: true, }) |  | yes | apps/core-api/src/modules/friend/jobs/streak-warning.job.ts:54 |
| InviteSweeperService | matching | ManagedInterval | this.config.getOrThrow( 'MATCHING_INVITE_SWEEPER_INTERVAL_MS', { infer: true, }, ) | true | yes | apps/core-api/src/modules/matching/jobs/invite-sweeper.service.ts:40 |
| MatcherWorkerService | matching | ManagedInterval | this.config.getOrThrow('MATCHING_MATCHER_INTERVAL_MS', { infer: true, }) | true | yes | apps/core-api/src/modules/matching/jobs/matcher-worker.service.ts:100 |
| TicketSweeperService | matching | ManagedInterval | this.config.getOrThrow('MATCHING_SWEEPER_INTERVAL_MS', { infer: true, }) | true | yes | apps/core-api/src/modules/matching/jobs/ticket-sweeper.service.ts:63 |
| PartyRoomSweeperService | party-room | ManagedInterval | this.config.getOrThrow('PARTY_SWEEPER_INTERVAL_MS', { infer: true, }) |  | yes | apps/core-api/src/modules/party-room/jobs/party-room-sweeper.service.ts:62 |
| PartyRoomSweeperService | party-room | ManagedInterval | this.config.getOrThrow('PARTY_HOST_GRACE_CHECK_INTERVAL_MS', { infer: true, }) |  | yes | apps/core-api/src/modules/party-room/jobs/party-room-sweeper.service.ts:73 |
| VideoRankingService | short-video | ManagedInterval | this.config.getOrThrow('VIDEO_RANKING_JOB_INTERVAL_MS', { infer: true, }) | true | yes | apps/core-api/src/modules/short-video/jobs/video-ranking.service.ts:41 |
| VideoSweeperService | short-video | ManagedInterval | this.config.getOrThrow('VIDEO_SWEEPER_INTERVAL_MS', { infer: true, }) | true | yes | apps/core-api/src/modules/short-video/jobs/video-sweeper.service.ts:45 |
| SignalingGateway | signaling-gateway | setInterval | Math.floor(this.connectionQuota.leaseMs / 3) |  |  | apps/signaling-gateway/src/app/signaling.gateway.ts:287 |

## Outbox writers

- economy apps/core-api/src/modules/economy/economy.service.ts:429: topic=ECONOMY_EVENTS_TOPIC, eventType='economy.vip.purchased', payload={ version: 1, userId, planId, tier: plan.tier, vipExpiresAt…
- economy apps/core-api/src/modules/economy/economy.service.ts:648: topic=ECONOMY_EVENTS_TOPIC, eventType='economy.gift.sent', payload={ version: 1, transactionId: txn.id, senderUserId, receiver…
- economy apps/core-api/src/modules/economy/services/ledger.service.ts:478: topic=ECONOMY_EVENTS_TOPIC, eventType=outboxEventTypeOverride ?? (balanceDelta > 0n ? 'economy.di…, payload={ version: 1, transactionId: txn.id, transactionType: txn.t…

Topics: ECONOMY_EVENTS_TOPIC=litmatch.economy.events. No Kafka consumer exists in this repository.

## Redis call sites

- core-api(common) publish(realtimeUserChannel(userId)) apps/core-api/src/common/realtime/publish-realtime.ts:24
- core-api(common) publish(channel) apps/core-api/src/common/realtime/redis-realtime-publisher.adapter.ts:11
- core-api(common) get(redisUrl) apps/core-api/src/common/redis/core-redis-client.ts:42
- core-api(common) set(redisUrl) apps/core-api/src/common/redis/core-redis-client.ts:58
- core-api(common) set(client) apps/core-api/src/common/redis/core-redis-client.ts:59
- core-api(common) get(client) apps/core-api/src/common/redis/core-redis-client.ts:69
- core-api(common) quit() apps/core-api/src/common/redis/core-redis-client.ts:81
- core-api(common) eval(RATE_LIMIT_CONSUME_LUA) apps/core-api/src/common/redis/redis-rate-limit.adapter.ts:79
- core-api(common) eval(RATE_LIMIT_REFUND_LUA) apps/core-api/src/common/redis/redis-rate-limit.adapter.ts:103
- core-api(common) eval(THROTTLE_INCREMENT_LUA) apps/core-api/src/common/throttle/redis-throttler-storage.ts:91
- matching zadd(shard) apps/core-api/src/modules/matching/redis/redis-matching-queue.adapter.ts:18
- matching zrem(shard) apps/core-api/src/modules/matching/redis/redis-matching-queue.adapter.ts:22
- matching zpopmin(shard) apps/core-api/src/modules/matching/redis/redis-matching-queue.adapter.ts:26
- matching smembers(this.activeShardsKey) apps/core-api/src/modules/matching/redis/redis-matching-queue.adapter.ts:35
- matching sadd(this.activeShardsKey) apps/core-api/src/modules/matching/redis/redis-matching-queue.adapter.ts:39
- matching srem(this.activeShardsKey) apps/core-api/src/modules/matching/redis/redis-matching-queue.adapter.ts:43
- matching zcard(shard) apps/core-api/src/modules/matching/redis/redis-matching-queue.adapter.ts:47
- user zremrangebyscore(key) apps/core-api/src/modules/user/redis/redis-presence-reader.adapter.ts:15
- user zcard(key) apps/core-api/src/modules/user/redis/redis-presence-reader.adapter.ts:16
- signaling-gateway eval(ACQUIRE_SCRIPT) apps/signaling-gateway/src/app/connection-quota.service.ts:126
- signaling-gateway eval(REFRESH_SCRIPT) apps/signaling-gateway/src/app/connection-quota.service.ts:139
- signaling-gateway eval(RELEASE_SCRIPT) apps/signaling-gateway/src/app/connection-quota.service.ts:151
- signaling-gateway quit() apps/signaling-gateway/src/app/connection-quota.service.ts:162
- signaling-gateway duplicate() apps/signaling-gateway/src/app/redis-adapter.service.ts:26
- signaling-gateway quit() apps/signaling-gateway/src/app/redis-adapter.service.ts:75
- signaling-gateway quit() apps/signaling-gateway/src/app/signaling.gateway.ts:140
- signaling-gateway psubscribe(REALTIME_USER_CHANNEL_PATTERN) apps/signaling-gateway/src/app/signaling.gateway.ts:345
