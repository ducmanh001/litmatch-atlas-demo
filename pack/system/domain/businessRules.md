# Domain rules, error handling, glossary, conventions: businessRules

id: system:domain:businessRules

Written by an AI agent from the code; every file:line quote was checked automatically (exists within ±3 lines) but NO independent verification pass has run yet. Treat claims as strong leads; open the cited file before editing.

### BR-01

- rule: Guest (chưa gắn phone/social) không được tạo đơn nạp Diamond qua payOS; controller trả 403 ECONOMY_PAYOS_GUEST_FORBIDDEN.
- appliesTo: economy; auth
- enforcedAt: service
- enforcedBy: EconomyController.assertNotGuestForPayos
- whatBreaksIfRemoved: Guest (user tạm chưa xác minh) nạp được Diamond và dễ farm bằng nhiều tài khoản guest; docs/06 coi không nạp Diamond là hạn chế cốt lõi của guest. Lưu ý: chỉ đường payOS có chặn, luồng IAP verify không có kiểm tra guest trong mã economy.
- evidence (evidence): apps/core-api/src/modules/economy/economy.controller.ts:167, apps/core-api/src/modules/economy/economy.controller.ts:171, apps/core-api/src/modules/economy/economy.controller.ts:90, docs/06-domain-rules.md:30

### BR-02

- rule: Người tặng mất DIA bằng đúng giá quà; người nhận được PTS = floor(giá × GIFT_POINTS_RATE_PERCENT / 100) (mặc định 40, tối đa 99) và guest nhận 0 PTS; tỉ lệ được snapshot vào GiftEvent.
- appliesTo: gift; economy
- enforcedAt: service
- enforcedBy: GiftService (pointsAwarded) và Joi max(99) của GIFT_POINTS_RATE_PERCENT
- whatBreaksIfRemoved: Quà thành kênh chuyển tiền ngang hàng hoặc farm điểm bằng guest (lý do ghi ở docs/06); đổi tỉ lệ mà không snapshot làm sai dữ liệu lịch sử.
- evidence (evidence): apps/core-api/src/modules/gift/gift.service.ts:200, apps/core-api/src/modules/gift/gift.service.ts:207, apps/core-api/src/modules/gift/entities/gift-event.entity.ts:48, apps/core-api/src/config/env.validation.ts:603, docs/06-domain-rules.md:26

### BR-03

- rule: Guest có MATCHING_GUEST_DAILY_LIMIT (3) lượt match mỗi ngày UTC, đếm trên ba khoá HMAC (user, device token, network IP); một khoá chạm trần là bị 429. Guest phải gửi X-Guest-Device-Token do Auth ký cho chính user, thiếu thì 401.
- appliesTo: matching; auth
- enforcedAt: service
- enforcedBy: GuestMatchQuotaService (authorize, consume, keyHash) với khoá pessimistic_write
- whatBreaksIfRemoved: Tạo guest mới (hoặc đổi thiết bị/IP) reset quota, match miễn phí không giới hạn; docs/06 yêu cầu không được phép chỉ reset một trong ba trục.
- evidence (evidence): apps/core-api/src/modules/matching/services/guest-match-quota.service.ts:73, apps/core-api/src/modules/matching/services/guest-match-quota.service.ts:74, apps/core-api/src/modules/matching/services/guest-match-quota.service.ts:110, apps/core-api/src/modules/matching/services/guest-match-quota.service.ts:63, apps/core-api/src/modules/matching/services/guest-match-quota.service.ts:229, apps/core-api/src/config/env.validation.ts:485, docs/06-domain-rules.md:34

### BR-04

