# Domain rules, error handling, glossary, conventions: glossary

id: system:domain:glossary

Written by an AI agent from the code; every file:line quote was checked automatically (exists within ±3 lines) but NO independent verification pass has run yet. Treat claims as strong leads; open the cited file before editing.

### DIA (Diamond)

- vietnameseOrProductTerm: Diamond / Kim cương
- definition: Tiền ảo trả phí của hệ thống, ghi ở tài khoản sổ cái `user_wallet` với currency `DIA`. Không hết hạn, không rút ra tiền thật, không chuyển trực tiếp giữa user (docs/06).
- codeIdentifiers: LedgerCurrency.Diamond; Wallet.balance; LedgerAccountKind.UserWallet; priceDiamond
- whereToLook: docs/services/economy-service.md:22

### PTS

- vietnameseOrProductTerm: Điểm quy đổi (earnings)
- definition: Điểm người nhận quà được cộng theo tỉ lệ GIFT_POINTS_RATE_PERCENT của giá quà, ghi ở `user_earnings` với currency `PTS`. Không phải Diamond; guest nhận 0.
- codeIdentifiers: Wallet.earnings; LedgerAccountKind.UserEarnings; GIFT_POINTS_RATE_PERCENT; pointsAwarded
- whereToLook: docs/services/economy-service.md:23

### Wallet

- vietnameseOrProductTerm: Ví
- definition: Snapshot đọc nhanh của `balance` (DIA), `earnings` (PTS) và VIP tier/hạn; nguồn sự thật là sổ cái. `balance` có thể âm sau refund.
- codeIdentifiers: Wallet; wallets; Wallet.activeVipTier
- whereToLook: apps/core-api/src/modules/economy/entities/wallet.entity.ts:17

### Ledger account

- vietnameseOrProductTerm: Tài khoản sổ cái
- definition: Tài khoản của sổ kép: `user_wallet`, `user_earnings` của user và các tài khoản hệ thống `system_iap`, `system_revenue`, `system_gift_pool`, `system_points_mint`, `system_adjustment`.
- codeIdentifiers: LedgerAccount; LedgerAccountKind
- whereToLook: apps/core-api/src/modules/economy/entities/ledger-account.entity.ts:8

### Ledger entry

- vietnameseOrProductTerm: Bút toán
- definition: Một dòng Nợ hoặc Có (amount > 0, theo currency) thuộc một giao dịch; bảng `ledger_entries` là append-only nhờ trigger.
- codeIdentifiers: LedgerEntry; LedgerDirection; ledger_entries
- whereToLook: apps/core-api/src/modules/economy/entities/ledger-entry.entity.ts:22

### Transaction

- vietnameseOrProductTerm: Giao dịch
- definition: Nhóm tối thiểu 2 bút toán cân bằng, lưu ở bảng `transactions` bằng entity `LedgerTransaction`; có `type` (iap_purchase, vip_purchase, matching_speedup, gift_send...), idempotencyKey và requestHash.
- codeIdentifiers: LedgerTransaction; TransactionType; TransactionStatus; transactions; requestHash
- whereToLook: apps/core-api/src/modules/economy/entities/transaction.entity.ts:37

### Reversal

- vietnameseOrProductTerm: Bút toán đảo
- definition: Giao dịch mới đảo chiều các bút toán của giao dịch gốc và trỏ `reversalOf` về nó; dùng cho refund, chargeback, sửa sai và là cách duy nhất để sửa sổ cái.
- codeIdentifiers: LedgerService.reverse; reversalOf; TransactionType.Reversal; TransactionType.Adjustment
- whereToLook: apps/core-api/src/modules/economy/services/ledger.service.ts:188

### Idempotency-Key

- vietnameseOrProductTerm: Khoá idempotency
- definition: Header `Idempotency-Key` (tối đa 128 ký tự) cho request có tác dụng phụ không được lặp; service prefix theo domain và chốt chặn bằng unique constraint.
- codeIdentifiers: IDEMPOTENCY_KEY_HEADER; IdempotencyKey; ApiIdempotencyKeyHeader; uq_transactions_idempotency_key
- whereToLook: apps/core-api/src/common/decorators/idempotency-key.decorator.ts:11

### Outbox event

- vietnameseOrProductTerm: Hộp thư sự kiện (outbox)
- definition: Bản ghi sự kiện Economy ghi cùng transaction nghiệp vụ để publish sau (chống dual-write); không thay transaction đồng bộ cho debit/credit.
- codeIdentifiers: OutboxEvent; ECONOMY_OUTBOX_MAX_ATTEMPTS
- whereToLook: docs/02-domain-model.md:53

