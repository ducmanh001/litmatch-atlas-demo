# Cache and state: Redis, locks, idempotency, client state: locksAndLeases

id: system:state:locksAndLeases

Written by an AI agent from the code; every file:line quote was checked automatically (exists within ±3 lines) but NO independent verification pass has run yet. Treat claims as strong leads; open the cited file before editing.

### pg advisory lock

- where: apps/core-api/src/common/scheduling/managed-interval.ts (runClusterSingleton)
- protects: Job nền bật clusterSingleton chỉ chạy ở một pod mỗi tick: pg_try_advisory_xact_lock(hashtextextended('litmatch:cluster-job:{jobName}')) trong transaction; lock nhả khi transaction/connection kết thúc kể cả crash. Dùng bởi call-ticker, reconciliation (fast/deep), story-sweeper, streak-warning, invite-sweeper, matcher-worker, ticket-sweeper, party-room-sweeper, video-ranking, video-sweeper.
- evidence: apps/core-api/src/common/scheduling/managed-interval.ts:128
- moreEvidence (evidence): apps/core-api/src/common/scheduling/managed-interval.ts:131, apps/core-api/src/modules/matching/jobs/matcher-worker.service.ts:111

### pg advisory lock

- where: apps/core-api/src/modules/movie-match/movie-match.service.ts (lockAnonMatcher)
- protects: Tuần tự hoá bộ ghép Movie Match ẩn danh giữa các request/pod bằng pg_advisory_xact_lock(hashtext(MOVIE_MATCH_ADVISORY_LOCK_KEY)) trong transaction.
- evidence: apps/core-api/src/modules/movie-match/movie-match.service.ts:680

### pg advisory lock

- where: apps/core-api/src/modules/palm-match/palm-match.service.ts (lockMatcher)
- protects: Tuần tự hoá bộ ghép Palm Match giữa các request/pod bằng pg_advisory_xact_lock(hashtext(PALM_MATCH_ADVISORY_LOCK_KEY)).
- evidence: apps/core-api/src/modules/palm-match/palm-match.service.ts:329

### row lock FOR UPDATE

- where: apps/core-api/src/modules/economy/services/ledger.service.ts (wallets; cũng economy.service.ts)
- protects: Ví user (wallets): điểm tuần tự hoá per-user khi ghi sổ và chống tiêu quá số dư (guard tầng ứng dụng, không phải CHECK); khoá theo thứ tự userId.
- evidence: apps/core-api/src/modules/economy/services/ledger.service.ts:412
- moreEvidence (evidence): apps/core-api/src/modules/economy/services/ledger.service.ts:420, docs/adr/0012-postgres-persistence-boundaries.md:37

### row lock FOR UPDATE

- where: apps/core-api/src/modules/matching/jobs/matcher-worker.service.ts (và ticket-sweeper, matching.service)
- protects: match_tickets/match_sessions: verify lại cả 2 ticket còn queued trước khi ghép và chuyển trạng thái; sweeper và matcher tranh nhau cùng dòng nên lock buộc tuần tự.
- evidence: apps/core-api/src/modules/matching/jobs/matcher-worker.service.ts:226
- moreEvidence (evidence): apps/core-api/src/modules/matching/jobs/matcher-worker.service.ts:237, apps/core-api/src/modules/matching/jobs/ticket-sweeper.service.ts:94

### row lock FOR UPDATE

- where: apps/core-api/src/modules/auth/repositories/typeorm-refresh-session.adapter.ts (rotate)
- protects: refresh_tokens: hai request cùng refresh token song song chỉ một thắng rotation, bên thua rơi vào nhánh reuse và revoke cả family.
- evidence: apps/core-api/src/modules/auth/repositories/typeorm-refresh-session.adapter.ts:42

### row lock FOR UPDATE

- where: apps/core-api/src/modules/auth/auth.service.ts và matching.service.ts (bảng users)
- protects: Khoá user khi nâng cấp guest và khi vào queue để đọc isGuest/status mới từ DB, không tin claim JWT cũ.
- evidence: apps/core-api/src/modules/auth/auth.service.ts:227
- moreEvidence (evidence): apps/core-api/src/modules/matching/matching.service.ts:102

### row lock FOR UPDATE

- where: apps/core-api/src/modules/party-room/party-room.service.ts
- protects: party_rooms: mọi thay đổi state phòng (join/leave/đổi role/close) serialize qua khoá dòng phòng và verify còn active.
- evidence: apps/core-api/src/modules/party-room/party-room.service.ts:108

### row lock FOR UPDATE

- where: apps/core-api/src/modules/calling/calling.service.ts
- protects: call_sessions: kết thúc call idempotent dù đến từ endpoint, webhook LiveKit hay ticker nhờ khoá dòng call.
- evidence: apps/core-api/src/modules/calling/calling.service.ts:438

### row lock FOR UPDATE

- where: apps/core-api/src/modules/matching/services/invite.service.ts
- protects: match_invites: precheckAccept khoá dòng invite nên request accept thứ hai luôn thấy trạng thái đã đổi.
- evidence: apps/core-api/src/modules/matching/services/invite.service.ts:500

### row lock FOR UPDATE

- where: apps/core-api/src/modules/matching/services/guest-match-quota.service.ts
- protects: guest_match_quotas và match_daily_quotas: đếm và tiêu quota ghép đôi mỗi ngày/thiết bị atomic.
- evidence: apps/core-api/src/modules/matching/services/guest-match-quota.service.ts:100
- moreEvidence (evidence): apps/core-api/src/modules/matching/services/guest-match-quota.service.ts:180

### row lock FOR UPDATE