- rule: Nâng cấp guest giữ nguyên userId (gắn thêm identity vào user hiện có). Identity đã gắn với user khác thì 409 AUTH_IDENTITY_ALREADY_LINKED, không tự merge; tài khoản đã hết là guest thì 409 AUTH_GUEST_UPGRADE_NOT_ALLOWED; credential guest cũ bị thu hồi (401 AUTH_GUEST_IDENTITY_RETIRED).
- appliesTo: auth; user; economy
- enforcedAt: service
- enforcedBy: AuthService (luồng upgrade, assertGuestIdentityStillActive)
- whatBreaksIfRemoved: Tự động merge hoặc tạo user mới sẽ gộp nhầm ledger của hai người hoặc làm guest mất ví/lịch sử; credential guest cũ còn đăng nhập được sau nâng cấp.
- evidence (evidence): apps/core-api/src/modules/auth/auth.service.ts:239, apps/core-api/src/modules/auth/auth.service.ts:246, apps/core-api/src/modules/auth/auth.service.ts:317, docs/06-domain-rules.md:31

### BR-05

- rule: Refresh token xoay vòng mỗi lần dùng; dùng lại token đã xoay thì 401 AUTH_REFRESH_TOKEN_REUSED và phiên bị thu hồi.
- appliesTo: auth
- enforcedAt: service
- enforcedBy: TokenService (kết quả rotation `reused`)
- whatBreaksIfRemoved: Refresh token bị đánh cắp dùng lại không bị phát hiện; phía client single-flight và Web Locks tồn tại để chính nó không tự gây ra reuse.
- evidence (evidence): apps/core-api/src/modules/auth/services/token.service.ts:94, apps/core-api/src/modules/auth/services/token.service.ts:97, libs/api-client/src/lib/client.ts:111

### BR-06

- rule: OTP 6 chữ số được trả thẳng trong response (không gửi SMS) là ngoại lệ sản phẩm có chủ đích; TTL 300 giây, tối đa 5 lần thử, 5 yêu cầu mỗi giờ; mã không được log, gửi analytics hay lưu bền.
- appliesTo: auth
- enforcedAt: service
- enforcedBy: OtpRequestedDto, AUTH_OTP_* trong Joi, REDACT_PATHS
- whatBreaksIfRemoved: Client mất luồng toast/tự điền OTP; bỏ giới hạn thử/giờ mở đường brute-force; log mã OTP làm lộ credential.
- evidence (evidence): apps/core-api/src/modules/auth/dto/otp-requested.dto.ts:6, apps/core-api/src/config/env.validation.ts:293, apps/core-api/src/config/env.validation.ts:294, apps/core-api/src/config/env.validation.ts:295, docs/05-coding-standards.md:102, libs/logger/src/lib/redact.ts:11

### BR-07

- rule: Mỗi giao dịch có tối thiểu 2 bút toán, amount nguyên dương và tổng Nợ bằng tổng Có theo từng currency (DIA, PTS) trước khi commit; vi phạm là lỗi lập trình (Error thường, ra 500).
- appliesTo: economy
- enforcedAt: both
- enforcedBy: LedgerService.validateEntries (service) và CHECK (amount > 0) của ledger_entries (db)
- whatBreaksIfRemoved: Ví và sổ cái lệch nhau (Wallet chỉ là snapshot dẫn xuất), tiền sinh ra hoặc mất mà không dấu vết; job đối soát sẽ báo lệch.
- evidence (evidence): apps/core-api/src/modules/economy/services/ledger.service.ts:324, apps/core-api/src/modules/economy/services/ledger.service.ts:328, apps/core-api/src/modules/economy/services/ledger.service.ts:339, apps/core-api/src/database/migrations/1752000000000-economy-ledger.ts:56, docs/06-domain-rules.md:24

### BR-08

- rule: ledger_entries là append-only: trigger DB chặn UPDATE/DELETE; muốn sửa phải tạo giao dịch đảo (reversal) trỏ `reversalOf` về giao dịch gốc.
- appliesTo: economy
- enforcedAt: both
- enforcedBy: trigger trg_ledger_entries_append_only (hàm forbid_ledger_entry_mutation) và LedgerService.reverse
- whatBreaksIfRemoved: Có thể sửa/xoá lịch sử tiền, mất khả năng kiểm toán; refund/chargeback không còn truy vết về giao dịch gốc.
- evidence (evidence): apps/core-api/src/database/migrations/1752000000000-economy-ledger.ts:77, apps/core-api/src/database/migrations/1752000000000-economy-ledger.ts:72, apps/core-api/src/modules/economy/services/ledger.service.ts:184, apps/core-api/src/modules/economy/services/ledger.service.ts:237