### VIP plan / tier

- vietnameseOrProductTerm: Gói VIP / SVIP
- definition: Catalog gói `VipPlan` (số ngày, giá DIA) và tier `VipTier` (vip, svip) lưu ở Wallet; tier ảnh hưởng quota và giá speed-up, hết hạn được suy ra khi đọc.
- codeIdentifiers: VipPlan; VipTier; Wallet.activeVipTier; getActiveVipTier
- whereToLook: apps/core-api/src/modules/economy/entities/wallet.entity.ts:3

### Capability

- vietnameseOrProductTerm: Khả năng runtime
- definition: Cờ sẵn sàng của tích hợp theo môi trường (vd `auth.phoneOtp`, `topUp.web`, `video.upload`, `notifications.push`) để UI hỏi qua CapabilitiesController thay vì hard-code.
- codeIdentifiers: CAPABILITY_IDS; CapabilitiesService; CapabilitiesController; parseMaintenanceCapabilities
- whereToLook: apps/core-api/src/config/capabilities.ts:1

### Ticket

- vietnameseOrProductTerm: Vé ghép (ticket)
- definition: Một lượt xếp hàng matching của một user (soul hoặc voice) với trạng thái queued, matched, confirmed, expired, cancelled.
- codeIdentifiers: MatchTicket; MatchTicketStatus; match_tickets; MatchType
- whereToLook: apps/core-api/src/modules/matching/entities/match-ticket.entity.ts:31

### Shard

- vietnameseOrProductTerm: Shard hàng đợi
- definition: Nhóm ứng viên cùng (match_type, region, age_band) được matcher quét cùng nhau; gender không phải khoá shard mà được kiểm lúc ghép trong tryPair.
- codeIdentifiers: idx_match_tickets_status_shard; matching:shards:active; ageBand; region; MATCHING_AGE_BAND_SIZE
- whereToLook: apps/core-api/src/modules/matching/entities/match-ticket.entity.ts:71

### Speed-up

- vietnameseOrProductTerm: Đẩy nhanh
- definition: Trả DIA để tăng priority của ticket đang queued; giá theo tier (50/40/30) do server đọc và số lần không giới hạn.
- codeIdentifiers: MatchingService.speedup; getSpeedupPriceDiamondForUser; TransactionType.MatchingSpeedup; speedupIdempotencyKey; MATCHING_PRIORITY_BOOST_MS
- whereToLook: apps/core-api/src/modules/matching/matching.service.ts:263

### Extra match

- vietnameseOrProductTerm: Mua thêm lượt
- definition: Sau khi hết quota miễn phí trong ngày, user trả 20 DIA (Soul) hoặc 40 DIA (Voice) để mở thêm một lượt; khoản trừ ghi ledger cùng transaction tạo ticket.
- codeIdentifiers: getExtraMatchPriceDiamond; TransactionType.MatchingExtraMatch; MATCHING_EXTRA_SOUL_PRICE_DIAMOND; MATCHING_EXTRA_VOICE_PRICE_DIAMOND
- whereToLook: apps/core-api/src/modules/matching/matching.service.ts:276

### Daily quota

- vietnameseOrProductTerm: Quota miễn phí mỗi ngày
- definition: Số lượt Soul/Voice miễn phí theo tier trong ngày UTC, đếm ở `MatchDailyQuota`; guest dùng `GuestMatchQuota` với khoá HMAC.
- codeIdentifiers: MatchDailyQuota; GuestMatchQuota; MATCHING_DAILY_SOUL_LIMIT; MATCHING_DAILY_VOICE_LIMIT; MATCHING_GUEST_DAILY_LIMIT
- whereToLook: apps/core-api/src/modules/matching/entities/match-daily-quota.entity.ts:10

### Guest account

- vietnameseOrProductTerm: Tài khoản khách (guest)
- definition: User dùng thử với `isGuest = true`, chưa gắn phone/social; bị hạn chế nạp tiền, nhận PTS và quota match, nâng cấp bằng cách gắn identity vào cùng userId.
- codeIdentifiers: User.isGuest; AuthProvider.Guest; assertGuestIdentityStillActive; AUTH_GUEST_UPGRADE_NOT_ALLOWED
- whereToLook: apps/core-api/src/modules/auth/auth.service.ts:314

### Guest device token

