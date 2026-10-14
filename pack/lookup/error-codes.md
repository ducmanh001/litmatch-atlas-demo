# Lookup: error codes

id: lookup:error-codes

DOMAIN_SUBJECT_REASON code to its group file.

| id | file | note |
|---|---|---|
| ADMIN_CANNOT_BAN_SELF | ../errors/adminerrors.md | HTTP FORBIDDEN. Admin không được tự ban chính mình — tránh tự khoá mất quyền truy cập. |
| ADMIN_CANNOT_CHANGE_OWN_ROLE | ../errors/adminerrors.md | HTTP CONFLICT. Error code errors:AdminErrors |
| ADMIN_CANNOT_DEMOTE_LAST_ADMIN | ../errors/adminerrors.md | HTTP CONFLICT. Error code errors:AdminErrors |
| ADMIN_CANNOT_DISABLE_PERMISSION_CONTROL | ../errors/adminerrors.md | HTTP CONFLICT. Error code errors:AdminErrors |
| ADMIN_PERMISSION_FORBIDDEN | ../errors/adminerrors.md | HTTP FORBIDDEN. Error code errors:AdminErrors |
| ADMIN_PERMISSION_UNKNOWN | ../errors/adminerrors.md | HTTP BAD_REQUEST,NOT_FOUND. Error code errors:AdminErrors |
| AUTH_GUEST_DEVICE_TOKEN_INVALID | ../errors/autherrors.md | HTTP UNAUTHORIZED. Error code errors:AuthErrors |
| AUTH_GUEST_IDENTITY_RETIRED | ../errors/autherrors.md | HTTP UNAUTHORIZED. Error code errors:AuthErrors |
| AUTH_GUEST_UPGRADE_NOT_ALLOWED | ../errors/autherrors.md | HTTP CONFLICT. Error code errors:AuthErrors |
| AUTH_IDENTITY_ALREADY_LINKED | ../errors/autherrors.md | HTTP CONFLICT. Error code errors:AuthErrors |
| AUTH_OTP_EXPIRED | ../errors/autherrors.md | HTTP BAD_REQUEST. Error code errors:AuthErrors |
| AUTH_OTP_INVALID | ../errors/autherrors.md | HTTP BAD_REQUEST. Error code errors:AuthErrors |
| AUTH_OTP_REQUEST_RATE_LIMITED | ../errors/autherrors.md | HTTP TOO_MANY_REQUESTS. Error code errors:AuthErrors |
| AUTH_OTP_TOO_MANY_ATTEMPTS | ../errors/autherrors.md | HTTP TOO_MANY_REQUESTS. Error code errors:AuthErrors |
| AUTH_PHONE_OTP_DISABLED | ../errors/autherrors.md | HTTP FORBIDDEN. Error code errors:AuthErrors |
| AUTH_REFRESH_TOKEN_INVALID | ../errors/autherrors.md | HTTP UNAUTHORIZED. Error code errors:AuthErrors |
| AUTH_REFRESH_TOKEN_REUSED | ../errors/autherrors.md | HTTP UNAUTHORIZED. Error code errors:AuthErrors |
| AUTH_SOCIAL_PROVIDER_NOT_SUPPORTED | ../errors/autherrors.md | HTTP BAD_REQUEST. Error code errors:AuthErrors |
| AUTH_SOCIAL_TOKEN_INVALID | ../errors/autherrors.md | HTTP UNAUTHORIZED. Error code errors:AuthErrors |
| AUTH_USER_BANNED | ../errors/autherrors.md | HTTP FORBIDDEN. Error code errors:AuthErrors |
| AVATAR_ASSET_IS_FREE | ../errors/avatarerrors.md | HTTP BAD_REQUEST. buy() gọi nhầm item free — dùng claim() thay vì buy(). |
| AVATAR_ASSET_NOT_FOUND | ../errors/avatarerrors.md | HTTP NOT_FOUND. Error code errors:AvatarErrors |
| AVATAR_ASSET_REQUIRES_PURCHASE | ../errors/avatarerrors.md | HTTP BAD_REQUEST. claim() gọi nhầm item phải mua — dùng buy() thay vì claim(). |
| AVATAR_ASSET_SLOT_MISMATCH | ../errors/avatarerrors.md | HTTP UNPROCESSABLE_ENTITY. Error code errors:AvatarErrors |
| AVATAR_ITEM_NOT_OWNED | ../errors/avatarerrors.md | HTTP FORBIDDEN. Error code errors:AvatarErrors |
| CALLING_CALL_ENDED | ../errors/callingerrors.md | HTTP CONFLICT. Call đã kết thúc — không mint token/join lại được. |
| CALLING_CALL_NOT_ENDED | ../errors/callingerrors.md | HTTP CONFLICT. Like chỉ hợp lệ sau khi call đã terminal; tránh lộ danh tính trong lúc đang nói. |
| CALLING_CALL_NOT_FOUND | ../errors/callingerrors.md | HTTP NOT_FOUND. Error code errors:CallingErrors |
| CALLING_SESSION_NOT_CALLABLE | ../errors/callingerrors.md | HTTP CONFLICT. Session không phải voice đã confirmed — chưa có quyền mở phòng. |
| CALLING_SESSION_NOT_FOUND | ../errors/callingerrors.md | HTTP NOT_FOUND. MatchSession/call không tồn tại HOẶC caller không phải thành viên — gộp, chống oracle (docs/1… |
| CALLING_WEBHOOK_INVALID | ../errors/callingerrors.md | HTTP UNAUTHORIZED. Webhook LiveKit không verify được chữ ký. |
| COMMON_CSRF_TOKEN_INVALID | ../errors/commonerrors.md | HTTP UNAUTHORIZED. Error code errors:CommonErrors |
| COMMON_FORBIDDEN | ../errors/commonerrors.md | HTTP FORBIDDEN. Error code errors:CommonErrors |
| COMMON_IDEMPOTENCY_KEY_INVALID | ../errors/commonerrors.md | HTTP BAD_REQUEST. Error code errors:CommonErrors |
| COMMON_IDEMPOTENCY_KEY_MISSING | ../errors/commonerrors.md | HTTP BAD_REQUEST. Error code errors:CommonErrors |
| COMMON_INTERNAL_ERROR | ../errors/commonerrors.md | Error code errors:CommonErrors |
| COMMON_RATE_LIMITED | ../errors/commonerrors.md | Error code errors:CommonErrors |
| COMMON_ROUTE_NOT_FOUND | ../errors/commonerrors.md | Error code errors:CommonErrors |
| COMMON_UNAUTHORIZED | ../errors/commonerrors.md | Error code errors:CommonErrors |
| COMMON_VALIDATION_FAILED | ../errors/commonerrors.md | Error code errors:CommonErrors |
| DISCOVERY_CURSOR_INVALID | ../errors/discoveryerrors.md | HTTP BAD_REQUEST. Error code errors:DiscoveryErrors |
| DISCOVERY_FILTER_INVALID | ../errors/discoveryerrors.md | HTTP UNPROCESSABLE_ENTITY. Error code errors:DiscoveryErrors |
| DISCOVERY_LOCATION_INVALID | ../errors/discoveryerrors.md | HTTP UNPROCESSABLE_ENTITY. Error code errors:DiscoveryErrors |
| DISCOVERY_NEARBY_CURSOR_INVALID | ../errors/discoveryerrors.md | HTTP BAD_REQUEST. Error code errors:DiscoveryErrors |
| DISCOVERY_NEARBY_LOCATION_MISSING | ../errors/discoveryerrors.md | HTTP CONFLICT. Error code errors:DiscoveryErrors |
| DISCOVERY_NEARBY_NOT_OPTED_IN | ../errors/discoveryerrors.md | HTTP FORBIDDEN. Error code errors:DiscoveryErrors |
| DISCOVERY_NEARBY_RATE_LIMITED | ../errors/discoveryerrors.md | HTTP TOO_MANY_REQUESTS. Error code errors:DiscoveryErrors |
| ECONOMY_IAP_DISABLED | ../errors/economyerrors.md | HTTP FORBIDDEN. Error code errors:EconomyErrors |
| ECONOMY_IAP_PRODUCT_UNKNOWN | ../errors/economyerrors.md | HTTP NOT_FOUND,BAD_REQUEST. Error code errors:EconomyErrors |
| ECONOMY_IAP_PROVIDER_UNAVAILABLE | ../errors/economyerrors.md | Error code errors:EconomyErrors |
| ECONOMY_IAP_RECEIPT_INVALID | ../errors/economyerrors.md | HTTP BAD_REQUEST. Error code errors:EconomyErrors |
| ECONOMY_PAYOS_DISABLED | ../errors/economyerrors.md | Error code errors:EconomyErrors |
| ECONOMY_PAYOS_GUEST_FORBIDDEN | ../errors/economyerrors.md | HTTP FORBIDDEN. Error code errors:EconomyErrors |
| ECONOMY_PAYOS_ORDER_IDEMPOTENCY_CONFLICT | ../errors/economyerrors.md | HTTP CONFLICT. Error code errors:EconomyErrors |
| ECONOMY_PAYOS_ORDER_NOT_FOUND | ../errors/economyerrors.md | HTTP NOT_FOUND. Error code errors:EconomyErrors |
| ECONOMY_PAYOS_PACKAGE_UNKNOWN | ../errors/economyerrors.md | HTTP NOT_FOUND. Error code errors:EconomyErrors |
| ECONOMY_PAYOS_PROVIDER_UNAVAILABLE | ../errors/economyerrors.md | Error code errors:EconomyErrors |
| ECONOMY_PAYOS_WEBHOOK_INVALID | ../errors/economyerrors.md | HTTP BAD_REQUEST. Error code errors:EconomyErrors |
| ECONOMY_PAYOS_WEBHOOK_MISMATCH | ../errors/economyerrors.md | HTTP BAD_REQUEST. Error code errors:EconomyErrors |
| ECONOMY_TRANSACTION_ALREADY_REVERSED | ../errors/economyerrors.md | HTTP CONFLICT. Error code errors:EconomyErrors |
| ECONOMY_TRANSACTION_CURSOR_INVALID | ../errors/economyerrors.md | HTTP BAD_REQUEST. Error code errors:EconomyErrors |
| ECONOMY_TRANSACTION_IDEMPOTENCY_CONFLICT | ../errors/economyerrors.md | HTTP CONFLICT. Error code errors:EconomyErrors |
| ECONOMY_TRANSACTION_NOT_FOUND | ../errors/economyerrors.md | HTTP NOT_FOUND. Error code errors:EconomyErrors |
| ECONOMY_VIP_PLAN_UNKNOWN | ../errors/economyerrors.md | HTTP NOT_FOUND. Error code errors:EconomyErrors |
| ECONOMY_WALLET_INSUFFICIENT_BALANCE | ../errors/economyerrors.md | HTTP UNPROCESSABLE_ENTITY. Error code errors:EconomyErrors |
| ECONOMY_WEBHOOK_SIGNATURE_INVALID | ../errors/economyerrors.md | HTTP UNAUTHORIZED. Error code errors:EconomyErrors |
| FEED_COMMENT_NOT_FOUND | ../errors/feederrors.md | HTTP NOT_FOUND,FORBIDDEN. Error code errors:FeedErrors |
| FEED_CURSOR_INVALID | ../errors/feederrors.md | HTTP BAD_REQUEST. Error code errors:FeedErrors |
| FEED_GUEST_FORBIDDEN | ../errors/feederrors.md | HTTP FORBIDDEN. Error code errors:FeedErrors |
| FEED_POST_CONTENT_REQUIRED | ../errors/feederrors.md | HTTP UNPROCESSABLE_ENTITY. Error code errors:FeedErrors |
| FEED_POST_IDEMPOTENCY_CONFLICT | ../errors/feederrors.md | HTTP CONFLICT. Error code errors:FeedErrors |
| FEED_POST_NOT_FOUND | ../errors/feederrors.md | HTTP NOT_FOUND,FORBIDDEN. Error code errors:FeedErrors |
| FEED_STORY_MEDIA_URL_REQUIRED | ../errors/feederrors.md | HTTP UNPROCESSABLE_ENTITY. Error code errors:FeedErrors |
| FEED_STORY_NOT_FOUND | ../errors/feederrors.md | HTTP NOT_FOUND. Error code errors:FeedErrors |
| FEED_STORY_REPLY_REQUIRES_FRIENDSHIP | ../errors/feederrors.md | HTTP FORBIDDEN. Error code errors:FeedErrors |
| FRIEND_CONVERSATION_NOT_FOUND | ../errors/frienderrors.md | HTTP NOT_FOUND. Conversation không tồn tại HOẶC caller không phải thành viên — cùng 1 mã (docs/10 § 10.1.D). |
| FRIEND_CURSOR_INVALID | ../errors/frienderrors.md | HTTP BAD_REQUEST. Error code errors:FriendErrors |
| FRIEND_MESSAGE_EMPTY | ../errors/frienderrors.md | HTTP UNPROCESSABLE_ENTITY. Message không có cả text lẫn ảnh — không có gì để gửi. |
| FRIEND_MESSAGE_IDEMPOTENCY_CONFLICT | ../errors/frienderrors.md | HTTP CONFLICT. Cùng Idempotency-Key nhưng nội dung/conversation khác (docs/05 § 5.10). |
| FRIEND_MESSAGE_TOO_LONG | ../errors/frienderrors.md | HTTP UNPROCESSABLE_ENTITY. Error code errors:FriendErrors |
| FRIEND_NOT_FRIEND | ../errors/frienderrors.md | HTTP NOT_FOUND. Không có conversation với profile (gộp not-found + chưa mở chat, chống oracle). |
| GIFT_CODE_ALREADY_EXISTS | ../errors/gifterrors.md | HTTP CONFLICT. Admin tạo quà với `code` đã tồn tại (unique constraint `uq_gifts_code`). |
| GIFT_GIFT_NOT_FOUND | ../errors/gifterrors.md | HTTP NOT_FOUND. Quà không tồn tại hoặc đã tắt khỏi catalog. |
| GIFT_RECEIVER_NOT_IN_ROOM | ../errors/gifterrors.md | HTTP CONFLICT. Người nhận không phải member active của phòng. |
| GIFT_SELF_GIFT_FORBIDDEN | ../errors/gifterrors.md | HTTP BAD_REQUEST. Không tự tặng quà cho chính mình (không có ý nghĩa kinh tế, chỉ để farm điểm). |
| GIFT_SENDER_NOT_IN_ROOM | ../errors/gifterrors.md | HTTP FORBIDDEN. Người tặng không phải member active của phòng. |
| MATCHING_DAILY_QUOTA_EXCEEDED | ../errors/matchingerrors.md | HTTP TOO_MANY_REQUESTS. Error code errors:MatchingErrors |
| MATCHING_GUEST_DAILY_QUOTA_EXCEEDED | ../errors/matchingerrors.md | HTTP TOO_MANY_REQUESTS. Error code errors:MatchingErrors |
| MATCHING_GUEST_DEVICE_TOKEN_REQUIRED | ../errors/matchingerrors.md | HTTP UNAUTHORIZED. Error code errors:MatchingErrors |
| MATCHING_INVITE_ACCEPT_USER_BUSY | ../errors/matchingerrors.md | HTTP CONFLICT. 1 trong 2 bên (inviter/invitee) đang có ticket active khác tại thời điểm accept (docs/06). |
| MATCHING_INVITE_ALREADY_PENDING | ../errors/matchingerrors.md | HTTP CONFLICT. Đã có 1 invite PENDING khác tới đúng người này (unique DB, không phải rate-limit chính). |
| MATCHING_INVITE_CURSOR_INVALID | ../errors/matchingerrors.md | HTTP BAD_REQUEST. Error code errors:MatchingErrors |
| MATCHING_INVITE_FORBIDDEN | ../errors/matchingerrors.md | HTTP FORBIDDEN. Invite không thuộc user đang gọi (không phải inviter/invitee tương ứng hành động). |
| MATCHING_INVITE_INVALID_TRANSITION | ../errors/matchingerrors.md | HTTP CONFLICT. Error code errors:MatchingErrors |
| MATCHING_INVITE_NOT_FOUND | ../errors/matchingerrors.md | HTTP NOT_FOUND. Error code errors:MatchingErrors |
| MATCHING_INVITE_RATE_LIMITED | ../errors/matchingerrors.md | HTTP TOO_MANY_REQUESTS. Error code errors:MatchingErrors |
| MATCHING_INVITE_TARGET_UNAVAILABLE | ../errors/matchingerrors.md | HTTP UNPROCESSABLE_ENTITY,FORBIDDEN. Không mời được người đang nằm trong hidden-set (banned/guest/block/repor… |
| MATCHING_SESSION_NOT_CALLABLE | ../errors/matchingerrors.md | HTTP CONFLICT. Error code errors:MatchingErrors |
| MATCHING_SESSION_NOT_FOUND | ../errors/matchingerrors.md | HTTP NOT_FOUND. Session không tồn tại hoặc caller không phải một trong hai thành viên (gộp, chống oracle). |
| MATCHING_SESSION_NOT_PENDING | ../errors/matchingerrors.md | HTTP CONFLICT. Error code errors:MatchingErrors |
| MATCHING_TICKET_ALREADY_QUEUED | ../errors/matchingerrors.md | HTTP CONFLICT. Error code errors:MatchingErrors |
| MATCHING_TICKET_FORBIDDEN | ../errors/matchingerrors.md | HTTP FORBIDDEN. Ticket không thuộc user đang gọi — chống IDOR (docs/10 § 10.1.D). |
| MATCHING_TICKET_IDEMPOTENCY_CONFLICT | ../errors/matchingerrors.md | HTTP CONFLICT. Cùng Idempotency-Key nhưng nội dung request khác (docs/05 § 5.10). |
| MATCHING_TICKET_INVALID_TRANSITION | ../errors/matchingerrors.md | HTTP CONFLICT. Error code errors:MatchingErrors |
| MATCHING_TICKET_NOT_FOUND | ../errors/matchingerrors.md | HTTP NOT_FOUND. Error code errors:MatchingErrors |
| MATCHING_USER_BANNED | ../errors/matchingerrors.md | HTTP FORBIDDEN,CONFLICT. User bị ban không được vào hàng đợi (re-verify lại lần nữa tại thời điểm ghép). |
| MEDIA_ASSET_NOT_FOUND | ../errors/mediaerrors.md | HTTP NOT_FOUND. Error code errors:MediaErrors |
| MEDIA_ASSET_NOT_READY | ../errors/mediaerrors.md | HTTP UNPROCESSABLE_ENTITY. Error code errors:MediaErrors |
| MEDIA_FILE_TOO_LARGE | ../errors/mediaerrors.md | HTTP UNPROCESSABLE_ENTITY. Error code errors:MediaErrors |
| MEDIA_GUEST_FORBIDDEN | ../errors/mediaerrors.md | HTTP FORBIDDEN. Error code errors:MediaErrors |
| MEDIA_INVALID_CONTENT_TYPE | ../errors/mediaerrors.md | HTTP UNPROCESSABLE_ENTITY. Error code errors:MediaErrors |
| MINI_GAME_ALREADY_WAITING | ../errors/minigameerrors.md | HTTP CONFLICT. Caller đang chờ move ở 1 CẶP KHÁC — không tự ý huỷ ván cũ thay user. |
| MINI_GAME_MOVE_ALREADY_SUBMITTED | ../errors/minigameerrors.md | HTTP CONFLICT. Nộp move lần 2 (đã nộp trước đó) HOẶC session không còn `waiting_moves` (đã `resolved`/`cancel… |
| MINI_GAME_NOT_CANCELLABLE | ../errors/minigameerrors.md | HTTP CONFLICT. Huỷ ván đã `resolved`/`cancelled` — chỉ huỷ được ván đang `waiting_moves`. |
| MINI_GAME_NOT_FRIEND | ../errors/minigameerrors.md | HTTP NOT_FOUND. `friendUserId` không phải bạn của caller HOẶC caller tự nhắm chính mình — gộp 1 mã, cùng 404 … |
| MINI_GAME_SESSION_NOT_FOUND | ../errors/minigameerrors.md | HTTP NOT_FOUND. Session không tồn tại HOẶC caller không phải participant — cùng 1 mã (docs/10 § 10.1.D). |
| MOOD_PRESET_NOT_FOUND | ../errors/mooderrors.md | HTTP NOT_FOUND. Error code errors:MoodErrors |
| MOVIE_SESSION_ALREADY_ACTIVE | ../errors/moviematcherrors.md | HTTP CONFLICT. Caller đang active với 1 cặp KHÁC — không tự ý kết thúc session cũ thay user (spec § 3). |
| MOVIE_SESSION_ANON_VIDEO_POOL_EMPTY | ../errors/moviematcherrors.md | HTTP CONFLICT. Không có video nào trong MOVIE_MATCH_ANON_VIDEO_URLS — lỗi cấu hình vận hành. |
| MOVIE_SESSION_ENDED | ../errors/moviematcherrors.md | HTTP CONFLICT. Session đã `ended` mà vẫn thao tác state. |
| MOVIE_SESSION_INVALID_VIDEO_URL | ../errors/moviematcherrors.md | HTTP UNPROCESSABLE_ENTITY. `videoUrl` sai format, quá dài, hoặc domain không nằm trong whitelist. |
| MOVIE_SESSION_MESSAGE_IDEMPOTENCY_CONFLICT | ../errors/moviematcherrors.md | HTTP CONFLICT. Cùng Idempotency-Key nhưng nội dung/session khác (docs/05 § 5.10). |
| MOVIE_SESSION_MESSAGE_TOO_LONG | ../errors/moviematcherrors.md | HTTP UNPROCESSABLE_ENTITY. Chat quá MOVIE_MATCH_MESSAGE_MAX_LENGTH. |
| MOVIE_SESSION_NOT_FOUND | ../errors/moviematcherrors.md | HTTP BAD_REQUEST,NOT_FOUND. Session không tồn tại HOẶC caller không phải participant — cùng 1 mã (docs/10 § 1… |
| MOVIE_SESSION_NOT_FRIEND | ../errors/moviematcherrors.md | HTTP NOT_FOUND. `friendUserId` không phải bạn của caller HOẶC caller tự nhắm chính mình — gộp 1 mã, cùng 404 … |
| MOVIE_SESSION_RATING_CONFLICT | ../errors/moviematcherrors.md | HTTP CONFLICT. Đổi rating đã chốt — mỗi bên chỉ đánh giá một lần. |
| MOVIE_SESSION_RATING_NOT_OPEN | ../errors/moviematcherrors.md | HTTP CONFLICT. Rating gọi trước khi phase xem kết thúc (chưa "Kết thúc"/chưa hết giờ). |
| MOVIE_SESSION_REACTION_INVALID | ../errors/moviematcherrors.md | HTTP UNPROCESSABLE_ENTITY. Reaction emoji ngoài whitelist. |
| NOTIFICATION_CURSOR_INVALID | ../errors/notificationerrors.md | HTTP BAD_REQUEST. Error code errors:NotificationErrors |
| NOTIFICATION_NOTIFICATION_NOT_FOUND | ../errors/notificationerrors.md | HTTP NOT_FOUND. Error code errors:NotificationErrors |
| PALM_MATCH_CATEGORY_EMPTY | ../errors/palmmatcherrors.md | HTTP CONFLICT. Không có template active nào cho category — tránh 500/mảng rỗng khi chọn phần tử. |
| PALM_MATCH_RATING_CONFLICT | ../errors/palmmatcherrors.md | HTTP CONFLICT. Một rating đã chốt không được đổi verdict khi retry. |
| PALM_MATCH_RATING_NOT_OPEN | ../errors/palmmatcherrors.md | HTTP CONFLICT. Rating chỉ mở sau khi cả hai participant tự lật lá của mình. |
| PALM_MATCH_SESSION_FINISHED | ../errors/palmmatcherrors.md | HTTP CONFLICT. Session đã terminal nên không nhận thêm flip/rating. |
| PALM_MATCH_SESSION_NOT_FOUND | ../errors/palmmatcherrors.md | HTTP NOT_FOUND. Session không tồn tại, caller không còn active trong session hoặc không phải participant. |
| PALM_MATCH_TARGET_NAME_TOO_LONG | ../errors/palmmatcherrors.md | HTTP UNPROCESSABLE_ENTITY. `targetName` vượt quá `PALM_MATCH_TARGET_NAME_MAX_LENGTH`. |
| PARTY_MEDIA_ROOM_CREATE_FAILED | ../errors/partyroomerrors.md | Tạo room trên SFU thất bại — thử lại sau. |
| PARTY_MEMBER_ALREADY_IN_ANOTHER_ROOM | ../errors/partyroomerrors.md | HTTP CONFLICT. User đang ở 1 phòng active khác — 1 user 1 phòng (party-room-service.md § 3). |
| PARTY_MEMBER_CANNOT_CHANGE_HOST_ROLE | ../errors/partyroomerrors.md | HTTP CONFLICT. Không đổi role của chính host. |
| PARTY_MEMBER_NOT_A_MEMBER | ../errors/partyroomerrors.md | HTTP FORBIDDEN. Caller không phải member active của phòng. |
| PARTY_MEMBER_NOT_HOST | ../errors/partyroomerrors.md | HTTP FORBIDDEN. Chỉ host mới được cấp/thu quyền speaker (docs/06). |
| PARTY_ROOM_CLOSED | ../errors/partyroomerrors.md | HTTP CONFLICT. Phòng đã đóng — không join/đổi role/tặng quà được nữa. |
| PARTY_ROOM_COMMENT_IDEMPOTENCY_CONFLICT | ../errors/partyroomerrors.md | HTTP CONFLICT. Error code errors:PartyRoomErrors |
| PARTY_ROOM_COMMENT_INVALID | ../errors/partyroomerrors.md | HTTP UNPROCESSABLE_ENTITY. Error code errors:PartyRoomErrors |
| PARTY_ROOM_CURSOR_INVALID | ../errors/partyroomerrors.md | HTTP BAD_REQUEST. Error code errors:PartyRoomErrors |
| PARTY_ROOM_FULL | ../errors/partyroomerrors.md | HTTP CONFLICT. Phòng đã đủ PARTY_MAX_MEMBERS. |
| PARTY_ROOM_NOT_FOUND | ../errors/partyroomerrors.md | HTTP NOT_FOUND. Phòng không tồn tại HOẶC đã đóng với người ngoài — gộp, chống oracle (docs/10 § 10.1.D). |
| PARTY_ROOM_TITLE_TOO_LONG | ../errors/partyroomerrors.md | HTTP BAD_REQUEST. Title vượt PARTY_TITLE_MAX_LENGTH. |
| PARTY_SPEAKER_INVITE_NOT_PENDING | ../errors/partyroomerrors.md | HTTP CONFLICT. Target chưa đồng ý lời mời lên speaker. |
| PARTY_SPEAKER_INVITE_TARGET_INVALID | ../errors/partyroomerrors.md | HTTP CONFLICT. Target đã là speaker/host hoặc không thể nhận lời mời. |
| PARTY_SPEAKER_LIMIT_REACHED | ../errors/partyroomerrors.md | HTTP CONFLICT. Đã chạm speaker_limit của phòng (giới hạn cứng — docs/03 § 3.8.A). |
| PARTY_TARGET_NOT_A_MEMBER | ../errors/partyroomerrors.md | HTTP NOT_FOUND. Target không phải member active của phòng. |
| PARTY_WEBHOOK_INVALID | ../errors/partyroomerrors.md | HTTP UNAUTHORIZED. Webhook LiveKit không verify được chữ ký. |
| PROFILE_SOCIAL_MESSAGE_GIFT_REQUIRED | ../errors/profilesocialerrors.md | HTTP PAYMENT_REQUIRED. Error code errors:ProfileSocialErrors |
| PROFILE_SOCIAL_PROFILE_NOT_AVAILABLE | ../errors/profilesocialerrors.md | HTTP NOT_FOUND. Error code errors:ProfileSocialErrors |
| PROFILE_SOCIAL_SELF_ACTION | ../errors/profilesocialerrors.md | HTTP BAD_REQUEST. Error code errors:ProfileSocialErrors |
| SAFETY_REPORT_NOT_FOUND | ../errors/safetyerrors.md | HTTP NOT_FOUND. Error code errors:SafetyErrors |
| SAFETY_SELF_BLOCK_FORBIDDEN | ../errors/safetyerrors.md | HTTP BAD_REQUEST. Error code errors:SafetyErrors |
| SAFETY_SELF_REPORT_FORBIDDEN | ../errors/safetyerrors.md | HTTP BAD_REQUEST. Error code errors:SafetyErrors |
| SHORT_VIDEO_CAPTION_TOO_LONG | ../errors/shortvideoerrors.md | HTTP UNPROCESSABLE_ENTITY. Error code errors:ShortVideoErrors |
| SHORT_VIDEO_CURSOR_INVALID | ../errors/shortvideoerrors.md | HTTP BAD_REQUEST. Error code errors:ShortVideoErrors |
| SHORT_VIDEO_FORBIDDEN | ../errors/shortvideoerrors.md | HTTP FORBIDDEN. Error code errors:ShortVideoErrors |
| SHORT_VIDEO_INVALID_TRANSITION | ../errors/shortvideoerrors.md | HTTP CONFLICT,video ? CONFLICT : HttpStatus.NOT_FOUND. Error code errors:ShortVideoErrors |
| SHORT_VIDEO_NOT_FOUND | ../errors/shortvideoerrors.md | HTTP NOT_FOUND. Error code errors:ShortVideoErrors |
| SHORT_VIDEO_UPLOAD_DISABLED | ../errors/shortvideoerrors.md | HTTP FORBIDDEN. Error code errors:ShortVideoErrors |
| SHORT_VIDEO_UPLOAD_IDEMPOTENCY_CONFLICT | ../errors/shortvideoerrors.md | HTTP CONFLICT. Error code errors:ShortVideoErrors |
| SOUL_MATCH_CHAT_NOT_OPEN | ../errors/soulmatcherrors.md | HTTP CONFLICT. Phòng chưa mở (chưa đủ 2 confirm / không phải soul) hoặc đã qua phase cho phép. |
| SOUL_MATCH_CURSOR_INVALID | ../errors/soulmatcherrors.md | HTTP BAD_REQUEST. Error code errors:SoulMatchErrors |
| SOUL_MATCH_MESSAGE_IDEMPOTENCY_CONFLICT | ../errors/soulmatcherrors.md | HTTP CONFLICT. Cùng Idempotency-Key nhưng nội dung/session khác (docs/05 § 5.10). |
| SOUL_MATCH_MESSAGE_TOO_LONG | ../errors/soulmatcherrors.md | HTTP UNPROCESSABLE_ENTITY. Vượt SOUL_CHAT_MESSAGE_MAX_LENGTH (config) — DTO chỉ chặn sanity cap. |
| SOUL_MATCH_PARTNER_LOCKED | ../errors/soulmatcherrors.md | HTTP FORBIDDEN. Chưa match (chưa có Friendship) — profile đối phương còn khoá (docs/01 #1). |
| SOUL_MATCH_RATING_CONFLICT | ../errors/soulmatcherrors.md | HTTP CONFLICT. Đã rate verdict khác — rating immutable (docs/10 § Soul Match). |
| SOUL_MATCH_RATING_NOT_OPEN | ../errors/soulmatcherrors.md | HTTP CONFLICT. Hết cửa sổ rating (phase closed). |
| SOUL_MATCH_SESSION_NOT_FOUND | ../errors/soulmatcherrors.md | HTTP NOT_FOUND. Session không tồn tại HOẶC caller không phải thành viên — gộp 1 mã, không làm oracle dò sessi… |
| SOUL_MATCH_USER_BANNED | ../errors/soulmatcherrors.md | HTTP FORBIDDEN. Bị ban giữa chừng — re-check tại thời điểm gửi (docs/10 § 10.0.C). |
| SUPPORT_CURSOR_INVALID | ../errors/supporterrors.md | HTTP BAD_REQUEST. Error code errors:SupportErrors |
| SUPPORT_IDEMPOTENCY_CONFLICT | ../errors/supporterrors.md | HTTP CONFLICT. Error code errors:SupportErrors |
| SUPPORT_INVALID_TRANSITION | ../errors/supporterrors.md | HTTP CONFLICT. Error code errors:SupportErrors |
| SUPPORT_TICKET_NOT_FOUND | ../errors/supporterrors.md | HTTP NOT_FOUND. Error code errors:SupportErrors |
| USER_PROFILE_BIRTH_DATE_INVALID | ../errors/usererrors.md | HTTP UNPROCESSABLE_ENTITY. Error code errors:UserErrors |
| USER_PROFILE_NOT_FOUND | ../errors/usererrors.md | HTTP NOT_FOUND. Error code errors:UserErrors |
| USER_SEEKING_AGE_RANGE_INVALID | ../errors/usererrors.md | HTTP UNPROCESSABLE_ENTITY. Khoảng tuổi "Đang tìm kiếm" min > max sau khi gộp giá trị mới với giá trị đã lưu. |