### BR-09

- rule: Số dư ví chỉ được âm khi giao dịch là Reversal hoặc Adjustment (refund, chargeback, admin sửa sai); mọi giao dịch tiêu khác làm số dư < 0 bị từ chối 422 ECONOMY_WALLET_INSUFFICIENT_BALANCE, được kiểm lại đúng lúc trừ tiền trong transaction.
- appliesTo: economy; gift; matching
- enforcedAt: service
- enforcedBy: LedgerService (mayGoNegative); DB cố ý không có CHECK balance >= 0 (chỉ earnings >= 0)
- whatBreaksIfRemoved: Cấm âm tuyệt đối thì refund sau khi đã tiêu sẽ thất bại; cho âm ở mọi loại giao dịch thì user tiêu vượt số dư.
- evidence (evidence): apps/core-api/src/modules/economy/services/ledger.service.ts:451, apps/core-api/src/modules/economy/services/ledger.service.ts:454, apps/core-api/src/modules/economy/services/ledger.service.ts:455, apps/core-api/src/database/migrations/1752000000000-economy-ledger.ts:8, apps/core-api/src/database/migrations/1752000000000-economy-ledger.ts:88

### BR-10

- rule: Idempotency key là unique constraint DB (`uq_transactions_idempotency_key`); cùng key cùng nội dung thì trả lại giao dịch cũ (replayed), cùng key khác request_hash thì 409 *_IDEMPOTENCY_CONFLICT.
- appliesTo: economy; matching; gift; friend; feed; party-room; soul-match; movie-match; short-video; support
- enforcedAt: both
- enforcedBy: LedgerService.recordInManager/assertSameRequest và unique index uq_transactions_idempotency_key
- whatBreaksIfRemoved: Request gửi lại (mạng chập chờn, double-click) trừ tiền hai lần; cùng key khác nội dung bị chấp nhận âm thầm.
- evidence (evidence): apps/core-api/src/modules/economy/services/ledger.service.ts:367, apps/core-api/src/modules/economy/services/ledger.service.ts:370, apps/core-api/src/modules/economy/services/ledger.service.ts:107, apps/core-api/src/database/migrations/1752000000000-economy-ledger.ts:43, docs/05-coding-standards.md:142

### BR-11

- rule: Mua VIP khi đang active thì gia hạn cộng dồn: expiry = max(now, expiry hiện tại) + số ngày gói; hết hạn tự downgrade bằng cách derive khi đọc (Wallet.activeVipTier), không cần cron.
- appliesTo: economy; matching
- enforcedAt: service
- enforcedBy: EconomyService (mua VIP trong withinTransaction) và getter Wallet.activeVipTier
- whatBreaksIfRemoved: Mua VIP lúc đang active làm mất ngày còn lại; nếu bỏ derive khi đọc thì tier không tự hết hạn.
- evidence (evidence): apps/core-api/src/modules/economy/economy.service.ts:422, apps/core-api/src/modules/economy/economy.service.ts:419, apps/core-api/src/modules/economy/entities/wallet.entity.ts:36, apps/core-api/src/modules/economy/entities/wallet.entity.ts:37, docs/06-domain-rules.md:42

### BR-12

- rule: Refund/chargeback IAP ghi bút toán đảo (không sửa/xoá giao dịch gốc), idempotent theo (provider, providerTransactionId); hai request cùng reverse thì chỉ một bên thắng nhờ UPDATE có điều kiện status.
- appliesTo: economy
- enforcedAt: service
- enforcedBy: RefundService.refundIapPurchase và LedgerService.reverse
- whatBreaksIfRemoved: Webhook retry hoặc job quét trùng sẽ hoàn tiền nhiều lần; sửa/xoá giao dịch gốc làm mất dấu vết.
- evidence (evidence): apps/core-api/src/modules/economy/services/refund.service.ts:20, apps/core-api/src/modules/economy/services/refund.service.ts:35, apps/core-api/src/modules/economy/services/ledger.service.ts:186, docs/06-domain-rules.md:38