- vietnameseOrProductTerm: Token thiết bị guest
- definition: Token do Auth cấp lúc guest login (TTL AUTH_GUEST_DEVICE_TOKEN_TTL_DAYS, mặc định 30 ngày) và gắn với user; guest phải gửi qua header `X-Guest-Device-Token` khi vào matching.
- codeIdentifiers: GuestDeviceTokenService; X-Guest-Device-Token; AUTH_GUEST_DEVICE_TOKEN_SECRET; AUTH_GUEST_DEVICE_TOKEN_TTL_DAYS; MATCHING_GUEST_DEVICE_TOKEN_REQUIRED
- whereToLook: apps/core-api/src/modules/auth/dto/auth-tokens.dto.ts:17

### Soul Match

- vietnameseOrProductTerm: Ghép chat ẩn danh
- definition: Chat text ẩn danh có thời hạn giữa hai người; rating rude/boring/like, chỉ mutual-like mới lộ danh tính và tạo quan hệ bền vững.
- codeIdentifiers: MatchType.Soul; SoulChatMessage; SoulMatchRating; SOUL_CHAT_MESSAGE_MAX_LENGTH
- whereToLook: docs/01-product-features.md:14

### Voice Match

- vietnameseOrProductTerm: Ghép gọi thoại
- definition: Cuộc gọi 1-1 qua SFU sinh từ ticket voice, tối đa CALLING_FREE_CALL_SECONDS (420 giây) cho mọi tier; mutual-like mới tạo Friendship.
- codeIdentifiers: CallKind.VoiceMatch; CallSession; VoiceMatchReaction; CALLING_FREE_CALL_SECONDS; CallEndReason.FreeLimit
- whereToLook: apps/core-api/src/modules/calling/entities/call-session.entity.ts:15

### Friend call

- vietnameseOrProductTerm: Gọi bạn bè
- definition: Cuộc gọi bền vững `CallKind.Friend` giữa hai user follow nhau và không block nhau; không có timer free hay MatchSession.
- codeIdentifiers: CallKind.Friend; CallingService.joinFriendCall; ProfileSocialService.canCall
- whereToLook: apps/core-api/src/modules/calling/calling.service.ts:203

### Palm Match

- vietnameseOrProductTerm: Ghép xem chỉ tay
- definition: Hàng đợi ẩn danh, lật thẻ theo lượt, compatibility snapshot và like/skip; kết quả do server chốt, không phải chẩn đoán thật.
- codeIdentifiers: PalmMatchSession; PalmReadingTemplate; PALM_MATCH_QUEUE_MAX_WAIT_SECONDS
- whereToLook: apps/core-api/src/modules/palm-match/entities/palm-match-session.entity.ts:27

### Movie Match

- vietnameseOrProductTerm: Xem phim cùng nhau
- definition: Hai chế độ: bạn bè chủ động xem URL đã chọn, hoặc hàng đợi ẩn danh với video do server chọn; playback last-write-wins, không có pipeline VOD riêng.
- codeIdentifiers: MovieSession; MOVIE_MATCH_QUEUE_MAX_WAIT_SECONDS; MOVIE_MATCH_URL_MAX_LENGTH
- whereToLook: apps/core-api/src/modules/movie-match/entities/movie-session.entity.ts:54

### Party room

- vietnameseOrProductTerm: Phòng nhóm (Party Room)
- definition: Voice room nhiều người với role host, speaker, audience; quyền và membership do Core API giữ, LiveKit chỉ là transport.
- codeIdentifiers: PartyRoom; PartyRoomMember; PartyRole; PARTY_MAX_MEMBERS
- whereToLook: apps/core-api/src/modules/party-room/entities/party-room-member.entity.ts:10

### Speaker limit

- vietnameseOrProductTerm: Giới hạn speaker
- definition: Số speaker tối đa của phòng, chụp snapshot từ PARTY_MAX_SPEAKERS (mặc định 8) lúc tạo phòng; chạm giới hạn thì 409 PARTY_SPEAKER_LIMIT_REACHED.
- codeIdentifiers: PartyRoom.speakerLimit; PARTY_MAX_SPEAKERS; PARTY_SPEAKER_LIMIT_REACHED
- whereToLook: apps/core-api/src/modules/party-room/entities/party-room.entity.ts:60

### Streak

- vietnameseOrProductTerm: Chuỗi trò chuyện
- definition: Số ngày UTC liên tiếp mà cả hai bên cùng nhắn trong một conversation, lưu ở `ConversationStreak`; có grace cho 1 ngày lỡ và các milestone.
- codeIdentifiers: ConversationStreak; StreakService; graceUsedForDate; STREAK_MILESTONE_DAYS
- whereToLook: apps/core-api/src/modules/friend/services/streak.service.ts:75

### Gift event

