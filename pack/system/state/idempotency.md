# Cache and state: Redis, locks, idempotency, client state: idempotency

id: system:state:idempotency

Written by an AI agent from the code; every file:line quote was checked automatically (exists within ±3 lines) but NO independent verification pass has run yet. Treat claims as strong leads; open the cited file before editing.

### #1

- scope: Mọi bút toán ví qua LedgerService.record (spend, credit, refund, VIP, gift, avatar)
- headerOrKey: idempotencyKey do flow dựng; client gửi header Idempotency-Key (bắt buộc, tối đa 128 ký tự) rồi flow thêm prefix
- storage: transactions.idempotency_key (kèm request_hash)
- uniqueConstraint: uq_transactions_idempotency_key
- replayBehavior: Tra key trước; cùng request_hash thì trả giao dịch cũ với replayed=true, không ghi sổ lần hai. Race: insert dùng ON CONFLICT DO NOTHING hoặc bắt unique violation rồi đọc bản thắng.
- conflictBehavior: request_hash khác thì 409 ECONOMY_TRANSACTION_IDEMPOTENCY_CONFLICT.
- evidence: apps/core-api/src/modules/economy/services/ledger.service.ts:367
- moreEvidence (evidence): apps/core-api/src/modules/economy/services/ledger.service.ts:61, apps/core-api/src/modules/economy/services/ledger.service.ts:133, apps/core-api/src/modules/economy/services/ledger.service.ts:371, apps/core-api/src/common/decorators/idempotency-key.decorator.ts:12, apps/core-api/src/modules/economy/economy.errors.ts:5, apps/core-api/src/database/migrations/1752000000000-economy-ledger.ts:43

### #2

- scope: Xác minh biên lai IAP Apple/Google (EconomyService)
- headerOrKey: iap:{provider}:{providerTransactionId} (lấy từ receipt đã verify, không dùng header)
- storage: transactions.idempotency_key và iap_receipts (provider, provider_transaction_id)
- uniqueConstraint: uq_iap_receipts_provider_txn; uq_transactions_idempotency_key
- replayBehavior: Cùng receipt gọi lại thì kết quả ledger.record trả replayed=true cho client, không cộng diamond lần hai.
- conflictBehavior: Nếu request_hash lệch giao dịch đã có thì ledger ném 409 như mục ledger.
- evidence: apps/core-api/src/modules/economy/economy.service.ts:322
- moreEvidence (evidence): apps/core-api/src/database/migrations/1752000000000-economy-ledger.ts:127

### #3

- scope: Mua VIP (EconomyService)
- headerOrKey: vip:{userId}:{Idempotency-Key}
- storage: transactions.idempotency_key
- uniqueConstraint: uq_transactions_idempotency_key
- replayBehavior: Replay không gia hạn lần hai, trả trạng thái VIP hiện tại với replayed=true.
- conflictBehavior: Cùng key khác nội dung thì 409 từ ledger.
- evidence: apps/core-api/src/modules/economy/economy.service.ts:445
- moreEvidence (evidence): apps/core-api/src/modules/economy/economy.service.ts:389

### #4

- scope: payOS tạo đơn nạp (PayosService.createOrder)
- headerOrKey: payos:{userId}:{Idempotency-Key của client}
- storage: payos_payment_orders.idempotency_key (kèm request_hash = sha256 của packageId)
- uniqueConstraint: uq_payos_payment_orders_idempotency_key
- replayBehavior: Tìm đơn theo key; cùng request_hash thì trả đơn cũ (replayed) và link thanh toán hiện hữu. Race: bắt unique violation rồi đọc bản thắng.
- conflictBehavior: assertSameCreateRequest so request_hash; mã lỗi định nghĩa sẵn là ECONOMY_PAYOS_ORDER_IDEMPOTENCY_CONFLICT.
- evidence: apps/core-api/src/modules/economy/services/payos.service.ts:85
- moreEvidence (evidence): apps/core-api/src/modules/economy/services/payos.service.ts:86, apps/core-api/src/modules/economy/services/payos.service.ts:123, apps/core-api/src/modules/economy/economy.errors.ts:16, apps/core-api/src/database/migrations/1756300000000-payos-diamond.ts:39

