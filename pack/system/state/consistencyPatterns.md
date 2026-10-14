# Cache and state: Redis, locks, idempotency, client state: consistencyPatterns

id: system:state:consistencyPatterns

Written by an AI agent from the code; every file:line quote was checked automatically (exists within ±3 lines) but NO independent verification pass has run yet. Treat claims as strong leads; open the cited file before editing.

### Outbox Economy sang Kafka

- where: ledger (ghi outbox_events) và OutboxRelayService
- rule: Sự kiện tiền ghi cùng transaction với bút toán; relay claim lô chưa publish bằng FOR UPDATE SKIP LOCKED, publish Kafka rồi đánh dấu published_at; lỗi lặp thì giữ dòng terminal để vận hành replay.
- why: Không mất sự kiện tiền khi broker lỗi và nhiều pod relay không dẫm nhau.
- evidence: apps/core-api/src/modules/economy/jobs/outbox-relay.service.ts:25
- moreEvidence (evidence): docs/adr/0012-postgres-persistence-boundaries.md:11, apps/core-api/src/modules/economy/jobs/outbox-relay.service.ts:133, apps/core-api/src/modules/economy/services/ledger.service.ts:475

### Publish realtime sau commit, không dùng outbox

- where: publishRealtimeEvent và libs/common-dtos realtime-events
- rule: Publish Redis pub/sub chạy sau khi DB transaction xong, nuốt lỗi và chỉ log warn; client còn REST polling làm fallback.
- why: Event ephemeral at-most-once, publish lỗi không được phá nghiệp vụ đã commit.
- evidence: apps/core-api/src/common/realtime/publish-realtime.ts:14
- moreEvidence (evidence): libs/common-dtos/src/lib/realtime-events.ts:10

### Wallet snapshot dựng lại từ ledger

- where: wallets (snapshot), ledger_entries (sự thật), ReconciliationService
- rule: wallets là snapshot dẫn xuất, chỉ LedgerService ghi trong cùng transaction với bút toán, rebuild được bằng LedgerService.rebuildWallet(); job đối soát deep lấy mẫu ví và so snapshot với số derive từ ledger.
- why: Một nguồn sự thật cho tiền; lệch snapshot phát hiện được và sửa được.
- evidence: apps/core-api/src/modules/economy/entities/wallet.entity.ts:9
- moreEvidence (evidence): apps/core-api/src/modules/economy/entities/wallet.entity.ts:11, apps/core-api/src/modules/economy/jobs/reconciliation.service.ts:180, apps/core-api/src/modules/economy/services/ledger.service.ts:277

### Append-only bằng trigger DB

- where: ledger_entries và admin_audit_logs
- rule: Trigger BEFORE UPDATE OR DELETE gọi forbid_ledger_entry_mutation / forbid_admin_audit_log_mutation nên dòng chỉ được thêm, không sửa/xoá.
- why: Sổ cái và audit log bất biến ngay cả khi mã ứng dụng có lỗi.
- evidence: apps/core-api/src/database/migrations/1752000000000-economy-ledger.ts:77
- moreEvidence (evidence): apps/core-api/src/database/migrations/1753700000000-admin-audit-log.ts:6

### Chuyển trạng thái bằng UPDATE có điều kiện

- where: OtpService (attempt_count, consumed_at), TypeOrmRefreshSessionAdapter.rotate (rotated_at), ticket-sweeper
- rule: Đếm lần thử và consume OTP, đánh dấu rotated của refresh token đều là UPDATE kèm điều kiện (attempt_count < max, consumed_at IS NULL, rotatedAt IS NULL); affected=0 nghĩa là thua race.
- why: Check-then-act phải atomic: client song song không vượt giới hạn và một mã/token chỉ dùng một lần.
- evidence: apps/core-api/src/modules/auth/services/otp.service.ts:93
- moreEvidence (evidence): apps/core-api/src/modules/auth/repositories/typeorm-refresh-session.adapter.ts:58, apps/core-api/src/modules/matching/jobs/ticket-sweeper.service.ts:94