### BR-13

- rule: Giá speed-up theo tier: Regular 50, VIP 40, SVIP 30 DIA, do server đọc ngay trước khi trừ. Quota miễn phí mỗi ngày UTC: Regular 10 Soul + 10 Voice, VIP 30 + 20, SVIP 60 + 40; hết quota thì 429 MATCHING_DAILY_QUOTA_EXCEEDED hoặc mua thêm lượt (Soul 20, Voice 40 DIA; docs/06: ghi ledger cùng transaction tạo ticket).
- appliesTo: matching; economy
- enforcedAt: both
- enforcedBy: MatchingService.getSpeedupPriceDiamondForUser/getExtraMatchPriceDiamond; GuestMatchQuotaService (hàng match_daily_quotas khoá pessimistic_write)
- whatBreaksIfRemoved: Client tự hard-code giá sẽ lệch tier; bỏ quota làm match miễn phí vô hạn; giá hiển thị và giá trừ có thể lệch nhau.
- confidence: medium
- evidence (evidence): apps/core-api/src/modules/matching/matching.service.ts:256, apps/core-api/src/modules/matching/matching.service.ts:265, apps/core-api/src/config/env.validation.ts:471, apps/core-api/src/config/env.validation.ts:472, apps/core-api/src/config/env.validation.ts:473, apps/core-api/src/config/env.validation.ts:490, apps/core-api/src/config/env.validation.ts:494, apps/core-api/src/modules/matching/services/guest-match-quota.service.ts:185, apps/core-api/src/modules/matching/services/guest-match-quota.service.ts:188, apps/core-api/src/modules/matching/matching.service.ts:142, docs/06-domain-rules.md:44

### BR-14

- rule: Một user chỉ có một ticket queued/matched tại một thời điểm: partial unique index `uq_match_tickets_active_user`, vi phạm trả 409 MATCHING_TICKET_ALREADY_QUEUED.
- appliesTo: matching
- enforcedAt: both
- enforcedBy: unique index uq_match_tickets_active_user và MatchingService (violatedConstraint)
- whatBreaksIfRemoved: User có nhiều ticket đồng thời, bị ghép trùng và trừ quota/ví nhiều lần; hai request đua nhau chỉ còn dựa vào kiểm tra ở tầng ứng dụng.
- evidence (evidence): apps/core-api/src/modules/matching/matching.service.ts:188, apps/core-api/src/modules/matching/matching.service.ts:191, apps/core-api/src/database/migrations/1752200000000-matching-core.ts:35, apps/core-api/src/database/migrations/1752200000000-matching-core.ts:6

### BR-15

- rule: canPair loại cặp có block active hai chiều hoặc có report gần đây trong SAFETY_REMATCH_COOLDOWN_DAYS (30 ngày); được kiểm trong matcher và kiểm lại lúc accept lời mời, nếu fail thì chuyển invite sang declined và commit trước khi ném lỗi.
- appliesTo: matching; safety
- enforcedAt: service
- enforcedBy: SafetyService.canPair (qua MatchInteractionPolicy) tại MatcherWorkerService.tryPair và InviteService.precheckAccept
- whatBreaksIfRemoved: Ghép lại người vừa chặn/report; lời mời accept sau khi phát sinh block vẫn tạo session; ném lỗi trong cùng transaction làm mất trạng thái declined.
- evidence (evidence): apps/core-api/src/modules/safety/safety.service.ts:291, apps/core-api/src/modules/safety/safety.service.ts:293, apps/core-api/src/modules/safety/safety.service.ts:298, apps/core-api/src/modules/matching/jobs/matcher-worker.service.ts:277, apps/core-api/src/modules/matching/services/invite.service.ts:475, apps/core-api/src/modules/matching/services/invite.service.ts:287, apps/core-api/src/config/env.validation.ts:607

### BR-16