### #5

- scope: Webhook payOS ghi có theo đơn (mã đơn và dedupe webhook)
- headerOrKey: orderCode của đơn; ledger key payos-credit:{orderCode}
- storage: payos_payment_orders.order_code và .transaction_id; transactions.idempotency_key
- uniqueConstraint: uq_payos_payment_orders_order_code; uq_payos_payment_orders_transaction_id; uq_transactions_idempotency_key
- replayBehavior: Webhook gửi lặp thì ledger trả replay theo key payos-credit:{orderCode}, không cộng lần hai; đơn được khoá FOR UPDATE khi xử lý.
- conflictBehavior: Chỉ webhook đã HMAC-verify mới vào write path; event.orderCode phải khớp đơn trước khi xử lý.
- evidence: apps/core-api/src/modules/economy/services/payos.service.ts:178
- moreEvidence (evidence): apps/core-api/src/modules/economy/services/payos.service.ts:169, apps/core-api/src/modules/economy/services/payos.service.ts:329, apps/core-api/src/database/migrations/1756300000000-payos-diamond.ts:38

### #6

- scope: Webhook Apple/Google và refund IAP
- headerOrKey: refund:{provider}:{providerTransactionId} (ledger key)
- storage: transactions.idempotency_key; tra cứu receipt qua iap_receipts (provider, provider_transaction_id)
- uniqueConstraint: uq_transactions_idempotency_key; uq_iap_receipts_provider_txn
- replayBehavior: Webhook đã verify luôn trả 200 kể cả không tìm thấy receipt để store không retry; refund truyền khoá refund:{provider}:{providerTransactionId} làm idempotencyKey của ledger.reverse nên lặp lại bị dedupe.
- conflictBehavior: Chữ ký sai thì verifier ném 401; refund Google do IapRefundPollService xử lý theo chu kỳ, không refund tại webhook.
- evidence: apps/core-api/src/modules/economy/services/refund.service.ts:68
- moreEvidence (evidence): apps/core-api/src/modules/economy/webhooks/economy-webhooks.controller.ts:38, apps/core-api/src/modules/economy/webhooks/economy-webhooks.controller.ts:111, apps/core-api/src/modules/economy/services/refund.service.ts:66

### #7

- scope: Gửi quà (GiftService)
- headerOrKey: giftSendIdempotencyKey(userId, Idempotency-Key) truyền xuống ledger
- storage: transactions.idempotency_key; gift_events.transaction_id
- uniqueConstraint: uq_transactions_idempotency_key; uq_gift_events_transaction
- replayBehavior: Ledger trả cùng giao dịch; gift_events ánh xạ 1:1 với transaction nên không sinh GiftEvent thứ hai.
- conflictBehavior: Cùng key khác nội dung thì 409 từ ledger.
- evidence: apps/core-api/src/modules/gift/gift.service.ts:215
- moreEvidence (evidence): apps/core-api/src/database/migrations/1752700000000-party-room-gift.ts:89

### #8

- scope: Mua vật phẩm avatar (AvatarService)
- headerOrKey: avatarBuyIdempotencyKey(userId, Idempotency-Key) cho bước spend
- storage: transactions.idempotency_key; quyền sở hữu ghi bằng ON CONFLICT DO NOTHING
- uniqueConstraint: uq_transactions_idempotency_key
- replayBehavior: Retry cùng Idempotency-Key trả replayed=true và không trừ diamond lần hai; ghi sở hữu idempotent.
- conflictBehavior: Cùng key khác nội dung thì 409 từ ledger.
- evidence: apps/core-api/src/modules/avatar/dto/avatar.dtos.ts:45
- moreEvidence (evidence): apps/core-api/src/modules/avatar/avatar.service.ts:89, apps/core-api/src/modules/avatar/avatar.service.ts:181

