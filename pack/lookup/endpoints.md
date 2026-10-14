# Lookup: REST endpoints

id: lookup:endpoints

METHOD /path (without /api/v1) to the file that documents it.

| id | file | note |
|---|---|---|
| DELETE /feed/comments/{commentId} | ../api/feed.md | Xoá mềm comment, chỉ tác giả comment; chỉ request chuyển deletedAt NULL -> timestamp mới giảm commentCount (c… |
| DELETE /feed/posts/{postId} | ../api/feed.md | Xoá mềm bài (deletedAt), chỉ tác giả; đi qua getPostOrThrow nên cũng áp block/audience. |
| DELETE /feed/posts/{postId}/reactions | ../api/feed.md | Bỏ thả tim idempotent: xoá Reaction và chỉ giảm likeCount khi thực sự xoá được dòng. |
| DELETE /matching/tickets/{id} | ../api/matching.md | Huỷ ticket của chính mình, chỉ khi còn queued; đổi trạng thái trong DB rồi ZREM khỏi shard Redis. |
| DELETE /mood/status | ../api/mood.md | Tắt mood bằng cách ghi dòng kind='clear' (không xoá dòng cũ), bắt buộc Idempotency-Key, trả 204. |
| DELETE /movie-match/anon/current | ../api/movie-match.md | Huỷ hàng đợi hoặc dismiss kết quả/phiên ẩn danh: phiên đang active bị chốt outcome=cancelled cho CẢ HAI và ca… |
| DELETE /notifications/web-push/subscription | ../api/notification.md | Huỷ subscription theo endpoint, chỉ xoá hàng thuộc chính user (delete theo userId+endpoint). |
| DELETE /palm-match/current | ../api/palm-match.md | Huỷ queue, huỷ phiên đang active cho cả hai hoặc dismiss kết quả terminal để giải phóng slot enqueue lượt mới. |
| DELETE /profiles/{profileUserId}/follow | ../api/friend.md | Unfollow chỉ đặt active=false (giữ lịch sử last_followed_at); cùng guard tự thao tác và block như follow. |
| DELETE /safety/blocks/{targetUserId} | ../api/safety.md | Unblock target (idempotent): chỉ ghi dòng action=unblocked khi dòng mới nhất đang là blocked; trả 204. |
| DELETE /videos/{id}/comments/{commentId} | ../api/short-video.md | Xoá mềm bình luận (set deletedAt): chỉ tác giả comment hoặc tác giả video được xoá, người khác 403, comment đ… |
| DELETE /videos/{id}/reactions | ../api/short-video.md | Bỏ tim idempotent: xoá VideoReaction và chỉ giảm likeCount khi xoá được dòng. |
| GET /admin/config/economy-catalog | ../api/admin.md | Xem catalog kinh tế: gói IAP diamond và gói VIP kèm trạng thái active. Cần manage_config. |
| GET /admin/dashboard | ../api/admin.md | Dashboard tổng hợp: thống kê user, doanh số diamond 7 ngày, số phòng đang mở và 10 hoạt động audit gần nhất. … |
| GET /admin/economy/users/{userId}/transactions | ../api/admin.md | Lịch sử giao dịch của user, phân trang cursor, actor-scoped. Cần refund_transaction. |
| GET /admin/economy/wallet/{userId} | ../api/admin.md | Xem ví (balance snapshot + VIP) của một user, chỉ đọc. Cần refund_transaction. |
| GET /admin/gifts | ../api/admin.md | Xem toàn bộ catalog quà kể cả quà đã tắt. Cần manage_gifts. |
| GET /admin/permissions | ../api/admin.md | Đọc ma trận quyền role (moderator/admin) x permission từ admin_role_permissions. Cần manage_permissions. |
| GET /admin/reports | ../api/admin.md | Danh sách báo cáo vi phạm (report) lọc và phân trang offset. Cần resolve_reports. |
| GET /admin/rooms | ../api/admin.md | Danh sách party room kèm số member, phân trang cursor. Cần manage_rooms. |
| GET /admin/staff | ../api/admin.md | Danh sách nhân sự (user có role admin/moderator). Cần manage_permissions. |
| GET /admin/support/tickets | ../api/admin.md | Danh sách ticket hỗ trợ của mọi user, lọc status, phân trang cursor. Cần manage_support. |
| GET /admin/users | ../api/admin.md | Danh sách user lọc theo status/role/nickname, phân trang offset. Cần view_users. |
| GET /admin/users/{id} | ../api/admin.md | Xem hồ sơ nội bộ của một user (status + role). Cần view_users. |
| GET /admin/videos/pending | ../api/admin.md | Danh sách video chờ duyệt (chế độ VIDEO_MODERATION_MODE=pre). Cần resolve_reports. |
| GET /admin/videos/published | ../api/admin.md | Danh sách video đang published (mới nhất trước) để admin gỡ thủ công. Cần resolve_reports. |
| GET /auth/search-by-phone | ../api/auth.md | Tìm hồ sơ công khai theo số điện thoại chỉ khi chủ hồ sơ bật cho phép; trả null cả khi không tồn tại lẫn khi … |
| GET /avatar/catalog | ../api/avatar.md | Liệt kê catalog item đang bật, lọc theo slot tuỳ chọn, sắp theo sortOrder/code; giá do server quyết định. Cần… |
| GET /avatar/me | ../api/avatar.md | Avatar của chính mình: config + danh sách layer đầy đủ để client ghép theo zIndex; lazy-init config mặc định … |
| GET /avatar/me/items | ../api/avatar.md | Liệt kê các item avatar user hiện tại đã sở hữu (join user_avatar_items với avatar_assets). |
| GET /avatar/users/{userId} | ../api/avatar.md | Xem avatar người khác: chỉ đọc, không cần là bạn bè (avatar là thông tin hiển thị công khai), chưa có config … |
| GET /calling/calls/{id} | ../api/calling.md | Poll fallback của call.ended: trả trạng thái call + liked/matched/friendUserId, chỉ member (404 gộp chống ora… |
| GET /capabilities | ../api/app-shell.md | Trả CapabilitiesDto (auth.phoneOtp/google/apple/facebook/guest, topUp.web/native, video, notifications.push) … |
| GET /conversations/{id}/messages | ../api/friend.md | Liệt kê message theo cursor keyset seq; chỉ 2 thành viên, conversation không tồn tại hoặc không phải thành vi… |
| GET /conversations/{id}/streak | ../api/friend.md | Streak hiện tại derive khi đọc (isActive khi gap <= 2 ngày UTC, chưa có streak trả current=0); chỉ thành viên. |
| GET /discovery/browse | ../api/discovery.md | Duyệt user theo gender/tuổi (cursor createdAt,id); loại self + block/report 2 chiều + hide_profile + guest/ba… |
| GET /discovery/nearby | ../api/discovery.md | Liệt kê người gần: yêu cầu caller đã opt-in và vị trí còn tươi (403/409 nếu không), rate limit truy vấn, chỉ … |
| GET /economy/iap/products | ../api/economy.md | Catalog gói diamond IAP đang bán (active=true) sắp theo diamonds tăng dần để client chọn productId trước khi … |
| GET /economy/payos/orders/{orderId} | ../api/economy.md | Trạng thái đơn payOS của chính user, nguồn sự thật phía server (returnUrl của browser không phải bằng chứng);… |
| GET /economy/payos/packages | ../api/economy.md | Catalog gói nạp payOS (packageId, amountVnd, diamonds) đang bán; client chỉ gửi packageId, giá luôn do server… |
| GET /economy/transactions | ../api/economy.md | Lịch sử giao dịch của chính user, cursor pagination (chưa gồm giao dịch chỉ là người nhận). |
| GET /economy/vip/plans | ../api/economy.md | Catalog gói VIP đang bán (planId, tier, số ngày, giá diamond) sắp theo giá tăng dần; client không hardcode bả… |
| GET /economy/wallet | ../api/economy.md | Đọc ví của chính user: balance, earnings, tier VIP đã derive hết hạn khi đọc; chưa có ví thì trả 0. |
| GET /feed/posts | ../api/feed.md | Feed công khai toàn cục (chỉ audience=public), cursor seq giảm dần, loại tác giả đang block/bị block. |
| GET /feed/posts/{postId} | ../api/feed.md | Chi tiết 1 bài qua getPostOrThrow: đã xoá, bị block, tác giả ẩn hồ sơ hay sai audience đều 404 POST_NOT_FOUND… |
| GET /feed/posts/{postId}/comments | ../api/feed.md | Liệt kê comment cũ -> mới theo cursor seq sau khi qua getPostOrThrow, kèm batch author PublicProfileDto. |
| GET /feed/posts/{postId}/reactions | ../api/feed.md | Trạng thái thả tim của chính mình + tổng likeCount, qua getPostOrThrow. |
| GET /feed/users/{userId}/posts | ../api/feed.md | Timeline một tác giả: audience khớp quan hệ (chính mình thấy hết, bạn thấy public+friends, người lạ chỉ publi… |
| GET /friends | ../api/friend.md | Inbox mọi conversation của caller sort theo lastMessageAt, kèm profile công khai, unreadCount, lastMessagePre… |
| GET /friends/connections | ../api/friend.md | Danh sách bạn bè = Friendship (cùng "Thích" lúc ghép đôi) hợp follow active hai chiều, gộp một dòng mỗi người… |
| GET /friends/{friendUserId}/conversation | ../api/friend.md | Tra conversation của một cặp theo cặp canonical; chưa có thì 404 NOT_FRIEND (không phân biệt user không tồn t… |
| GET /gifts | ../api/gift.md | Liệt kê catalog quà đang bật (active) với giá server là nguồn sự thật; client map code sang asset/animation. … |
| GET /health | ../api/app-shell.md | Liveness tương thích ngược, trả status ok + uptimeSeconds; không kiểm tra Redis. |
| GET /health/live | ../api/app-shell.md | Liveness probe của K8s: chỉ cho biết process còn sống, không phụ thuộc Redis. |
| GET /health/ready | ../api/app-shell.md | Readiness probe: 503 nếu PSUBSCRIBE realtime:user:* chưa sẵn sàng, quota Redis chưa ready hoặc Redis cluster … |
| GET /matching/invites | ../api/matching.md | Inbox lời mời ĐANG pending gửi tới mình (cursor); re-check hidden-set Safety mỗi lần đọc và batch-load profil… |
| GET /matching/invites/{id} | ../api/matching.md | Chi tiết một lời mời, chỉ inviter hoặc invitee xem được (người khác 403) kèm inviterProfile. |
| GET /matching/tickets/current | ../api/matching.md | Phục hồi UI sau reload: trả ticket queued/matched của chính user (hoặc null) kèm giá speedup theo tier VIP. |
| GET /matching/tickets/{id} | ../api/matching.md | Poll trạng thái ticket, chỉ chủ ticket xem được (chống IDOR); đây là fallback khi socket match.confirmed bị l… |
| GET /mini-game/sessions/{id} | ../api/mini-game.md | Đọc ván (chỉ participant, 404 gộp nếu không tồn tại/không phải thành viên); là fallback polling khi realtime … |
| GET /mood/presets | ../api/mood.md | Liệt kê preset mood đang active (sortOrder, code) để client dựng UI chọn; chỉ cần JWT. |
| GET /mood/status/me | ../api/mood.md | Mood hiện tại của chính mình, suy ra từ dòng event mới nhất; không qua kiểm tra block. |
| GET /mood/status/{userId} | ../api/mood.md | Mood công khai của userId khác theo góc nhìn người xem; trả rỗng nếu giữa hai người có block active 2 chiều (… |
| GET /movie-match/anon/current | ../api/movie-match.md | Trả state phục hồi/poll của flow ẩn danh (idle\|queued\|watching\|rating\|completed) theo vai trò tương đối, khôn… |
| GET /movie-match/anon/sessions/{id}/messages | ../api/movie-match.md | Liệt kê chat của phiên ẩn danh theo cursor seq; chỉ participant đọc được, sender trả vai trò tương đối me\|par… |
| GET /movie-match/sessions/{id} | ../api/movie-match.md | Poll fallback cho realtime: trả MovieSessionDto (kèm partnerUserId) cho participant; không tồn tại hoặc không… |
| GET /notifications | ../api/notification.md | Liệt kê notification của chính user, mới nhất trước, phân trang cursor theo seq (cursor sai trả 400 CURSOR_IN… |
| GET /notifications/unread-count | ../api/notification.md | Đếm notification chưa đọc (readAt IS NULL) cho badge chuông; web poll mỗi 60s. |
| GET /palm-match/current | ../api/palm-match.md | State phục hồi/poll của queue hoặc phiên hiện tại, đồng thời lazy-expire hạn queue/phiên; là kênh DUY NHẤT để… |
| GET /palm-match/reading | ../api/palm-match.md | Trả nội dung bói giải trí {category, content, forDate}, deterministic theo hash(userId, category, ngày UTC); … |
| GET /party/rooms | ../api/party-room.md | Liệt kê phòng active kèm số member, phân trang cursor, lọc q/category; chỉ đọc DB. |
| GET /party/rooms/{id} | ../api/party-room.md | Chi tiết phòng + member active, nickname lấy từ User (findByIds); là nguồn sự thật mà client poll fallback để… |
| GET /party/rooms/{id}/comments | ../api/party-room.md | Lấy comment mới nhất theo cursor (đi lùi); chỉ member của phòng mới đọc được. |
| GET /profiles/{profileUserId}/actions | ../api/friend.md | Trả isFollowing, follower/following count, conversationId hiện có và requiresGift (số ProfileChatContact hôm … |
| GET /profiles/{profileUserId}/follow-counts | ../api/friend.md | Trả followerCount/followingCount của một profile, dùng được cho cả chính mình (khác /actions từ chối self); 4… |
| GET /profiles/{profileUserId}/followers | ../api/friend.md | Danh sách người theo dõi profile, cursor keyset (last_followed_at, id) mới theo dõi nhất trước, kèm isFollowi… |
| GET /profiles/{profileUserId}/following | ../api/friend.md | Danh sách profile mà user đang theo dõi, cùng cursor keyset và cùng bộ lọc block/active như followers. |
| GET /safety/blocks/{targetUserId} | ../api/safety.md | Cho caller biết MÌNH có đang block target hay không (một chiều, không lộ việc bị block ngược). |
| GET /soul-match/sessions/{id} | ../api/soul-match.md | Poll trạng thái phòng chat ẩn danh (phase chatting\|rating\|closed, chatEndsAt/ratingEndsAt, verdict CỦA MÌNH, … |
| GET /soul-match/sessions/{id}/messages | ../api/soul-match.md | Liệt kê message ẩn danh theo cursor seq, chỉ trả senderRole me\|partner (không lộ userId đối phương); phòng đã… |
| GET /soul-match/sessions/{id}/partner | ../api/soul-match.md | Trả PublicProfileDto của đối phương chỉ khi FriendService.areFriends=true; chưa match trả 403 SOUL_MATCH_PART… |
| GET /stories/ring | ../api/feed.md | Ring story còn hạn của mình + bạn bè (listFriendIds), loại tác giả block 2 chiều và tác giả ẩn hồ sơ. |
| GET /stories/{storyId} | ../api/feed.md | Xem 1 story và ghi StoryView (self-view không đếm, xem lại idempotent); block/hidden/sai audience đều trả 404… |
| GET /stories/{storyId}/viewers | ../api/feed.md | Danh sách người xem story, CHỈ tác giả truy vấn được; lọc block hiện tại lúc đọc. |
| GET /support/tickets/me | ../api/support.md | Liệt kê phiếu của chính mình theo cursor (createdAt, id) giảm dần để theo dõi trạng thái và staffResponse. |
| GET /users/me | ../api/user.md | Đọc hồ sơ đầy đủ của chính mình (MyProfileDto) qua UserService.getByIdOrThrow. |
| GET /users/me/privacy | ../api/user.md | Đọc privacy settings của mình; chưa có row thì trả DEFAULT_PRIVACY_SETTINGS. |
| GET /users/{id} | ../api/user.md | Hồ sơ công khai tối thiểu (PublicProfileDto) của user khác theo id; không kiểm hideProfile/ban (xem notes). |
| GET /users/{id}/presence | ../api/user.md | Cho biết user khác có đang online không: chỉ true khi họ bật showOnlineStatus VÀ Redis presence còn lease; kh… |
| GET /videos | ../api/short-video.md | Danh sách video published (for_you hoặc following = video của bạn bè qua listFriendIds; sort recent\|ranked) k… |
| GET /videos/{id} | ../api/short-video.md | Chi tiết 1 video qua getVideoOrThrow: chỉ tác giả hoặc video published, còn lại 404 VIDEO_NOT_FOUND. |
| GET /videos/{id}/comments | ../api/short-video.md | Liệt kê comment theo cursor sau khi qua getVideoOrThrow, batch author PublicProfileDto. |
| PATCH /admin/config/iap-products/{productId} | ../api/admin.md | Bật/tắt một IAP product + audit economy.iap_product.updated cùng transaction. Cần manage_config. |
| PATCH /admin/config/vip-plans/{id} | ../api/admin.md | Bật/tắt một gói VIP + audit economy.vip_plan.updated cùng transaction. Cần manage_config. |
| PATCH /admin/gifts/{id} | ../api/admin.md | Sửa giá/tên/thứ tự/bật-tắt quà (không hard-delete) + audit gift.updated cùng transaction. Cần manage_gifts. |
| PATCH /admin/permissions/{role}/{permission} | ../api/admin.md | Bật/tắt một permission cho role admin/moderator + audit; không cho tắt manage_permissions của admin (tránh tự… |
| PATCH /admin/staff/{id}/role | ../api/admin.md | Đổi role nhân sự với khoá pessimistic để không hạ admin cuối cùng; chặn tự đổi role chính mình; audit cùng tr… |
| PATCH /admin/support/tickets/{id} | ../api/admin.md | Đổi trạng thái ticket (kiểm canTransition) và staffResponse + audit support.ticket.updated cùng transaction. … |
| PATCH /movie-match/anon/sessions/{id}/state | ../api/movie-match.md | Cập nhật playback phiên ẩn danh (last-write-wins) chỉ khi còn trong phần xem (409 sau khi finish/hết giờ), kh… |
| PATCH /movie-match/sessions/{id}/state | ../api/movie-match.md | Participant cập nhật positionSeconds/isPlaying của phiên bạn bè (last-write-wins, không lock, không Idempoten… |
| PATCH /users/me | ../api/user.md | Sửa hồ sơ của mình (nickname, gender, birthDate không ở tương lai, region, interests, seeking*); kiểm khoảng … |
| POST /admin/economy/transactions/{id}/refund | ../api/admin.md | Hoàn tiền thủ công một giao dịch bằng bút toán đảo (ledger.reverse), bắt buộc có reason, audit cùng transacti… |
| POST /admin/gifts | ../api/admin.md | Tạo quà mới (code unique uq_gifts_code) + audit gift.created cùng transaction. Cần manage_gifts. |
| POST /admin/notifications/broadcast | ../api/admin.md | Gửi thông báo broadcast tới nhóm all/vip/non-vip: lưu in-app + audit trong một transaction, rồi push best-eff… |
| POST /admin/reports/{id}/dismiss | ../api/admin.md | Bác bỏ report (dismissed) + audit cùng transaction. Cần resolve_reports. |
| POST /admin/reports/{id}/resolve | ../api/admin.md | Đánh dấu report là resolved + audit report.resolved cùng transaction. Cần resolve_reports. |
| POST /admin/rooms/{id}/close | ../api/admin.md | Đóng cưỡng bức party room (reason AdminClosed) với audit trong cùng transaction đóng phòng; dọn LiveKit/realt… |
| POST /admin/users/{id}/ban | ../api/admin.md | Khoá tài khoản user và ghi audit user.banned trong cùng transaction; chặn tự ban chính mình (CANNOT_BAN_SELF)… |
| POST /admin/users/{id}/unban | ../api/admin.md | Mở khoá tài khoản user và ghi audit user.unbanned cùng transaction. Cần ban_users. |
| POST /admin/videos/{id}/approve | ../api/admin.md | Duyệt video pending_review -> published + audit video.approved cùng transaction. Cần resolve_reports. |
| POST /admin/videos/{id}/reject | ../api/admin.md | Từ chối video pending_review -> rejected + audit video.rejected cùng transaction. Cần resolve_reports. |
| POST /admin/videos/{id}/remove | ../api/admin.md | Gỡ thủ công video published -> removed + audit video.removed, bổ sung cho auto-hide theo ngưỡng report. Cần r… |
| POST /auth/guest | ../api/auth.md | Đăng nhập khách bằng deviceId: find-or-create user guest + identity trong một transaction, trả access token, … |
| POST /auth/logout | ../api/auth.md | Thu hồi refresh token hiện tại (idempotent) và xoá cookie. Cần CsrfGuard; trả 204. |
| POST /auth/otp/request | ../api/auth.md | Tạo OTP 6 số cho số điện thoại (rate limit theo số trong DB, vô hiệu mã cũ) và trả mã trong response vì không… |
| POST /auth/otp/verify | ../api/auth.md | Xác minh OTP (đếm lần thử và consume bằng UPDATE có điều kiện) rồi đăng nhập hoặc tạo user mới theo identity … |
| POST /auth/refresh | ../api/auth.md | Xoay refresh token từ cookie httpOnly (rotation, phát hiện reuse thu hồi cả family), kiểm lại user chưa bị ba… |
| POST /auth/social | ../api/auth.md | Đăng nhập Google/Apple (ID token JWKS) hoặc Facebook (debug_token) sau khi server tự verify, rồi find-or-crea… |
| POST /auth/upgrade/otp | ../api/auth.md | Nâng cấp tài khoản guest đang đăng nhập bằng OTP điện thoại: gắn identity, xoá identity guest, đặt isGuest=fa… |
| POST /auth/upgrade/social | ../api/auth.md | Nâng cấp guest bằng social ID token: cùng luồng linkIdentityAndUpgrade như OTP, 409 nếu identity đã thuộc tài… |
| POST /avatar/items/{assetId}/buy | ../api/avatar.md | Mua item trả phí: bắt buộc Idempotency-Key, Economy.spendDiamond trừ DIA (TransactionType.AvatarPurchase) rồi… |
| POST /avatar/items/{assetId}/claim | ../api/avatar.md | Nhận item miễn phí (priceDiamond = 0): ghi sở hữu idempotent ON CONFLICT DO NOTHING, không đi qua Economy; it… |
| POST /calling/calls/{id}/end | ../api/calling.md | Member chủ động kết thúc call, idempotent (lock FOR UPDATE), chốt luôn MatchSession voice ở Matching, dọn roo… |
| POST /calling/calls/{id}/like | ../api/calling.md | Ghi consent like immutable khi call active/ended (khoá CallSession, unique reaction); đủ like hai chiều thì t… |
| POST /calling/friends/{friendUserId}/join | ../api/calling.md | Mở/lấy friend call lâu dài (canonical pair, không MatchSession, không free timer) chỉ khi hai bên follow nhau… |
| POST /calling/match-sessions/{matchSessionId}/end | ../api/calling.md | Rời màn Voice Match: end call nếu đã có, rồi chốt MatchSession ended kể cả khi CallSession chưa được tạo. |
| POST /calling/match-sessions/{matchSessionId}/join | ../api/calling.md | Member của voice MatchSession đã confirmed tạo/lấy CallSession (unique match_session_id) và nhận token LiveKi… |
| POST /calling/webhooks/livekit | ../api/calling.md | Nhận webhook LiveKit (participant_joined/left/connection_aborted, room_finished): verify chữ ký JWT trên raw … |
| POST /conversations/{id}/messages | ../api/friend.md | Gửi message: bắt buộc Idempotency-Key, guard thành viên + block 2 chiều (404 gộp), resolve imageAssetId qua M… |
| POST /conversations/{id}/mute | ../api/friend.md | Bật/tắt mute hội thoại (body {muted}); chỉ tắt kênh notification friend_message, message/realtime/unread vẫn … |
| POST /conversations/{id}/read | ../api/friend.md | Đánh dấu đã đọc tới now() bằng upsert ON CONFLICT trên conversation_member_states; idempotent, chỉ thành viên. |
| POST /economy/iap/verify | ../api/economy.md | Verify receipt IAP Apple/Google ở server rồi credit diamond; idempotency tự sinh iap:{provider}:{providerTran… |
| POST /economy/payos/orders | ../api/economy.md | Tạo đơn nạp payOS idempotent (header Idempotency-Key, @Throttle 10/phút, guest bị 403): snapshot giá/diamond … |
| POST /economy/vip/purchase | ../api/economy.md | Mua VIP bằng diamond (header Idempotency-Key): Nợ user_wallet / Có system_revenue và cập nhật vip_tier/vip_ex… |
| POST /economy/webhooks/apple | ../api/economy.md | Webhook App Store Server Notifications V2: verify JWS x5c lên Apple Root CA, đối chiếu bundleId, nếu REFUND/R… |
| POST /economy/webhooks/google/rtdn | ../api/economy.md | Webhook Google RTDN (Pub/Sub push): verify OIDC bearer token rồi chỉ log CANCELED làm tín hiệu phụ, KHÔNG ref… |
| POST /economy/webhooks/payos | ../api/economy.md | Webhook payOS: kiểm HMAC-SHA256 trước mọi query, đối chiếu orderCode/amount/currency/paymentLinkId với snapsh… |
| POST /feed/posts | ../api/feed.md | Đăng bài: Idempotency-Key (unique DB, khác nội dung 409), guest bị chặn, ảnh chỉ qua imageAssetId được Media … |
| POST /feed/posts/{postId}/comments | ../api/feed.md | Bình luận: guest bị chặn, guard block/audience, insert Comment + tăng commentCount + tạo Notification trong c… |
| POST /feed/posts/{postId}/reactions | ../api/feed.md | Thả tim idempotent: insert Reaction + tăng likeCount + tạo Notification post_liked cùng transaction; thua rac… |
| POST /matching/invites | ../api/matching.md | Tạo lời mời Voice/Soul tới invitee: chặn tự mời, rate limit Redis theo giờ, từ chối kín (oracle-safe) nếu inv… |
| POST /matching/invites/{id}/accept | ../api/matching.md | Invitee chấp nhận: transaction riêng precheck lazy-expire + canPair (fail thì invite thành declined), rồi 1 t… |
| POST /matching/invites/{id}/cancel | ../api/matching.md | Inviter huỷ lời mời đã gửi trước khi có phản hồi (chỉ inviter). |
| POST /matching/invites/{id}/decline | ../api/matching.md | Invitee từ chối lời mời pending (chỉ invitee, lazy-expire theo expiresAt tại thời điểm hành động). |
| POST /matching/tickets | ../api/matching.md | Vào hàng đợi: Throttle 10 req/phút, bắt buộc Idempotency-Key, 1 transaction khoá user + trừ quota ngày (guest… |
| POST /matching/tickets/{id}/confirm | ../api/matching.md | Đường tương thích cho session pending_confirm cũ: khoá session trước ticket, đủ 2 bên confirm thì chuyển cả h… |
| POST /matching/tickets/{id}/speedup | ../api/matching.md | Mua ưu tiên hàng đợi: chỉ khi ticket queued, trừ DIA qua Economy (key speedup:{user}:{key}, replay không trừ … |
| POST /media/images/upload-intent | ../api/media.md | Xin presigned PUT URL + assetId (purpose post/message/story); kiểm content type cho phép, sizeBytes <= MEDIA_… |
| POST /mini-game/sessions | ../api/mini-game.md | Tạo ván oẳn tù tì với một người bạn (404 nếu không phải bạn), idempotent theo cặp, 409 ALREADY_WAITING nếu us… |
| POST /mini-game/sessions/{id}/cancel | ../api/mini-game.md | Một trong hai participant huỷ ván đang chờ move (idempotent nếu đã cancelled, 409 nếu đã resolved), giải phón… |
| POST /mini-game/sessions/{id}/moves | ../api/mini-game.md | Nộp move rock/paper/scissors bằng UPDATE có điều kiện (move IS NULL AND status=waiting_moves); đủ 2 move thì … |
| POST /mood/status | ../api/mood.md | Đặt mood theo presetCode đang active, bắt buộc header Idempotency-Key; mỗi lần gọi (kể cả cùng preset) là 1 d… |
| POST /movie-match/anon/queue | ../api/movie-match.md | Vào hàng đợi ghép ẩn danh (idempotent): advisory lock tuần tự hoá matcher, ghép với entry cũ nhất vượt qua Sa… |
| POST /movie-match/anon/sessions/{id}/finish | ../api/movie-match.md | Bấm 'Kết thúc' phần xem chung: đặt watchEndedAt và dừng playback để mở phase rating cho cả hai, idempotent; p… |
| POST /movie-match/anon/sessions/{id}/messages | ../api/movie-match.md | Gửi chat ẩn danh trong phiên (HTTP 201): bắt buộc header Idempotency-Key (khoá DB movie:msg:{userId}:{key}, c… |
| POST /movie-match/anon/sessions/{id}/rating | ../api/movie-match.md | Chốt rating like/boring/rude đúng một lần (đổi giá trị → 409, chỉ mở sau watchEndedAt): non-like → not_matche… |
| POST /movie-match/anon/sessions/{id}/reactions | ../api/movie-match.md | Gửi reaction emoji nổi (whitelist 4 emoji): realtime-only, không persist, không kèm userId, @Throttle 60 requ… |
| POST /movie-match/sessions | ../api/movie-match.md | Tạo phiên xem chung với 1 bạn (friendUserId + videoUrl): URL phải http/https và host thuộc MOVIE_MATCH_ALLOWE… |
| POST /movie-match/sessions/{id}/end | ../api/movie-match.md | Một trong hai participant kết thúc phiên bạn bè (endReason=left), idempotent; đổi status và xoá 2 dòng active… |
| POST /notifications/web-push/subscription | ../api/notification.md | Đăng ký/cập nhật browser push subscription (endpoint+p256dh+auth) của user; upsert theo endpoint unique. |
| POST /notifications/{notificationId}/read | ../api/notification.md | Đánh dấu đã đọc, idempotent; chỉ chủ sở hữu (người khác nhận 404 NOTIFICATION_NOT_FOUND để không lộ sự tồn tạ… |
| POST /palm-match/queue | ../api/palm-match.md | Vào hàng đợi (idempotent) và thử ghép ngay với entry cũ nhất còn hợp lệ; mỗi ứng viên phải qua SafetyService.… |
| POST /palm-match/sessions/{id}/flip | ../api/palm-match.md | Lật lá của chính caller (idempotent), quyền theo PalmMatchActiveParticipant(userId, sessionId) lấy từ JWT, kh… |
| POST /palm-match/sessions/{id}/rating | ../api/palm-match.md | Chốt like/skip một lần sau khi cả hai đã lật; cả hai like thì tạo Friendship + Conversation trong cùng transa… |
| POST /party/rooms | ../api/party-room.md | Tạo phòng: transaction lưu party_rooms + membership host (speaker_limit và livekit_url snapshot), rồi createR… |
| POST /party/rooms/{id}/comments | ../api/party-room.md | Gửi comment: bắt buộc Idempotency-Key (unique DB, replay không bắn lại), throttle 60/phút, kiểm member active… |
| POST /party/rooms/{id}/join | ../api/party-room.md | Join làm audience (token canPublish=false) hoặc rejoin: đếm PARTY_MAX_MEMBERS dưới lock; rejoin của host/memb… |
| POST /party/rooms/{id}/leave | ../api/party-room.md | Rời phòng idempotent: member thường nhả membership + removeParticipant khỏi SFU; host rời chủ động thì đóng p… |
| POST /party/rooms/{id}/members/{userId}/role | ../api/party-room.md | Host thu quyền speaker về audience dưới lock phòng; role commit trước rồi updateParticipantPublish(canPublish… |
| POST /party/rooms/{id}/members/{userId}/speaker-invite | ../api/party-room.md | Host mời audience lên speaker: chỉ ghi speaker_invite_pending và bắn party.speaker.invite.received tới target… |
| POST /party/rooms/{id}/speaker-invite/accept | ../api/party-room.md | Target chấp nhận lời mời: đếm speaker so speaker_limit dưới lock phòng, đổi role, updateParticipantPublish(tr… |
| POST /party/rooms/{id}/speaker-invite/decline | ../api/party-room.md | Target từ chối lời mời speaker (idempotent), chỉ xoá cờ speaker_invite_pending. |
| POST /party/rooms/{roomId}/gifts | ../api/gift.md | Tặng quà trong phòng Party: bắt buộc Idempotency-Key, throttle 30/phút, cả người tặng và người nhận phải là m… |
| POST /party/webhooks/livekit | ../api/party-room.md | Nhận webhook LiveKit (participant_left, room_finished) cho room party-*: verify chữ ký JWT trên raw body, sai… |
| POST /profiles/{profileUserId}/conversation | ../api/friend.md | Mở chat trực tiếp từ profile: transaction khoá hàng User đích, trả conversation sẵn có, nếu đã đủ N first-con… |
| POST /profiles/{profileUserId}/follow | ../api/friend.md | Follow một chiều, upsert idempotent theo cặp (follower, followee) đặt active=true; chặn tự follow (400) và bl… |
| POST /profiles/{profileUserId}/gifts | ../api/gift.md | Tặng quà để mở chat trực tiếp từ profile: kiểm tra block hai chiều (trước và lại TRONG transaction), khoá use… |
| POST /safety/blocks/{targetUserId} | ../api/safety.md | Block target (idempotent): chặn tự block, xác nhận target tồn tại, ghi dòng action=blocked nếu dòng mới nhất … |
| POST /safety/reports | ../api/safety.md | Report 1 user: chặn tự report, khoá dòng users của target rồi ghi Report + trừ trust score (1 report hiệu lực… |
| POST /soul-match/sessions/{id}/end | ../api/soul-match.md | Rời phòng: Matching đặt MatchSession sang ended cho cả hai (idempotent, trả 204) rồi publish soul.ended cho c… |
| POST /soul-match/sessions/{id}/messages | ../api/soul-match.md | Gửi message ẩn danh: bắt buộc header Idempotency-Key (unique DB, retry trả lại message cũ), throttle 60 req/p… |
| POST /soul-match/sessions/{id}/rating | ../api/soul-match.md | Chốt verdict rude\|boring\|like một lần (cùng verdict là replay, khác verdict là 409); khi cả hai like thì tạo … |
| POST /stories | ../api/feed.md | Đăng story từ mediaAssetId: bắt buộc Idempotency-Key (unique DB), guest bị chặn, URL ảnh chỉ nhận qua Media.r… |
| POST /stories/{storyId}/reply | ../api/feed.md | Trả lời story thành DM qua FriendService.sendMessage (attachment story_reply); cần đã có Conversation với tác… |
| POST /support/tickets | ../api/support.md | Gửi phiếu hỗ trợ (category + message tối đa 2000 ký tự) ở trạng thái open; bắt buộc header Idempotency-Key, c… |
| POST /videos/upload-intent | ../api/short-video.md | Tạo Video{uploading} + presigned uploadUrl qua VideoStoragePort; bắt buộc Idempotency-Key (replay phát lại ĐÚ… |
| POST /videos/{id}/comments | ../api/short-video.md | Bình luận video: insert VideoComment + tăng commentCount cùng transaction, kèm author PublicProfileDto. |
| POST /videos/{id}/finalize | ../api/short-video.md | Client báo upload xong: conditional UPDATE uploading -> processing, gọi VideoTranscodePort.transcode rồi sang… |
| POST /videos/{id}/reactions | ../api/short-video.md | Thả tim idempotent: insert VideoReaction + tăng likeCount cùng transaction, thua race unique thì replay không… |
| POST /videos/{id}/report | ../api/short-video.md | Report video: kiểm video visible rồi SafetyService.reportVideo (unique 1 report/người/video); vượt VIDEO_REPO… |
| POST /videos/{id}/views | ../api/short-video.md | Ghi watch-time: self-view bỏ qua, video_views unique, viewCount chỉ +1 đúng lúc qualified chuyển false->true … |
| POST /videos/{videoId}/gifts | ../api/gift.md | Tặng quà cho tác giả video: người nhận suy từ video.authorUserId (client không gửi receiverUserId), video phả… |
| PUT /avatar/me/equip | ../api/avatar.md | Trang bị item vào slot: chống IDOR bằng 3 check (asset active, đúng slot, user đã sở hữu) rồi cập nhật cột sl… |
| PUT /discovery/nearby/location | ../api/discovery.md | Ghi vị trí hiện tại, quantize ~500m ngay lúc ghi (không lưu toạ độ thô); rate limit DISCOVERY_LOCATION_UPDATE… |
| PUT /discovery/nearby/visible | ../api/discovery.md | Bật/tắt opt-in Nearby; tắt thì xoá user_locations cùng transaction với cập nhật discovery_settings. |
| PUT /users/me/privacy | ../api/user.md | Ghi privacy settings của mình bằng upsert theo userId (searchableByPhone, hideProfile, showOnlineStatus), giá… |