- rule: Preference giới tính chỉ khớp khi gender đối phương bằng đúng giá trị chọn (unknown/other chỉ ghép với `any`), kiểm bằng gender đọc tươi lúc ghép; region/ageBand do server derive từ hồ sơ. Ngày sinh chỉ cần hợp lệ và không ở tương lai, không phải access gate; bộ lọc tuổi là preference và loại user chưa khai ngày sinh khi bật.
- appliesTo: matching; user; discovery
- enforcedAt: service
- enforcedBy: genderPreferenceAllows (MatcherWorkerService.tryPair), UserService.assertBirthDateValid và bộ lọc ageMin/ageMax
- whatBreaksIfRemoved: Ghép sai preference; client có thể giả mạo region/ageBand nếu không derive ở server; chặn truy cập theo ngày sinh làm hỏng onboarding.
- evidence (evidence): apps/core-api/src/modules/matching/entities/match-ticket.entity.ts:28, apps/core-api/src/modules/matching/jobs/matcher-worker.service.ts:263, apps/core-api/src/modules/matching/dto/matching.dtos.ts:12, apps/core-api/src/modules/user/user.service.ts:315, apps/core-api/src/modules/user/user.service.ts:408, docs/06-domain-rules.md:39

### BR-17

- rule: Trust score dưới 100 chỉ cộng thêm ms vào score của ticket lúc enqueue (snapshot một lần; MATCHING_TRUST_PENALTY_MS_PER_POINT, tối đa 120000 ms) nên làm chậm priority chứ không chặn hẳn matching; ban thật là UserStatus.Banned. trustScore mặc định 100 và không lộ ra API.
- appliesTo: matching; safety; user
- enforcedAt: service
- enforcedBy: MatchingService và InviteService (penalty theo trustScore)
- whatBreaksIfRemoved: User bị report nhiều vẫn được ghép ưu tiên như thường; chặn hẳn thay vì làm chậm là đổi hành vi sản phẩm.
- evidence (evidence): apps/core-api/src/modules/matching/matching.service.ts:682, apps/core-api/src/modules/matching/matching.service.ts:678, apps/core-api/src/config/env.validation.ts:478, apps/core-api/src/config/env.validation.ts:484, apps/core-api/src/modules/user/entities/user.entity.ts:58

### BR-18

- rule: Voice Match tối đa CALLING_FREE_CALL_SECONDS (420 giây) cho mọi tier, server tự kết thúc bằng free_limit; mất kết nối quá CALLING_RECONNECT_WINDOW_SECONDS (30 giây) thì call kết thúc.
- appliesTo: calling
- enforcedAt: service
- enforcedBy: CallTickerService (processActiveCalls, sweepReconnectTimeouts)
- whatBreaksIfRemoved: Cuộc gọi không tự dừng sau 7 phút (client không tự enforce); rớt mạng giữ call Active vô hạn; đổi 420 giây là đổi cam kết sản phẩm cho mọi tier.
- evidence (evidence): apps/core-api/src/modules/calling/jobs/call-ticker.service.ts:30, apps/core-api/src/modules/calling/jobs/call-ticker.service.ts:141, apps/core-api/src/modules/calling/jobs/call-ticker.service.ts:96, apps/core-api/src/modules/calling/jobs/call-ticker.service.ts:110, apps/core-api/src/config/env.validation.ts:559, apps/core-api/src/config/env.validation.ts:561, apps/core-api/src/modules/calling/dto/calling.dtos.ts:24

### BR-19

- rule: Friend call chỉ mở khi hai user follow nhau (cả hai chiều) và không block nhau, kiểm lại ở endpoint join; thất bại trả 404 CALLING_SESSION_NOT_FOUND (cùng mã với call không tồn tại).
- appliesTo: calling; friend; safety
- enforcedAt: service
- enforcedBy: CallingService.joinFriendCall và ProfileSocialService.canCall
- whatBreaksIfRemoved: Ai cũng gọi bền vững được cho người khác (spam); cờ UI bị tin thay vì kiểm tra ở endpoint join.
- evidence (evidence): apps/core-api/src/modules/calling/calling.service.ts:208, apps/core-api/src/modules/friend/services/profile-social.service.ts:156, apps/core-api/src/modules/friend/services/profile-social.service.ts:155, apps/core-api/src/modules/calling/calling.service.ts:211, docs/06-domain-rules.md:13