### #9

- scope: Vào hàng đợi ghép đôi POST /matching/tickets
- headerOrKey: joinIdempotencyKey(userId, Idempotency-Key)
- storage: match_tickets.idempotency_key
- uniqueConstraint: uq_match_tickets_idempotency_key
- replayBehavior: Nhận diện replay trước khi consume quota nên retry không tốn quota; khớp thì trả lại ticket cũ.
- conflictBehavior: assertJoinReplay so userId, matchType, genderPreference, useDiamond; khi unique violation phải kiểm tra replay trước vì uq_match_tickets_active_user cũng có thể vi phạm.
- evidence: apps/core-api/src/modules/matching/matching.service.ts:117
- moreEvidence (evidence): apps/core-api/src/modules/matching/matching.service.ts:97, apps/core-api/src/modules/matching/matching.service.ts:171, apps/core-api/src/database/migrations/1752200000000-matching-core.ts:30

### #10

- scope: Speed-up ticket ghép đôi
- headerOrKey: speedupIdempotencyKey(userId, Idempotency-Key), dạng matching:speedup:{userId}:{key}, truyền xuống ledger spend
- storage: transactions.idempotency_key
- uniqueConstraint: uq_transactions_idempotency_key
- replayBehavior: Spend idempotent nên retry không trừ tiền lại; bước cập nhật score Redis retry được an toàn.
- conflictBehavior: Cùng key khác nội dung thì 409 từ ledger.
- evidence: apps/core-api/src/modules/matching/matching.service.ts:446
- moreEvidence (evidence): docs/services/matching-service.md:59

### #11

- scope: Chấp nhận invite ghép đôi và requeue sau sweeper
- headerOrKey: inviteAcceptIdempotencyKey(invite.id, role) và requeueIdempotencyKey(session.id, ticket.id) (khoá tất định, không cần header)
- storage: match_tickets.idempotency_key
- uniqueConstraint: uq_match_tickets_idempotency_key
- replayBehavior: Accept lặp đọc lại ticket theo idempotencyKey (In [...]) và dùng lại; request accept thứ hai thấy invite đã đổi trạng thái do khoá FOR UPDATE.
- conflictBehavior: Không có so khớp payload; unique key tất định theo invite/role hoặc session/ticket.
- evidence: apps/core-api/src/modules/matching/services/invite.service.ts:572
- moreEvidence (evidence): apps/core-api/src/modules/matching/services/invite.service.ts:534, apps/core-api/src/modules/matching/jobs/ticket-sweeper.service.ts:208

### #12

- scope: Gửi tin nhắn bạn bè (ConversationService)
- headerOrKey: messageIdempotencyKey(senderUserId, Idempotency-Key)
- storage: messages.idempotency_key
- uniqueConstraint: uq_messages_idempotency_key
- replayBehavior: Bắt unique violation rồi đọc lại; cùng conversationId và content thì trả message cũ (client retry sau timeout).
- conflictBehavior: Khác nội dung thì 409 FRIEND MESSAGE_IDEMPOTENCY_CONFLICT.
- evidence: apps/core-api/src/modules/friend/services/conversation.service.ts:181
- moreEvidence (evidence): apps/core-api/src/modules/friend/services/conversation.service.ts:184, apps/core-api/src/database/migrations/1752600000000-friend-chat.ts:39

### #13

- scope: Tạo bài viết feed (FeedService)
- headerOrKey: feedPostIdempotencyKey(userId, Idempotency-Key)
- storage: posts.idempotency_key (unique một phần, chỉ khi khác NULL)
- uniqueConstraint: uq_posts_idempotency_key
- replayBehavior: Bắt unique violation rồi đọc lại; cùng content và imageUrl thì trả bài cũ.
- conflictBehavior: Khác nội dung thì 409 FeedErrors.POST_IDEMPOTENCY_CONFLICT.
- evidence: apps/core-api/src/modules/feed/feed.service.ts:83
- moreEvidence (evidence): apps/core-api/src/modules/feed/feed.service.ts:86, apps/core-api/src/database/migrations/1754300000000-feed-audience.ts:21