- where: apps/core-api/src/modules/friend/services/streak.service.ts
- protects: conversation_streaks: recordActivity khoá dòng streak để tính streak on-write không race giữa các tin nhắn.
- evidence: apps/core-api/src/modules/friend/services/streak.service.ts:68

### row lock FOR UPDATE

- where: apps/core-api/src/modules/economy/services/payos.service.ts
- protects: payos_payment_orders: webhook hợp lệ đến trễ sau khi read-path suy ra expired vẫn xử lý trên dòng đã khoá.
- evidence: apps/core-api/src/modules/economy/services/payos.service.ts:316
- moreEvidence (evidence): apps/core-api/src/modules/economy/services/payos.service.ts:210

### row lock FOR UPDATE

- where: apps/core-api/src/modules/soul-match/soul-match.service.ts
- protects: Phiên Soul Match: khoá dòng để hai rater like song song không bỏ lỡ nhau (READ COMMITTED).
- evidence: apps/core-api/src/modules/soul-match/soul-match.service.ts:287

### row lock FOR UPDATE

- where: movie-match.service.ts, palm-match.service.ts, safety.service.ts, support.service.ts, short-video.service.ts, user.service.ts
- protects: movie_sessions, palm_match_sessions, users, support_tickets, video_views: khoá dòng trước khi đổi trạng thái (số lượng theo db-usage: 3, 3, 5, 1, 1).
- evidence: apps/core-api/src/modules/movie-match/movie-match.service.ts:400
- moreEvidence (evidence): apps/core-api/src/modules/palm-match/palm-match.service.ts:315, apps/core-api/src/modules/support/support.service.ts:83

### SKIP LOCKED

- where: apps/core-api/src/modules/economy/jobs/outbox-relay.service.ts (flushOnce)
- protects: outbox_events: nhiều pod relay claim lô chưa publish mà không lấy trùng dòng (FOR UPDATE SKIP LOCKED); job này không dùng clusterSingleton.
- evidence: apps/core-api/src/modules/economy/jobs/outbox-relay.service.ts:72
- moreEvidence (evidence): apps/core-api/src/modules/economy/jobs/outbox-relay.service.ts:25

### SKIP LOCKED

- where: apps/core-api/src/modules/economy/jobs/iap-refund-poll.service.ts
- protects: iap_receipts: nhiều pod claim lô receipt để poll refund mà không dẫm nhau.
- evidence: apps/core-api/src/modules/economy/jobs/iap-refund-poll.service.ts:97

### redis lease

- where: apps/signaling-gateway/src/app/connection-quota.service.ts (ACQUIRE/REFRESH/RELEASE Lua)
- protects: Tối đa WS_MAX_CONNECTIONS_PER_USER (<=3) socket mỗi user trên toàn cụm; lease TTL WS_CONNECTION_LEASE_MS, pod chết thì tự hết hạn; đồng thời nuôi realtime:presence:{userId}.
- evidence: apps/signaling-gateway/src/app/connection-quota.service.ts:29

### ZPOPMIN atomic pop

- where: apps/core-api/src/modules/matching/jobs/matcher-worker.service.ts (popMin) + redis-matching-queue.adapter.ts
- protects: Hai matcher/pod không lấy trùng ticket nhờ ZPOPMIN shard 2 atomic của Redis; sau đó Postgres verify lại bằng SELECT FOR UPDATE.
- evidence: apps/core-api/src/modules/matching/jobs/matcher-worker.service.ts:199
- moreEvidence (evidence): apps/core-api/src/modules/matching/jobs/matcher-worker.service.ts:63

### unique constraint guard

- where: uq_match_tickets_active_user (match_tickets)
- protects: Partial unique: mỗi user chỉ có 1 ticket ở trạng thái queued hoặc matched.
- evidence: apps/core-api/src/database/migrations/1752200000000-matching-core.ts:35

### unique constraint guard

- where: uq_match_invites_pending_pair (match_invites)
- protects: Partial unique: tối đa 1 invite pending mỗi cặp (inviter, invitee).
- evidence: apps/core-api/src/database/migrations/1754700000000-match-invite.ts:34

### unique constraint guard

- where: uq_call_sessions_active_friend_pair (call_sessions)
- protects: Partial unique: một cặp bạn bè chỉ có 1 cuộc gọi friend ở trạng thái pending/active.
- evidence: apps/core-api/src/database/migrations/1757200000000-friend-calling.ts:13

### unique constraint guard

- where: uq_party_members_active_user và uq_party_members_active_room_user (party_room_members)
- protects: Partial unique (left_at IS NULL): mỗi user ở tối đa 1 phòng active và 1 membership active mỗi phòng.
- evidence: apps/core-api/src/database/migrations/1752700000000-party-room-gift.ts:53
- moreEvidence (evidence): apps/core-api/src/database/migrations/1752700000000-party-room-gift.ts:47

### unique constraint guard

- where: uq_ledger_accounts_user và uq_ledger_accounts_system (ledger_accounts)
- protects: Partial unique: mỗi (kind, user, currency) và mỗi (kind, currency) hệ thống chỉ có một tài khoản sổ cái; tạo lazy bằng ON CONFLICT DO NOTHING.
- evidence: apps/core-api/src/database/migrations/1752000000000-economy-ledger.ts:26
- moreEvidence (evidence): apps/core-api/src/database/migrations/1752000000000-economy-ledger.ts:29

### unique constraint guard

- where: uq_reports_video_reporter (reports)
- protects: Partial unique: mỗi người chỉ báo cáo một video một lần.
- evidence: apps/core-api/src/database/migrations/1754900000000-report-target-video.ts:31
