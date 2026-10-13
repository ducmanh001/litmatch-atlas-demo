# Module matching

id: module:matching
kind: core-api module
code: apps/core-api/src/modules/matching

Sở hữu vòng đời MatchTicket (queued→confirmed|expired|cancelled), hàng đợi Redis sharded + matcher worker, MatchInvite (mời có chủ đích) và MatchSession là bản ghi duy nhất của một cặp đã ghép; là nơi DUY NHẤT ghi match_sessions. KHÔNG sở hữu ví/ledger (Economy), block/report (Safety), hồ sơ (User), chat Soul (Soul Match) hay call LiveKit (Calling).

## Facts

- tables owned: guest_match_quotas, match_daily_quotas, match_invites, match_sessions (ALSO ACCESSED BY soul-match), match_tickets
- touches tables of other modules: users (owner user; read 3, write 0)
- API: 12 endpoints, 0 public, 0 admin-only, 2 require Idempotency-Key, 2 with own throttle
- publishes realtime: match.confirmed
- jobs: InviteSweeperService, MatcherWorkerService, TicketSweeperService
- env vars read: MATCHING_INVITE_SWEEPER_INTERVAL_MS, MATCHING_MATCHER_INTERVAL_MS, MATCHING_MATCHER_BATCH_SIZE, MATCHING_SWEEPER_INTERVAL_MS, MATCHING_QUEUE_MAX_WAIT_SECONDS, MATCHING_CONFIRM_TIMEOUT_SECONDS, MATCHING_SPEEDUP_PRICE_DIAMOND, MATCHING_SVIP_SPEEDUP_PRICE_DIAMOND, MATCHING_VIP_SPEEDUP_PRICE_DIAMOND, MATCHING_EXTRA_SOUL_PRICE_DIAMOND, MATCHING_EXTRA_VOICE_PRICE_DIAMOND, MATCHING_PRIORITY_BOOST_MS, MATCHING_AGE_BAND_SIZE, MATCHING_TRUST_PENALTY_MS_PER_POINT, MATCHING_TRUST_PENALTY_MAX_MS, REDIS_URL, MATCHING_GUEST_DAILY_LIMIT, MATCHING_SVIP_DAILY_SOUL_LIMIT, MATCHING_SVIP_DAILY_VOICE_LIMIT, MATCHING_VIP_DAILY_SOUL_LIMIT, MATCHING_VIP_DAILY_VOICE_LIMIT, MATCHING_DAILY_SOUL_LIMIT, MATCHING_DAILY_VOICE_LIMIT, MATCHING_GUEST_QUOTA_PEPPER, MATCHING_INVITE_RATE_LIMIT_PER_HOUR, MATCHING_INVITE_TTL_SECONDS
- error codes: 20 in MatchingErrors
- tests: 6 unit spec files, 1 integration spec files
- history: 24 commits (1 fixes), last 2026-08-13
- docs: docs/services/matching-service.md (last changed 2026-08-13)
- features: matching

## Where to read next

- tables: ../db/matching.md
- API contract: ../api/matching.md
- why it talks to other services: ../service-map/matching.md and ../service-map/matching-techniques.md
- error codes: ../errors/matchingerrors.md
- flows involving it: [calling-end-and-ticker](../flows/calling-end-and-ticker.md), [calling-like-after-call](../flows/calling-like-after-call.md), [matching-invite-accept](../flows/matching-invite-accept.md), [matching-invite-create](../flows/matching-invite-create.md), [matching-invite-decline-cancel](../flows/matching-invite-decline-cancel.md), [matching-sweeper-jobs](../flows/matching-sweeper-jobs.md), [matching-ticket-status-cancel-speedup](../flows/matching-ticket-status-cancel-speedup.md), [matching-to-call](../flows/matching-to-call.md), [soul-match-room-reads-and-reveal](../flows/soul-match-room-reads-and-reveal.md), [soul-match-send-message](../flows/soul-match-send-message.md), [soul-match-session](../flows/soul-match-session.md)