### #14

- scope: Tạo story (StoryService)
- headerOrKey: storyIdempotencyKey(userId, Idempotency-Key)
- storage: stories.idempotency_key
- uniqueConstraint: uq_stories_idempotency_key
- replayBehavior: Đọc lại bản ghi cùng key sau unique violation (chưa đọc nhánh so khớp nội dung).
- conflictBehavior: Chưa xác minh trong lượt đọc này.
- evidence: apps/core-api/src/modules/feed/services/story.service.ts:73
- moreEvidence (evidence): apps/core-api/src/database/migrations/1754500000000-story.ts:21
- confidence: low

### #15

- scope: Đặt/tắt mood (MoodService)
- headerOrKey: moodSetIdempotencyKey / moodClearIdempotencyKey (userId, Idempotency-Key)
- storage: mood_status_events.idempotency_key
- uniqueConstraint: uq_mood_status_events_idempotency_key
- replayBehavior: Unique violation trên idempotencyKey được coi là replay hợp lệ, đọc lại event cũ.
- conflictBehavior: Không có so khớp payload (replay hợp lệ theo mã).
- evidence: apps/core-api/src/modules/mood/mood.service.ts:159
- moreEvidence (evidence): apps/core-api/src/modules/mood/mood.service.ts:170, apps/core-api/src/database/migrations/1754100000000-mood.ts:35

### #16

- scope: Bình luận phòng party (PartyRoomService)
- headerOrKey: party:comment:{userId}:{roomId}:{clientKey} (đây là giá trị cột DB, không phải khoá Redis)
- storage: party_room_comments.idempotency_key
- uniqueConstraint: uq_party_room_comments_idempotency_key
- replayBehavior: Retry không nhân đôi comment nhờ unique DB.
- conflictBehavior: Chưa đọc nhánh xử lý trùng trong service.
- evidence: apps/core-api/src/modules/party-room/party-room.constants.ts:27
- moreEvidence (evidence): apps/core-api/src/database/migrations/1756800000000-party-room-presence-chat.ts:29
- confidence: low

### #17

- scope: Tin nhắn Soul Match ẩn danh
- headerOrKey: idempotency_key do client gửi (hàm dựng key chưa đọc)
- storage: soul_chat_messages.idempotency_key
- uniqueConstraint: uq_soul_chat_messages_idempotency_key
- replayBehavior: Client retry không nhân đôi message (theo ghi chú migration).
- conflictBehavior: Chưa xác minh trong lượt đọc này.
- evidence: apps/core-api/src/database/migrations/1752400000000-soul-match.ts:53
- confidence: low

### #18

- scope: Tin nhắn phiên Movie Match
- headerOrKey: movieMessageIdempotencyKey(userId, Idempotency-Key)
- storage: movie_session_messages.idempotency_key
- uniqueConstraint: movie_session_messages_idempotency_key_key
- replayBehavior: Chưa đọc nhánh replay; tên ràng buộc do Postgres tự đặt (UNIQUE inline) theo db-schema.json.
- conflictBehavior: Chưa xác minh trong lượt đọc này.
- evidence: apps/core-api/src/modules/movie-match/movie-match.service.ts:551
- confidence: low

### #19

- scope: Đăng video ngắn và vé hỗ trợ
- headerOrKey: idempotency_key do client gửi (video); (user_id, idempotency_key) cho support ticket
- storage: videos.idempotency_key; support_tickets (user_id, idempotency_key)
- uniqueConstraint: uq_videos_idempotency_key; uq_support_ticket_user_idempotency
- replayBehavior: Chưa đọc nhánh replay; ràng buộc unique chặn bản ghi thứ hai.
- conflictBehavior: Chưa xác minh trong lượt đọc này.
- evidence: apps/core-api/src/database/migrations/1754800000000-short-video.ts:31
- moreEvidence (evidence): apps/core-api/src/database/migrations/1755300000000-support-ticket.ts:22
- confidence: low

