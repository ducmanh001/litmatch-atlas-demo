# Lookup: database tables

id: lookup:tables

Table name to the file that documents columns, keys, indexes and who reads/writes.

| id | file | note |
|---|---|---|
| admin_audit_logs | ../db/common.md | Log hành động nhạy cảm của admin/moderator (docs/06 dòng 13, docs/05 dòng 96) — append-only tuyệt đối, DB tri… |
| admin_role_permissions | ../db/admin.md | Entity: AdminRolePermission (apps/core-api/src/modules/admin/entities/admin-role-permission.entity.ts) |
| auth_identities | ../db/auth.md | 1 cách đăng nhập của 1 user. unique(provider, providerUid) ở tầng DB — 2 request đăng ký song song cùng devic… |
| avatar_assets | ../db/avatar.md | Catalog item avatar — layer ghép hình (docs/services/avatar-service.md § 1). Giá là DATA trong DB (seed ở mig… |
| blocks | ../db/safety.md | Log block/unblock — append-only theo cặp (blocker, blocked). Trạng thái "đang block" hiện tại = dòng mới nhất… |
| call_sessions | ../db/calling.md | Phiên voice call 2 người trên LiveKit (docs/services/calling-service.md § 1). 1 voice MatchSession = tối đa 1… |
| comments | ../db/feed.md | Comment dưới 1 post — soft-delete, chỉ tác giả xoá được (feed-service.md § 2). |
| conversation_member_states | ../db/friend.md | Trạng thái CÁ NHÂN của 1 thành viên trong 1 conversation — mỗi bên một dòng, KHÔNG phải trạng thái chung của … |
| conversation_streaks | ../db/friend.md | Streak trò chuyện — 1:1 với `Conversation` (docs/services/streak-service.md). Tính on-write trong transaction… |
| conversations | ../db/friend.md | Chat 1-1 lâu dài giữa 2 user ĐÃ LÀ BẠN — khác chat ẩn danh tạm thời của Soul Match (docs/02). Cặp canonical `… |
| discovery_settings | ../db/discovery.md | Cài đặt hiển thị Nearby của 1 user — 1:1 với `users` (docs/services/discovery-service.md § Nearby). `nearbyVi… |
| friendships | ../db/friend.md | Quan hệ bạn bè 2 CHIỀU lưu đúng 1 dòng theo cặp canonical `userLowId < userHighId` (so sánh chuỗi uuid) — uni… |
| gift_events | ../db/gift.md | Log 1 lần tặng quà — ghi trong CÙNG DB transaction với 2 chân ledger (qua `EconomyService.sendGift` withinTra… |
| gifts | ../db/gift.md | Catalog quà (docs/services/gift-service.md § 2). Giá là DATA trong DB (đổi bằng UPDATE/admin) — server ĐỌC LẠ… |
| guest_match_quotas | ../db/matching.md | Entity: GuestMatchQuota (apps/core-api/src/modules/matching/entities/guest-match-quota.entity.ts) |
| iap_products | ../db/economy.md | Catalog: product id trên store → số diamond. Giá tiền thật nằm ở store, đây chỉ là mapping. |
| iap_receipts | ../db/economy.md | Receipt đã verify — nguồn đối soát với Apple/Google. Unique (provider, provider_transaction_id) chặn double-c… |
| image_assets | ../db/media.md | Entity: ImageAsset (apps/core-api/src/modules/media/entities/image-asset.entity.ts) |
| ledger_accounts | ../db/economy.md | Tài khoản nội bộ của sổ cái. Unique (kind, user_id, currency) enforce bằng partial index ở migration. |
| ledger_entries | ../db/economy.md | Bút toán Nợ/Có — NGUỒN SỰ THẬT DUY NHẤT của tiền (luật 2 AGENTS.md). Append-only tuyệt đối: DB trigger chặn U… |
| match_daily_quotas | ../db/matching.md | Quota miễn phí của user đã đăng ký theo ngày UTC và loại match. Guest vẫn dùng GuestMatchQuotaService với ba … |
| match_invites | ../db/matching.md | CTA "mời Voice/Soul Match" (W4, docs/services/matching-service.md § Invite) — directed invite, KHÔNG phải fri… |
| match_sessions | ../db/matching.md | Flow mới tạo session đã confirmed ngay khi matcher tìm được cặp. `PendingConfirm` được giữ để sweeper/confirm… |
| match_tickets | ../db/matching.md | 1 yêu cầu ghép cặp (docs/services/matching-service.md § 5). - Nguồn sự thật về trạng thái là Postgres — Redis… |
| messages | ../db/friend.md | Message chat 1-1 lâu dài — APPEND-ONLY (không updatedAt, không extend BaseAppEntity), KHÔNG ẩn danh (2 bên đã… |
| mini_game_active_participants | ../db/mini-game.md | Bảng phụ trợ THUẦN để enforce bất biến "1 user chỉ có 1 MiniGameSession `waiting_moves` tại 1 thời điểm" — PK… |
| mini_game_sessions | ../db/mini-game.md | Ván oẳn tù tì 2 người ĐÃ LÀ BẠN (docs/services/mini-game-service.md). Cặp canonical `userLowId < userHighId` … |
| mood_presets | ../db/mood.md | Catalog mood preset — data-driven (seed migration, pattern `avatar_assets`); danh sách hiển thị được cho user… |
| mood_status_events | ../db/mood.md | Log APPEND-ONLY set/clear mood — không update/xoá dòng cũ. "Mood hiện tại" của 1 user = dòng mới nhất (ORDER … |
| movie_match_queue_entries | ../db/movie-match.md | Hàng chờ ghép ẩn danh — PK userId: 1 user chỉ queue 1 lần (cùng kỹ thuật Palm Match). |
| movie_session_active_participants | ../db/movie-match.md | Bảng phụ trợ THUẦN để enforce bất biến "1 user chỉ có 1 MovieSession active tại 1 thời điểm" — PK trên `userI… |
| movie_session_messages | ../db/movie-match.md | Chat trong phiên ẩn danh — APPEND-ONLY, sống theo session (khác Friend Chat: 2 người CHƯA là bạn nên không có… |
| movie_sessions | ../db/movie-match.md | Phiên xem chung 2 người ĐÃ LÀ BẠN (docs/services/movie-match-service.md). Cặp canonical `userLowId < userHigh… |
| notifications | ../db/notification.md | In-app notification (docs/services/notification-service.md). `payload` chỉ chứa dữ liệu tối thiểu để client t… |
| outbox_events | ../db/economy.md | Outbox Pattern (docs/03 § 3.6): event ghi cùng DB transaction với nghiệp vụ, relay publish Kafka sau. |
| palm_match_active_participants | ../db/palm-match.md | Chốt DB cho bất biến một user chỉ trỏ tới một Palm session chưa dismiss. Hai partial unique index trên low/hi… |
| palm_match_queue_entries | ../db/palm-match.md | Hàng đợi idempotent: PK theo user ngăn hai tab enqueue hai lần. |
| palm_match_sessions | ../db/palm-match.md | Snapshot của một lượt Palm Match. Sign, phần trăm và fortune được chốt một lần ở server để reload/retry không… |
| palm_reading_templates | ../db/palm-match.md | Catalog nội dung bói toán giải trí (docs/services/palm-match-service.md § 2) — KHÔNG có bảng lịch sử, kết quả… |
| party_room_comments | ../db/party-room.md | Bình luận realtime trong Party Room — append-only để retry/poll có thứ tự ổn định và giữ bằng chứng cho Trust… |
| party_room_members | ../db/party-room.md | Membership của 1 user trong 1 phòng. Active = `leftAt IS NULL`; rejoin sau khi rời tạo ROW MỚI (giữ lịch sử).… |
| party_rooms | ../db/party-room.md | Phòng party multi-user trên LiveKit (docs/services/party-room-service.md). `speakerLimit` là SNAPSHOT config … |
| payos_packages | ../db/economy.md | Catalog nạp web Việt Nam. Giá/DIA chỉ được đọc từ đây, không nhận từ client. |
| payos_payment_orders | ../db/economy.md | Intent payOS tách khỏi ledger: tạo link chưa mint DIA; chỉ webhook verified mới ghi sổ. amount/diamonds/packa… |
| phone_otps | ../db/auth.md | OTP lưu dạng HMAC-SHA256(code, pepper) — attempt_count enforce ở server, không tin client. |
| posts | ../db/feed.md | Bài viết feed công khai toàn cục (docs/services/feed-service.md § 1) — không fanout, query thẳng theo `seq` g… |
| profile_chat_contacts | ../db/friend.md | Lần đầu một user mở chat trực tiếp với profile. Một cặp chỉ được ghi một lần; ngày này là ngày UTC để đếm ngư… |
| profile_follows | ../db/friend.md | Quan hệ theo dõi một chiều trên profile, độc lập với popularity gate của chat. |
| reactions | ../db/feed.md | Reaction "thả tim" (docs/01 #6) — 1 loại duy nhất, không phải bảng đa loại reaction. Nguồn sự thật ai đã like… |
| refresh_tokens | ../db/auth.md | Refresh token lưu dạng SHA-256 hash (không bao giờ lưu plaintext). `familyId` nhóm các token sinh ra từ cùng … |
| reports | ../db/safety.md | Log report — append-only (docs/06: hành động nhạy cảm phải log audit riêng, không xoá được). `trustPenaltyApp… |
| soul_chat_messages | ../db/soul-match.md | Message của chat ẩn danh tạm thời — APPEND-ONLY (không updatedAt, không extend BaseAppEntity): phòng đóng thì… |
| soul_match_ratings | ../db/soul-match.md | 1 người 1 verdict cho 1 session — IMMUTABLE (đổi ý = mở đường thăm dò "like thử xem bên kia like chưa rồi đổi… |
| stories | ../db/feed.md | Story ephemeral (docs/services/feed-service.md § 8) — KHÁC `Post` (không soft-delete/audit): hết hạn = filter… |
| story_views | ../db/feed.md | Seen-state — unique (storyId, viewerId), chỉ tác giả truy vấn được danh sách (docs/services/ feed-service.md … |
| support_tickets | ../db/support.md | Entity: SupportTicket (apps/core-api/src/modules/support/entities/support-ticket.entity.ts) |
| transactions | ../db/economy.md | Metadata 1 giao dịch nghiệp vụ — KHÔNG chứa số tiền (số tiền nằm ở ledger_entries, tránh 2 nguồn sự thật — do… |
| user_avatar_configs | ../db/avatar.md | Item đang trang bị mỗi slot (docs/services/avatar-service.md § 1) — 1 dòng/user, PK = userId (không dùng `Bas… |
| user_avatar_items | ../db/avatar.md | Sở hữu item avatar — nguồn sự thật "user có item này chưa" (docs/services/avatar-service.md § 1), tách khỏi `… |
| user_locations | ../db/discovery.md | Vị trí Nearby của 1 user — 1:1 với `users` (docs/services/discovery-service.md § Nearby). Toạ độ đã QUANTIZE … |
| user_privacy_settings | ../db/user.md | Privacy preference của chính user — không compose vào PublicProfileDto. |
| users | ../db/user.md | Entity: User (apps/core-api/src/modules/user/entities/user.entity.ts) |
| video_comments | ../db/short-video.md | Comment dưới 1 video — sở hữu riêng bởi `short-video` (KHÔNG share bảng `comments` của Feed dù cùng shape). |
| video_reactions | ../db/short-video.md | Reaction "thả tim" cho video — 1 loại duy nhất, toggle idempotent, sở hữu riêng bởi `short-video`. |
| video_views | ../db/short-video.md | View 1 video (docs/services/short-video-service.md § 3) — unique (video, viewer) chống đếm đôi, cùng pattern … |
| videos | ../db/short-video.md | Video ngắn (docs/services/short-video-service.md) — V1 hướng Momo, KHÔNG phải feed toàn cục cạnh tranh TikTok… |
| vip_plans | ../db/economy.md | Gói VIP mua bằng diamond — giá/thời hạn ở DB, không hardcode (docs/05 § 5.1). |
| voice_match_reactions | ../db/calling.md | Một lượt yêu thích immutable sau Voice Match; unique DB là chốt chống double tap/retry. |
| wallets | ../db/economy.md | SNAPSHOT dẫn xuất từ ledger — KHÔNG phải nguồn sự thật (luật 2 AGENTS.md). Chỉ LedgerService được ghi, trong … |
| web_push_subscriptions | ../db/notification.md | Một endpoint browser/user — endpoint được coi là credential, không log ra ngoài adapter. |
