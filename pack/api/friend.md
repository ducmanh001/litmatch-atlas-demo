# REST API of friend

id: api:friend
module: module:friend
prefix: /api/v1; success body {data, meta?}; errors are DomainException codes (see ../errors/)
see: ../modules/friend.md

### GET /profiles/{profileUserId}/follow-counts

Trả followerCount/followingCount của một profile, dùng được cho cả chính mình (khác /actions từ chối self); 404 gộp khi bị block 2 chiều.

- handler: ProfileSocialController.getFollowCounts (apps/core-api/src/modules/friend/controllers/profile-social.controller.ts:46); operationId: ProfileSocialController_getFollowCounts
- auth: Bearer JWT
- params: path:profileUserId*:string
- response 200 FollowCountsDto: followerCount:number, followingCount:number
- FE callers: none in this repo (mobile/third party or dead)

### GET /profiles/{profileUserId}/followers

Danh sách người theo dõi profile, cursor keyset (last_followed_at, id) mới theo dõi nhất trước, kèm isFollowing của người xem; chỉ follow active, user active, loại người có block 2 chiều với người xem; cursor sai trả 400.

- handler: ProfileSocialController.listFollowers (apps/core-api/src/modules/friend/controllers/profile-social.controller.ts:61); operationId: ProfileSocialController_listFollowers
- auth: Bearer JWT
- params: path:profileUserId*:string, query:limit:number, query:cursor:string
- response 200 FollowListPageDto: items:array<FollowListItemDto>, items[].profile:PublicProfileDto, items[].profile.id:string, items[].profile.nickname:string, items[].profile.gender:enum(unknown|male|female|other), items[].profile.avatarId:string, items[].profile.interests:array<string>, items[].followedAt:string(date-time), items[].isFollowing:boolean, meta:object, meta.nextCursor:string
- FE callers: web apps/web/src/features/profile/api.ts:56

### GET /profiles/{profileUserId}/following

Danh sách profile mà user đang theo dõi, cùng cursor keyset và cùng bộ lọc block/active như followers.

- handler: ProfileSocialController.listFollowing (apps/core-api/src/modules/friend/controllers/profile-social.controller.ts:82); operationId: ProfileSocialController_listFollowing
- auth: Bearer JWT
- params: path:profileUserId*:string, query:limit:number, query:cursor:string
- response 200 FollowListPageDto: items:array<FollowListItemDto>, items[].profile:PublicProfileDto, items[].profile.id:string, items[].profile.nickname:string, items[].profile.gender:enum(unknown|male|female|other), items[].profile.avatarId:string, items[].profile.interests:array<string>, items[].followedAt:string(date-time), items[].isFollowing:boolean, meta:object, meta.nextCursor:string
- FE callers: web apps/web/src/features/profile/api.ts:59

### GET /profiles/{profileUserId}/actions

Trả isFollowing, follower/following count, conversationId hiện có và requiresGift (số ProfileChatContact hôm nay UTC >= ngưỡng); 400 khi tự xem mình, 404 gộp khi bị block 2 chiều.

- handler: ProfileSocialController.getActions (apps/core-api/src/modules/friend/controllers/profile-social.controller.ts:103); operationId: ProfileSocialController_getActions
- auth: Bearer JWT
- params: path:profileUserId*:string
- response 200 ProfileActionsDto: isFollowing:boolean, followerCount:number, followingCount:number, conversationId:string, messageAvailable:boolean, requiresGift:boolean, dailyFirstChatCount:number, firstChatThreshold:number
- FE callers: none in this repo (mobile/third party or dead)

### POST /profiles/{profileUserId}/follow

Follow một chiều, upsert idempotent theo cặp (follower, followee) đặt active=true; chặn tự follow (400) và block 2 chiều (404).

- handler: ProfileSocialController.follow (apps/core-api/src/modules/friend/controllers/profile-social.controller.ts:115); operationId: ProfileSocialController_follow
- auth: Bearer JWT
- params: path:profileUserId*:string
- response 200 ProfileFollowDto: following:boolean
- FE callers: none in this repo (mobile/third party or dead)

### DELETE /profiles/{profileUserId}/follow

Unfollow chỉ đặt active=false (giữ lịch sử last_followed_at); cùng guard tự thao tác và block như follow.

- handler: ProfileSocialController.unfollow (apps/core-api/src/modules/friend/controllers/profile-social.controller.ts:128); operationId: ProfileSocialController_unfollow
- auth: Bearer JWT
- params: path:profileUserId*:string
- response 200 ProfileFollowDto: following:boolean
- FE callers: none in this repo (mobile/third party or dead)

### POST /profiles/{profileUserId}/conversation

Mở chat trực tiếp từ profile: transaction khoá hàng User đích, trả conversation sẵn có, nếu đã đủ N first-contact hôm nay trả 402 PROFILE_SOCIAL_MESSAGE_GIFT_REQUIRED, ngược lại tạo Conversation + ProfileChatContact.

- handler: ProfileSocialController.openConversation (apps/core-api/src/modules/friend/controllers/profile-social.controller.ts:141); operationId: ProfileSocialController_openConversation
- auth: Bearer JWT
- params: path:profileUserId*:string
- response 200 ConversationDto: id:string
- FE callers: none in this repo (mobile/third party or dead)

### GET /friends

Inbox mọi conversation của caller sort theo lastMessageAt, kèm profile công khai, unreadCount, lastMessagePreview, muted, isFriend và canCall (follow active 2 chiều).