### #20

- scope: Đăng nhập/liên kết social và guest (AuthService)
- headerOrKey: (provider, provider_uid) của identity
- storage: auth_identities (provider, provider_uid)
- uniqueConstraint: uq_auth_identities_provider_uid
- replayBehavior: Request song song: transaction thua bắt unique violation rồi đọc identity thắng và trả user hiện có.
- conflictBehavior: Khi nâng cấp guest mà identity đã gắn tài khoản khác thì 409 IDENTITY_ALREADY_LINKED, không tự merge.
- evidence: apps/core-api/src/modules/auth/auth.service.ts:273
- moreEvidence (evidence): apps/core-api/src/modules/auth/auth.service.ts:281, apps/core-api/src/database/migrations/1751900000000-init-auth-user.ts:35

### #21

- scope: Xoay refresh token (TokenService.rotate)
- headerOrKey: token_hash = SHA-256 của refresh token (cookie)
- storage: refresh_tokens.token_hash
- uniqueConstraint: uq_refresh_tokens_token_hash
- replayBehavior: Token đã rotated_at hoặc UPDATE có điều kiện thua race thì coi là reuse.
- conflictBehavior: Reuse thì revoke cả family và trả 401 REFRESH_TOKEN_REUSED.
- evidence: apps/core-api/src/modules/auth/services/token.service.ts:66
- moreEvidence (evidence): apps/core-api/src/database/migrations/1751900000000-init-auth-user.ts:52

### #22

- scope: Đánh giá Soul Match và phản ứng Voice Match
- headerOrKey: (session_id, rater_user_id) và (call_id, rater_user_id)
- storage: soul_match_ratings; voice_match_reactions
- uniqueConstraint: uq_soul_match_ratings_session_rater; uq_voice_match_reactions_call_rater
- replayBehavior: Rating immutable: replay cùng verdict là idempotent; unique DB chặn double tap/retry với reaction.
- conflictBehavior: Verdict khác thì 409.
- evidence: apps/core-api/src/modules/soul-match/soul-match.service.ts:501
- moreEvidence (evidence): apps/core-api/src/modules/calling/entities/voice-match-reaction.entity.ts:9

### #23

- scope: Kết thúc call và webhook LiveKit (CallingService)
- headerOrKey: callId/trạng thái (không có khoá riêng)
- storage: call_sessions.status (khoá dòng FOR UPDATE)
- uniqueConstraint: n/a (idempotent theo trạng thái ended)
- replayBehavior: End idempotent: đã ended thì trả trạng thái hiện tại; webhook LiveKit retry an toàn và luôn trả 200 khi đã verify.
- conflictBehavior: n/a
- evidence: apps/core-api/src/modules/calling/calling.service.ts:438
- moreEvidence (evidence): apps/core-api/src/modules/calling/webhooks/livekit-webhook.controller.ts:24

### #24

- scope: Rate limit Redis consume (RateLimitPort)
- headerOrKey: operationId tuỳ chọn trong RateLimitConsumeRequest (chưa caller nào truyền)
- storage: Redis {rateLimitKey}:rate-limit-reservation:{operationId}
- uniqueConstraint: n/a (khoá Redis)
- replayBehavior: Dùng lại operationId thì Lua trả deduplicated (không tiêu thêm slot) cho tới khi refund hoặc hết hạn.
- conflictBehavior: n/a
- evidence: apps/core-api/src/common/redis/rate-limit.port.ts:15
- moreEvidence (evidence): apps/core-api/src/common/redis/redis-rate-limit.adapter.ts:23
