# Module friend

id: module:friend
kind: core-api module
code: apps/core-api/src/modules/friend

Sở hữu Friendship, Conversation/Message chat 1-1 lâu dài, trạng thái đọc/mute theo thành viên, ConversationStreak (sub-service nội bộ), ProfileFollow và ProfileChatContact (cổng first-contact của chat trực tiếp từ profile). KHÔNG sở hữu User/profile, block (Safety), ví/ledger (Economy), notification hay hạ tầng realtime; các flow match (Soul/Voice/Movie/Palm) chỉ tạo bạn qua FriendService.ensureFriendship.

## Facts

- tables owned: conversation_member_states, conversation_streaks, conversations, friendships, messages, profile_chat_contacts, profile_follows
- touches tables of other modules: users (owner user; read 3, write 0)
- API: 15 endpoints, 0 public, 0 admin-only, 1 require Idempotency-Key, 0 with own throttle
- publishes realtime: friend.message, friend.streak.increased, friend.streak.at_risk
- jobs: StreakWarningJob
- env vars read: FRIEND_MESSAGE_MAX_LENGTH, STREAK_WARNING_CHECK_INTERVAL_MS, REDIS_URL, PROFILE_DIRECT_MESSAGE_DAILY_FIRST_CHAT_THRESHOLD, STREAK_WARNING_HOURS, STREAK_MILESTONE_DAYS
- error codes: 9 in FriendErrors, ProfileSocialErrors
- tests: 4 unit spec files, 1 integration spec files
- history: 20 commits (2 fixes), last 2026-10-10
- docs: docs/services/friend-service.md (last changed 2026-10-10)
- features: friend-chat-streak

## Where to read next

- tables: ../db/friend.md
- API contract: ../api/friend.md
- why it talks to other services: ../service-map/friend.md and ../service-map/friend-techniques.md
- error codes: ../errors/frienderrors.md, ../errors/profilesocialerrors.md
- flows involving it: [calling-like-after-call](../flows/calling-like-after-call.md), [feed-comment-with-notification](../flows/feed-comment-with-notification.md), [feed-delete-post](../flows/feed-delete-post.md), [feed-reaction-with-notification](../flows/feed-reaction-with-notification.md), [feed-read-posts](../flows/feed-read-posts.md), [friend-direct-call](../flows/friend-direct-call.md), [friend-follow-lists-and-connections](../flows/friend-follow-lists-and-connections.md), [friend-list-and-conversation-reads](../flows/friend-list-and-conversation-reads.md), [friend-open-conversation-from-profile](../flows/friend-open-conversation-from-profile.md), [friend-profile-follow-and-actions](../flows/friend-profile-follow-and-actions.md), [friend-streak-warning-job](../flows/friend-streak-warning-job.md), [gift-to-profile-chat-gate](../flows/gift-to-profile-chat-gate.md), [message-push-notification](../flows/message-push-notification.md), [mini-game-session](../flows/mini-game-session.md), [movie-match-anonymous](../flows/movie-match-anonymous.md), [movie-match-friend-session](../flows/movie-match-friend-session.md), [palm-match-queue-and-rating](../flows/palm-match-queue-and-rating.md), [short-video-browse-and-detail](../flows/short-video-browse-and-detail.md), [soul-match-room-reads-and-reveal](../flows/soul-match-room-reads-and-reveal.md), [soul-match-send-message](../flows/soul-match-send-message.md), [soul-match-session](../flows/soul-match-session.md), [story-reply-to-dm](../flows/story-reply-to-dm.md), [story-ring-view-and-viewers](../flows/story-ring-view-and-viewers.md)

## Linked issues (heuristic, public subset)

- P006 [fixed] Swagger thiếu cursor pagination → client sinh kiểu never

See ../issues/index.md.