### BR-20

- rule: Voice Match chỉ tạo Friendship khi cả hai đã Yêu thích: lượt like của đối tác được đọc trong transaction và ensureFriendship nhận `manager` của transaction đó.
- appliesTo: calling; friend
- enforcedAt: service
- enforcedBy: CallingService (nhánh partnerLiked) và FriendService.ensureFriendship
- whatBreaksIfRemoved: Mutual-like không tạo Friendship (mất liên kết sau cuộc gọi) hoặc tạo khi chỉ một bên like (lộ danh tính ẩn danh).
- confidence: medium
- evidence (evidence): apps/core-api/src/modules/calling/calling.service.ts:399, apps/core-api/src/modules/calling/calling.service.ts:404, docs/06-domain-rules.md:111

### BR-21

- rule: Chat trực tiếp từ hồ sơ: mỗi ngày UTC 10 người đầu (PROFILE_DIRECT_MESSAGE_DAILY_FIRST_CHAT_THRESHOLD) mở chat miễn phí; từ người thứ 11 phải tặng quà (402 PROFILE_SOCIAL_MESSAGE_GIFT_REQUIRED). Conversation đã tồn tại thì không thu lại quà.
- appliesTo: friend; gift; economy
- enforcedAt: service
- enforcedBy: ProfileSocialService.openConversation (khoá hồ sơ đích trong transaction)
- whatBreaksIfRemoved: Hồ sơ nổi tiếng nhận tin nhắn miễn phí không giới hạn (mất cổng popularity) hoặc thu quà lặp ở các tin sau; nhiều người cùng bấm có thể vượt ngưỡng.
- evidence (evidence): apps/core-api/src/modules/friend/services/profile-social.service.ts:413, apps/core-api/src/modules/friend/services/profile-social.service.ts:431, apps/core-api/src/modules/friend/services/profile-social.service.ts:438, apps/core-api/src/modules/friend/services/profile-social.service.ts:442, apps/core-api/src/modules/friend/services/profile-social.service.ts:612, apps/core-api/src/config/env.validation.ts:516

### BR-22

- rule: Streak chỉ tăng khi cả hai chiều nhắn trong cùng ngày UTC (server clock); lỡ đúng 1 ngày được grace tự động, lỡ từ 2 ngày trở lên reset về 1; milestone mặc định 3,7,14,30,50,100 ngày.
- appliesTo: friend; notification
- enforcedAt: service
- enforcedBy: StreakService (khoá pessimistic_write trên conversation_streaks)
- whatBreaksIfRemoved: Streak tăng khi chỉ một bên nhắn hoặc theo múi giờ client (spoof); hai tin nhắn đồng thời đếm sai; mất grace làm reset sớm.
- evidence (evidence): apps/core-api/src/modules/friend/services/streak.service.ts:75, apps/core-api/src/modules/friend/services/streak.service.ts:84, apps/core-api/src/modules/friend/services/streak.service.ts:89, apps/core-api/src/modules/friend/services/streak.service.ts:68, apps/core-api/src/config/env.validation.ts:523, apps/core-api/src/common/date/utc-date.ts:2

### BR-23

- rule: Party Room: chỉ host được cấp/thu quyền speaker; số speaker tối đa là snapshot PARTY_MAX_SPEAKERS (mặc định 8) lúc tạo phòng, kiểm trong transaction (409 PARTY_SPEAKER_LIMIT_REACHED); phòng đầy theo PARTY_MAX_MEMBERS (mặc định 100).
- appliesTo: party-room
- enforcedAt: service
- enforcedBy: PartyRoomService (kiểm role host và speakerCount >= room.speakerLimit)
- whatBreaksIfRemoved: Audience tự cấp quyền; số speaker vượt mức SFU chịu được; đổi config làm thay đổi phòng đang chạy.
- evidence (evidence): apps/core-api/src/modules/party-room/party-room.service.ts:441, apps/core-api/src/modules/party-room/party-room.service.ts:444, apps/core-api/src/modules/party-room/party-room.service.ts:604, apps/core-api/src/modules/party-room/entities/party-room.entity.ts:38, apps/core-api/src/modules/party-room/party-room.service.ts:171, apps/core-api/src/config/env.validation.ts:567, apps/core-api/src/modules/party-room/party-room.errors.ts:7, apps/core-api/src/config/env.validation.ts:569