- vietnameseOrProductTerm: Sự kiện tặng quà
- definition: Bản ghi mỗi lần tặng quà, snapshot giá quà và tỉ lệ PTS tại thời điểm tặng, đi cùng giao dịch Economy.
- codeIdentifiers: GiftEvent; Gift; TransactionType.GiftSend
- whereToLook: apps/core-api/src/modules/gift/entities/gift-event.entity.ts:15

### Friendship

- vietnameseOrProductTerm: Bạn bè
- definition: Quan hệ bạn bè bền vững; được tạo bằng `ensureFriendship(manager, a, b, source)` khi mutual-like (vd source `FriendshipSource.VoiceMatch`).
- codeIdentifiers: Friendship; FriendshipSource; FriendService.ensureFriendship; Conversation
- whereToLook: apps/core-api/src/modules/friend/entities/friendship.entity.ts:21

### Profile follow

- vietnameseOrProductTerm: Theo dõi (follow)
- definition: Quan hệ theo dõi một chiều `ProfileFollow` (cờ active) độc lập với chat; follow hai chiều mở khoá friend call.
- codeIdentifiers: ProfileFollow; ProfileSocialService.areMutuallyFollowing; ProfileSocialService.canCall
- whereToLook: apps/core-api/src/modules/friend/entities/profile-follow.entity.ts:20

### First chat

- vietnameseOrProductTerm: Chat đầu tiên từ hồ sơ
- definition: Lần đầu một cặp mở chat trực tiếp được ghi ở `ProfileChatContact` theo ngày UTC; vượt ngưỡng mỗi ngày của hồ sơ đích thì phải tặng quà (HTTP 402).
- codeIdentifiers: ProfileChatContact; firstContactDate; PROFILE_DIRECT_MESSAGE_DAILY_FIRST_CHAT_THRESHOLD; PROFILE_SOCIAL_MESSAGE_GIFT_REQUIRED
- whereToLook: apps/core-api/src/modules/friend/services/profile-social.service.ts:433

### Match invite

- vietnameseOrProductTerm: Lời mời ghép trực tiếp
- definition: Lời mời Voice/Soul gửi thẳng cho một user; accept tạo ticket/session bỏ qua hàng đợi shard và vẫn kiểm lại canPair.
- codeIdentifiers: MatchInvite; InviteService; MATCHING_INVITE_TTL_SECONDS; MATCHING_INVITE_RATE_LIMIT_PER_HOUR
- whereToLook: apps/core-api/src/modules/matching/entities/match-invite.entity.ts:36

### Trust score

- vietnameseOrProductTerm: Điểm tin cậy
- definition: Số nội bộ (mặc định 100, không lộ ra API) giảm khi bị report và làm chậm priority trong matching, không chặn hẳn.
- codeIdentifiers: User.trustScore; SAFETY_TRUST_PENALTY_PER_REPORT; SAFETY_TRUST_SCORE_FLOOR; MATCHING_TRUST_PENALTY_MS_PER_POINT
- whereToLook: apps/core-api/src/modules/user/entities/user.entity.ts:58

### Report / Block

- vietnameseOrProductTerm: Báo cáo và chặn
- definition: `Report` (append-only) và `Block` là đầu vào của canPair, isBlocked và getHiddenUserIds mà matching, discovery, feed, chat kiểm lại tại điểm quyết định.
- codeIdentifiers: Report; Block; SafetyService.canPair; SafetyService.isBlocked; SafetyService.getHiddenUserIds
- whereToLook: apps/core-api/src/modules/safety/safety.service.ts:291

### Nearby

- vietnameseOrProductTerm: Gần bạn (Nearby)
- definition: Duyệt user lân cận khi chủ hồ sơ opt-in `nearbyVisible`; vị trí được làm tròn khoảng 500 m khi ghi và chỉ trả `distanceBucket` (hoặc null).
- codeIdentifiers: DiscoverySetting.nearbyVisible; UserLocation; DISCOVERY_LOCATION_QUANTIZE_DEGREES; distanceBucket
- whereToLook: apps/core-api/src/modules/discovery/entities/discovery-setting.entity.ts:19

### Video status

- vietnameseOrProductTerm: Trạng thái video ngắn
- definition: Các trạng thái uploading, processing, pending_review, published, removed, rejected, failed; sau transcode video vào pending_review (mode `pre`) hoặc published (mode `post`).
- codeIdentifiers: VideoStatus; VIDEO_MODERATION_MODE; VIDEO_REPORT_AUTOHIDE_THRESHOLD
- whereToLook: apps/core-api/src/modules/short-video/entities/video.entity.ts:5
