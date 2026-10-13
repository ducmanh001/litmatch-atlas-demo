# Database tables owned by module friend

id: db:friend
module: module:friend
tables: conversation_member_states, conversation_streaks, conversations, friendships, messages, profile_chat_contacts, profile_follows
see: ../modules/friend.md

### table: conversation_member_states

Trạng thái CÁ NHÂN của 1 thành viên trong 1 conversation — mỗi bên một dòng, KHÔNG phải trạng thái chung của conversation (khác `Conversation.lastMessageAt`). Lazy: chỉ tồn tại sau lần đầu user đọc/mute; vắng dòng ⟺ chưa đọc gì và không mute. - `lastReadAt`: mốc đã đọc — unread count = message của ĐỐI PHƯƠNG sau mốc này. - `mutedAt`: đang tắt thông báo hội thoại này (notification friend_message bị bỏ qua, message vẫn gửi bình thường) — NULL là đang bật.

Entity: ConversationMemberState (apps/core-api/src/modules/friend/entities/conversation-member-state.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| conversation_id | uuid |  |  | PK FK→conversations.id |  |
| user_id | uuid |  |  | PK |  |
| last_read_at | timestamp with time zone | null |  |  |  |
| muted_at | timestamp with time zone | null |  |  |  |

Access: friend(owner) r2/w4 via querybuilder+repository [apps/core-api/src/modules/friend/friend.service.ts:233, apps/core-api/src/modules/friend/friend.service.ts:250]

### table: conversation_streaks

Streak trò chuyện — 1:1 với `Conversation` (docs/services/streak-service.md). Tính on-write trong transaction `sendMessage` (khoá row này FOR UPDATE — xem `StreakService.recordActivity`); KHÔNG có job nào khác được ghi vào bảng này ngoài `recordActivity` (cron warning chỉ đọc + ghi `lastWarningSentAt`, không bao giờ đổi `currentStreak`/`longestStreak`). Ngày lưu dạng `date` (không giờ) — luôn là NGÀY UTC theo giờ server, không phải giờ địa phương của client (docs/06: chống spoof + tránh mơ hồ giữa 2 user khác múi giờ).

Entity: ConversationStreak (apps/core-api/src/modules/friend/entities/conversation-streak.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| conversation_id | uuid |  |  | PK FK→conversations.id |  |
| current_streak | integer |  | 0 |  |  |
| longest_streak | integer |  | 0 |  |  |
| user_low_last_active_date | date | null |  |  | Ngày UTC gần nhất `userLowId` gửi message trong conversation này — tăng đơn điệu. |
| user_high_last_active_date | date | null |  |  | Ngày UTC gần nhất `userHighId` gửi message — tăng đơn điệu. |
| last_confirmed_date | date | null |  |  | Ngày UTC gần nhất streak được xác nhận tăng (cả 2 chiều cùng nhắn trong ngày đó). |
| grace_used_for_date | date | null |  |  | Ngày bị lỡ đã được grace cứu — audit/idempotency, KHÔNG phải bộ đếm số lần dùng còn lại. |
| last_warning_sent_at | timestamp with time zone | null |  |  | Idempotency cho cron cảnh báo "sắp mất" — 1 cảnh báo/ngày UTC/conversation. |

Constraints: check chk_conversation_streaks_nonneg (((current_streak >= 0) AND (longest_streak >= 0)))

Access: friend(owner) r2/w3/lock1 via manager.querybuilder+manager.save(instance)+querybuilder+raw-sql+repository [apps/core-api/src/modules/friend/services/streak.service.ts:61, apps/core-api/src/modules/friend/services/streak.service.ts:65]

### table: conversations

Chat 1-1 lâu dài giữa 2 user ĐÃ LÀ BẠN — khác chat ẩn danh tạm thời của Soul Match (docs/02). Cặp canonical `userLowId < userHighId` giống hệt `Friendship`, được tạo ATOMICALLY cùng Friendship (`FriendService.ensureFriendship`) — bất biến: tồn tại Friendship cho 1 cặp ⇒ tồn tại Conversation cho đúng cặp đó; profile direct chat cũng có thể tạo Conversation trước Friendship (docs/services/friend-service.md § 1).

Entity: Conversation (apps/core-api/src/modules/friend/entities/conversation.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | uuid |  | gen_random_uuid() | PK |  |
| user_low_id | uuid |  |  | FK→users.id |  |
| user_high_id | uuid |  |  | FK→users.id |  |
| last_message_at | timestamp with time zone | null |  |  | Chỉ dùng để sort danh sách chat gần nhất ở GET /friends — không phải nguồn sự thật gì khác. |
| created_at | timestamp with time zone |  | now() |  |  |
| updated_at | timestamp with time zone |  | now() |  |  |

Constraints: check chk_conversations_canonical ((user_low_id < user_high_id)); unique uq_conversations_pair (user_low_id,user_high_id)

Indexes: idx_conversations_user_high (user_high_id); UNIQUE uq_conversations_pair (user_low_id, user_high_id)

Access: friend(owner) r8/w5 via manager+querybuilder+repository [apps/core-api/src/modules/friend/friend.service.ts:125, apps/core-api/src/modules/friend/friend.service.ts:134]

### table: friendships

Quan hệ bạn bè 2 CHIỀU lưu đúng 1 dòng theo cặp canonical `userLowId < userHighId` (so sánh chuỗi uuid) — unique DB trên cặp là chốt chặn cuối chống tạo đôi khi 2 rating "like" chạy song song (docs/services/soul-match-service.md § 3). Đây cũng là nguồn sự thật của "unlock profile".

Entity: Friendship (apps/core-api/src/modules/friend/entities/friendship.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | uuid |  | gen_random_uuid() | PK |  |
| user_low_id | uuid |  |  | FK→users.id |  |
| user_high_id | uuid |  |  | FK→users.id |  |
| source | character varying(16) |  |  |  |  |
| created_at | timestamp with time zone |  | now() |  |  |
| updated_at | timestamp with time zone |  | now() |  |  |

Constraints: check chk_friendships_canonical ((user_low_id < user_high_id)); check chk_friendships_source (((source)::text = ANY ((ARRAY['soul_match'::character varying, 'voice_match'::character varying, 'palm_match'::character varying, 'movie_match'::character var…; unique uq_friendships_pair (user_low_id,user_high_id)

Indexes: idx_friendships_user_high (user_high_id); UNIQUE uq_friendships_pair (user_low_id, user_high_id)

Access: friend(owner) r3/w1 via querybuilder+raw-sql+repository [apps/core-api/src/modules/friend/friend.service.ts:117, apps/core-api/src/modules/friend/friend.service.ts:184]

### table: messages

Message chat 1-1 lâu dài — APPEND-ONLY (không updatedAt, không extend BaseAppEntity), KHÔNG ẩn danh (2 bên đã unlock profile khi thành bạn — khác SoulChatMessage (docs/services/friend-service.md § 2).

Entity: Message (apps/core-api/src/modules/friend/entities/message.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | uuid |  | gen_random_uuid() | PK |  |
| seq | bigint |  |  |  | Thứ tự tăng dần DB cấp (bigint identity) cho cursor keyset — không dùng createdAt làm cursor (2 message cùng mili-giây làm trùng/mất dòng khi phân trang). |
| conversation_id | uuid |  |  | FK→conversations.id |  |
| sender_user_id | uuid |  |  | FK→users.id |  |
| content | text |  |  |  |  |
| idempotency_key | character varying(255) |  |  | UQ | Prefix `friend:msg:{userId}:{key}` — unique DB, client retry không nhân đôi. |
| created_at | timestamp with time zone |  | now() |  |  |
| attachment | jsonb | null |  |  | NULL cho message thường. HTTP chỉ set được `kind='image'` (whitelist ở FriendController — client gửi `imageAssetId`, controller resolve thành cloud URL); các kind khác (vd `story_reply`) vẫn chỉ modu… |

Constraints: unique uq_messages_idempotency_key (idempotency_key)

Indexes: idx_messages_conversation_created (conversation_id, created_at); idx_messages_conversation_seq (conversation_id, seq); UNIQUE uq_messages_idempotency_key (idempotency_key)

Access: friend(owner) r4/w3 via querybuilder+raw-sql+repository [apps/core-api/src/modules/friend/friend.service.ts:143, apps/core-api/src/modules/friend/services/conversation.service.ts:104]

### table: profile_chat_contacts

Lần đầu một user mở chat trực tiếp với profile. Một cặp chỉ được ghi một lần; ngày này là ngày UTC để đếm người bắt chuyện mới trong ngày của profile.

Entity: ProfileChatContact (apps/core-api/src/modules/friend/entities/profile-chat-contact.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | uuid |  | gen_random_uuid() | PK |  |
| profile_user_id | uuid |  |  | FK→users.id |  |
| requester_user_id | uuid |  |  | FK→users.id |  |
| first_contact_date | date |  |  |  |  |
| created_at | timestamp with time zone |  | now() |  |  |
| updated_at | timestamp with time zone |  | now() |  |  |

Constraints: check chk_profile_chat_contacts_not_self ((profile_user_id <> requester_user_id)); unique uq_profile_chat_contacts_pair (profile_user_id,requester_user_id)

Indexes: idx_profile_chat_contacts_profile_date (profile_user_id, first_contact_date); UNIQUE uq_profile_chat_contacts_pair (profile_user_id, requester_user_id)

Access: friend(owner) r1/w1 via querybuilder [apps/core-api/src/modules/friend/services/profile-social.service.ts:531, apps/core-api/src/modules/friend/services/profile-social.service.ts:608]

### table: profile_follows

Quan hệ theo dõi một chiều trên profile, độc lập với popularity gate của chat.

Entity: ProfileFollow (apps/core-api/src/modules/friend/entities/profile-follow.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | uuid |  | gen_random_uuid() | PK |  |
| follower_user_id | uuid |  |  | FK→users.id |  |
| followee_user_id | uuid |  |  | FK→users.id |  |
| active | boolean |  | true |  |  |
| last_followed_at | timestamp with time zone |  | now() |  | Thời điểm follow gần nhất để hiển thị/audit trạng thái theo dõi. |
| created_at | timestamp with time zone |  | now() |  |  |
| updated_at | timestamp with time zone |  | now() |  |  |

Constraints: check chk_profile_follows_not_self ((follower_user_id <> followee_user_id)); unique uq_profile_follows_pair (follower_user_id,followee_user_id)

Indexes: idx_profile_follows_followee_daily (followee_user_id, active, last_followed_at); idx_profile_follows_follower_recent (follower_user_id, active, last_followed_at); UNIQUE uq_profile_follows_pair (follower_user_id, followee_user_id)

Access: friend(owner) r8/w3 via querybuilder+raw-sql+repository [apps/core-api/src/modules/friend/services/profile-social.service.ts:92, apps/core-api/src/modules/friend/services/profile-social.service.ts:116]