### Verify lại block/guest/privacy tại thời điểm hành động, không cache

- where: matcher-worker (tryPair), invite accept, POST /matching/tickets, publish realtime
- rule: Block/report được kiểm lại đúng lúc ghép/accept (không chỉ lúc vào queue); isGuest đọc lại từ DB trong transaction thay vì tin JWT; quyền xem realtime được core-api quyết tại lúc publish rồi gateway relay nguyên văn.
- why: Trạng thái block/guest có thể đổi trong lúc ticket chờ hoặc sau khi nâng cấp; cache hay claim cũ sẽ sai.
- evidence: docs/services/matching-service.md:111
- moreEvidence (evidence): apps/core-api/src/modules/matching/matching.service.ts:102, libs/common-dtos/src/lib/realtime-events.ts:7

### Redis là bản dẫn xuất, Postgres là nguồn sự thật

- where: matching queue, presence, quota kết nối
- rule: Queue shard chỉ là bản dẫn xuất (matcher verify lại Postgres bằng SELECT FOR UPDATE); presence là state dẫn xuất tự hết hạn theo lease.
- why: Mất hoặc lệch Redis không làm sai dữ liệu nghiệp vụ.
- evidence: apps/core-api/src/modules/matching/jobs/matcher-worker.service.ts:63
- moreEvidence (evidence): libs/common-dtos/src/lib/realtime-presence.ts:3

### Job nền chạy mọi pod nhưng một pod thắng advisory lock

- where: ManagedInterval.runClusterSingleton
- rule: Mỗi tick pod thử pg_try_advisory_xact_lock; pod thua nghỉ followerRetryMs. Domain vẫn phải đúng khi chạy trùng (transaction, lock, idempotency).
- why: Backstop timer không được nhân N lần khi scale ngang và không cần leader election riêng.
- evidence: apps/core-api/src/common/scheduling/managed-interval.ts:23
- moreEvidence (evidence): apps/core-api/src/common/scheduling/managed-interval.ts:128

### Replay bằng unique violation hoặc ON CONFLICT DO NOTHING

- where: LedgerService, ConversationService, FeedService, AuthService, resolveAccount
- rule: Insert trước, bắt unique violation (23505) rồi đọc bản thắng và so khớp payload; trong transaction do caller sở hữu dùng ON CONFLICT DO NOTHING để không abort transaction.
- why: Lỗi 23505 làm abort transaction Postgres nên không dùng insert-rồi-bắt trong transaction ledger.
- evidence: apps/core-api/src/modules/economy/services/ledger.service.ts:117
- moreEvidence (evidence): apps/core-api/src/modules/economy/services/ledger.service.ts:390

### Lease có TTL thay cho cờ trạng thái

- where: signaling connection quota và realtime:presence
- rule: Mỗi socket là một member lease có hạn; refresh mỗi leaseMs/3, release khi disconnect, pod chết thì tự hết hạn; presence có member theo lease nên không race xoá trạng thái khi user còn socket khác.
- why: Pod hoặc Redis chết không giữ slot hay trạng thái online vĩnh viễn.
- evidence: libs/common-dtos/src/lib/realtime-presence.ts:3
- moreEvidence (evidence): apps/signaling-gateway/src/app/signaling.gateway.ts:192

### Access token stateless, trạng thái tài khoản kiểm lại ở refresh/login

- where: JwtAuthGuard và TokenService.rotate
- rule: Guard global chỉ verify chữ ký và hạn của JWT, không query DB mỗi request; banned được kiểm lại khi refresh/login nên cửa sổ rủi ro bằng TTL access token.
- why: Tránh một truy vấn DB mỗi request đổi lấy cửa sổ thu hồi ngắn.
- evidence: apps/core-api/src/common/guards/jwt-auth.guard.ts:22
- moreEvidence (evidence): apps/core-api/src/modules/auth/services/token.service.ts:81