### BR-24

- rule: Report trừ trust score 5 điểm mỗi report (SAFETY_TRUST_PENALTY_PER_REPORT), tối đa 20 điểm mỗi ngày, sàn 0; cùng cặp reporter-target trong 7 ngày (SAFETY_REPORT_COOLDOWN_DAYS) vẫn được ghi report nhưng không trừ thêm điểm; mọi phép trừ được serialize bằng khoá pessimistic_write trên user đích.
- appliesTo: safety; user
- enforcedAt: service
- enforcedBy: SafetyService.report (transaction + khoá user đích)
- whatBreaksIfRemoved: Report spam trừ điểm một người vô hạn; hai report đồng thời vượt trần ngày; trust score xuống dưới sàn.
- evidence (evidence): apps/core-api/src/modules/safety/safety.service.ts:84, apps/core-api/src/modules/safety/safety.service.ts:50, apps/core-api/src/modules/safety/safety.service.ts:93, apps/core-api/src/modules/safety/safety.service.ts:76, apps/core-api/src/config/env.validation.ts:610, apps/core-api/src/config/env.validation.ts:612, apps/core-api/src/config/env.validation.ts:609

### BR-25

- rule: Video ngắn: mặc định VIDEO_MODERATION_MODE=pre đưa video sau transcode vào pending_review (mode post thì published); đủ VIDEO_REPORT_AUTOHIDE_THRESHOLD (5) reporter khác nhau thì tự chuyển published sang removed mà không đụng trust score; mọi transition là UPDATE có điều kiện status.
- appliesTo: short-video; safety
- enforcedAt: service
- enforcedBy: ShortVideoService (transition, autoHideIfPublished)
- whatBreaksIfRemoved: Video chưa duyệt lên public; báo cáo hàng loạt không tự ẩn; transition sai chiều khi hai request đua nhau.
- evidence (evidence): apps/core-api/src/modules/short-video/short-video.service.ts:147, apps/core-api/src/modules/short-video/short-video.service.ts:148, apps/core-api/src/modules/short-video/short-video.service.ts:500, apps/core-api/src/modules/short-video/short-video.service.ts:507, apps/core-api/src/modules/short-video/short-video.service.ts:534, apps/core-api/src/config/env.validation.ts:732, apps/core-api/src/config/env.validation.ts:742

### BR-26

- rule: Discovery (browse và nearby) loại trừ vĩnh viễn mọi cặp từng block hoặc report nhau theo bất kỳ chiều nào; không hết hạn, khác cooldown 30 ngày của matching.
- appliesTo: discovery; safety
- enforcedAt: service
- enforcedBy: SafetyService.getHiddenUserIds (DiscoveryService và NearbyService truyền vào excludeUserIds)
- whatBreaksIfRemoved: Hai người từng report/block nhau thấy nhau lại trong browse/nearby; reports append-only nên không có cách tự hết hạn.
- evidence (evidence): apps/core-api/src/modules/safety/safety.service.ts:267, apps/core-api/src/modules/safety/safety.service.ts:264, apps/core-api/src/modules/discovery/discovery.service.ts:49, apps/core-api/src/modules/discovery/nearby.service.ts:174, docs/06-domain-rules.md:46

### BR-27