- handler: FriendController.listFriends (apps/core-api/src/modules/friend/friend.controller.ts:56); operationId: FriendController_listFriends
- auth: Bearer JWT
- response 200 array<FriendDto>: [].profile:PublicProfileDto, [].profile.id:string, [].profile.nickname:string, [].profile.gender:enum(unknown|male|female|other), [].profile.avatarId:string, [].profile.interests:array<string>, [].conversationId:string, [].friendSince:string(date-time), [].lastMessageAt:string(date-time), [].unreadCount:number, [].lastMessagePreview:string, [].muted:boolean, [].isFriend:boolean, [].canCall:boolean
- FE callers: web apps/web/src/features/friend-chat/api.ts:35

### GET /friends/connections

Danh sách bạn bè = Friendship (cùng "Thích" lúc ghép đôi) hợp follow active hai chiều, gộp một dòng mỗi người kèm isFriend/isMutualFollow/since; không cần có conversation; loại user bị khoá và người có block 2 chiều; tối đa 500 dòng, không phân trang.

- handler: FriendController.listConnections (apps/core-api/src/modules/friend/friend.controller.ts:85); operationId: FriendController_listConnections
- auth: Bearer JWT
- response 200 array<FriendConnectionDto>: [].profile:PublicProfileDto, [].profile.id:string, [].profile.nickname:string, [].profile.gender:enum(unknown|male|female|other), [].profile.avatarId:string, [].profile.interests:array<string>, [].isFriend:boolean, [].isMutualFollow:boolean, [].since:string(date-time)
- FE callers: web apps/web/src/features/friend-chat/api.ts:49

### GET /friends/{friendUserId}/conversation

Tra conversation của một cặp theo cặp canonical; chưa có thì 404 NOT_FRIEND (không phân biệt user không tồn tại, tránh oracle).

- handler: FriendController.getConversationWithFriend (apps/core-api/src/modules/friend/friend.controller.ts:109); operationId: FriendController_getConversationWithFriend
- auth: Bearer JWT
- params: path:friendUserId*:string
- response 200 ConversationDto: id:string
- FE callers: none in this repo (mobile/third party or dead)

### GET /conversations/{id}/messages

Liệt kê message theo cursor keyset seq; chỉ 2 thành viên, conversation không tồn tại hoặc không phải thành viên đều 404 gộp. Là fallback REST khi socket rớt.

- handler: FriendController.listMessages (apps/core-api/src/modules/friend/friend.controller.ts:126); operationId: FriendController_listMessages
- auth: Bearer JWT
- params: path:id*:string, query:limit:number, query:cursor:string
- response 200 MessagesPageDto: items:array<MessageDto>, items[].id:string, items[].conversationId:string, items[].senderUserId:string, items[].content:string, items[].attachment:MessageAttachmentDto, items[].attachment.kind:string, items[].attachment.payload:object, items[].sentAt:string(date-time), meta:object, meta.nextCursor:string
- FE callers: web apps/web/src/features/friend-chat/api.ts:91

### GET /conversations/{id}/streak

Streak hiện tại derive khi đọc (isActive khi gap <= 2 ngày UTC, chưa có streak trả current=0); chỉ thành viên.

- handler: FriendController.getStreak (apps/core-api/src/modules/friend/friend.controller.ts:147); operationId: FriendController_getStreak
- auth: Bearer JWT
- params: path:id*:string
- response 200 StreakDto: current:number, longest:number, isActive:boolean
- FE callers: none in this repo (mobile/third party or dead)

### POST /conversations/{id}/read

Đánh dấu đã đọc tới now() bằng upsert ON CONFLICT trên conversation_member_states; idempotent, chỉ thành viên.

- handler: FriendController.markRead (apps/core-api/src/modules/friend/friend.controller.ts:161); operationId: FriendController_markRead
- auth: Bearer JWT
- params: path:id*:string
- response 200 ConversationMemberStateDto: lastReadAt:string(date-time), muted:boolean
- FE callers: web apps/web/src/features/friend-chat/api.ts:140

### POST /conversations/{id}/mute

Bật/tắt mute hội thoại (body {muted}); chỉ tắt kênh notification friend_message, message/realtime/unread vẫn chạy.

- handler: FriendController.setMuted (apps/core-api/src/modules/friend/friend.controller.ts:178); operationId: FriendController_setMuted
- auth: Bearer JWT
- params: path:id*:string
- body MuteConversationDto: muted*:boolean
- response 200 ConversationMemberStateDto: lastReadAt:string(date-time), muted:boolean
- FE callers: web apps/web/src/features/friend-chat/api.ts:158

### POST /conversations/{id}/messages

Gửi message: bắt buộc Idempotency-Key, guard thành viên + block 2 chiều (404 gộp), resolve imageAssetId qua Media, lưu message, tính streak trong transaction riêng, publish friend.message sau persist, notification best-effort (bỏ qua nếu người nhận mute).

- handler: FriendController.sendMessage (apps/core-api/src/modules/friend/friend.controller.ts:198); operationId: FriendController_sendMessage
- auth: Bearer JWT; requires Idempotency-Key header
- params: path:id*:string, header:Idempotency-Key*:string
- body SendFriendMessageDto: content:string[maxLength=4000], imageAssetId:string
- response 201 MessageDto: id:string, conversationId:string, senderUserId:string, content:string, attachment:MessageAttachmentDto, attachment.kind:string, attachment.payload:object, sentAt:string(date-time)
- FE callers: web apps/web/src/features/friend-chat/api.ts:115