- rule: Nearby chỉ hoạt động khi người dùng opt-in `nearbyVisible` (mặc định false); toạ độ được làm tròn theo DISCOVERY_LOCATION_QUANTIZE_DEGREES (0.0045 độ, khoảng 500 m) và cộng jitter tất định theo cặp-theo-ngày; `showDistance=false` trả distanceBucket null; hideProfile=true loại user khỏi nearby và browse ngay ở query server.
- appliesTo: discovery; user; feed
- enforcedAt: service
- enforcedBy: NearbyService (quantize, jitter, SQL hide_profile) và UserService.browsePage (lọc hide_profile cho Discovery browse)
- whatBreaksIfRemoved: Lộ vị trí chính xác qua trilateration; người chưa opt-in vẫn bị xem; hồ sơ ẩn vẫn hiện trong nearby/browse.
- evidence (evidence): apps/core-api/src/modules/discovery/entities/discovery-setting.entity.ts:5, apps/core-api/src/modules/discovery/nearby.service.ts:92, apps/core-api/src/config/env.validation.ts:680, apps/core-api/src/modules/discovery/nearby.constants.ts:28, apps/core-api/src/modules/discovery/nearby.service.ts:249, apps/core-api/src/modules/discovery/nearby.service.ts:320, apps/core-api/src/modules/user/user.service.ts:308, apps/core-api/src/modules/discovery/discovery.service.ts:63

### BR-28

- rule: Post.audience được enforce ở guard trung tâm getPostOrThrow: chủ bài xem mọi audience, `friends` cần quan hệ bạn bè, feed toàn cục chỉ hiện `public`; block, hồ sơ ẩn, sai audience hay không tồn tại đều trả cùng 404 FEED_POST_NOT_FOUND (oracle-safe).
- appliesTo: feed; safety; friend
- enforcedAt: service
- enforcedBy: FeedService.getPostOrThrow
- whatBreaksIfRemoved: Vào thẳng GET /posts/:id vượt audience; phân biệt được bài tồn tại nhưng bị chặn với bài không tồn tại.
- evidence (evidence): apps/core-api/src/modules/feed/feed.service.ts:216, apps/core-api/src/modules/feed/feed.service.ts:215, apps/core-api/src/modules/feed/feed.service.ts:209, apps/core-api/src/modules/feed/feed.service.ts:199, apps/core-api/src/modules/feed/feed.service.ts:108, docs/06-domain-rules.md:69

### BR-29

- rule: Admin không được tự ban chính mình; ban và audit log ghi atomic trong cùng transaction; quyền admin được kiểm ở backend (AdminPermissionGuard, 403 ADMIN_PERMISSION_FORBIDDEN), UI chỉ hỗ trợ UX; admin_audit_log append-only.
- appliesTo: admin
- enforcedAt: both
- enforcedBy: AdminService.banUser, AdminPermissionGuard, trigger append-only của admin_audit_log
- whatBreaksIfRemoved: Admin tự khoá mất quyền truy cập; quyền chỉ kiểm ở UI thì bị bypass bằng gọi API; audit log sửa/xoá được.
- evidence (evidence): apps/core-api/src/modules/admin/admin.service.ts:69, apps/core-api/src/modules/admin/admin.service.ts:65, apps/core-api/src/modules/admin/services/admin-permission.guard.ts:43, apps/core-api/src/database/migrations/1753700000000-admin-audit-log.ts:6, docs/02-domain-model.md:15

### BR-30

- rule: Block hai chiều được kiểm lại tại từng điểm quyết định (xem bài, nhắn tin, mở chat từ hồ sơ, friend call, ghép cặp/accept lời mời, discovery), không tin cờ UI hay kết quả cũ.
- appliesTo: safety; feed; friend; matching; discovery; gift; movie-match; palm-match; mood
- enforcedAt: service
- enforcedBy: SafetyService.isBlocked/canPair/getHiddenUserIds được các module gọi lại ở mỗi hành động
- whatBreaksIfRemoved: Block phát sinh sau khi cặp đã được ghép/mời vẫn cho nhắn, xem bài hoặc ghép; kiểm tra chỉ ở UI/cache thì bị bypass.
- evidence (evidence): apps/core-api/src/modules/feed/feed.service.ts:206, apps/core-api/src/modules/friend/services/profile-social.service.ts:164, apps/core-api/src/modules/friend/friend.service.ts:346, apps/core-api/src/modules/friend/services/profile-social.service.ts:155, apps/core-api/src/modules/matching/services/invite.service.ts:475, docs/02-domain-model.md:27
