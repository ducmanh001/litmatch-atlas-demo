# Feature slice: friend-chat-streak

id: slice:friend-chat-streak
status: implemented; owner: core-api/friend

Friendship, direct chat and streak

This file follows ONE feature through every layer. Layers are joined by the actual endpoint calls found in the frontend code, so it shows what the code does, not what a plan says.

## 1. Product contract and docs

- registry contracts: openapi/core-api.json, docs/services/friend-service.md, docs/services/streak-service.md
- registry verification tests: apps/core-api/src/modules/friend/friend.integration.spec.ts
- service docs: docs/services/friend-service.md

## 2. Frontend

| route | app | endpoints of this feature it calls | page |
|---|---|---|---|
| [/chat/:friendUserId](../fe/route-web-chat-frienduserid.md) | web | GET /friends; GET /friends/{friendUserId}/conversation; GET /conversations/{id}/messages; POST /conversations/{id}/read; POST /conversations/{id}/mute; POST /conversations/{id}/messages | app/(app)/chat/[friendUserId]/page.tsx |
| [/users/:id/followers](../fe/route-web-users-id-followers.md) | web | GET /profiles/{profileUserId}/follow-counts; GET /profiles/{profileUserId}/followers; GET /profiles/{profileUserId}/following; POST /profiles/{profileUserId}/follow; DELETE /profiles/{profileUserId}/follow | app/(app)/users/[id]/followers/page.tsx |
| [/users/:id/following](../fe/route-web-users-id-following.md) | web | GET /profiles/{profileUserId}/follow-counts; GET /profiles/{profileUserId}/followers; GET /profiles/{profileUserId}/following; POST /profiles/{profileUserId}/follow; DELETE /profiles/{profileUserId}/follow | app/(app)/users/[id]/following/page.tsx |
| [/users/:id](../fe/route-web-users-id.md) | web | GET /profiles/{profileUserId}/actions; POST /profiles/{profileUserId}/follow; DELETE /profiles/{profileUserId}/follow; POST /profiles/{profileUserId}/conversation | app/(app)/users/[id]/page.tsx |
| [/friends](../fe/route-web-friends.md) | web | GET /friends/connections; GET /friends; POST /profiles/{profileUserId}/conversation | app/(app)/friends/page.tsx |
| [/movie-match/:sessionId](../fe/route-web-movie-match-sessionid.md) | web | GET /friends/{friendUserId}/conversation; GET /conversations/{id}/messages; POST /conversations/{id}/messages | app/(app)/movie-match/[sessionId]/page.tsx |
| [/profile](../fe/route-web-profile.md) | web | GET /friends/connections; GET /profiles/{profileUserId}/follow-counts | app/(app)/profile/page.tsx |
| [/messages](../fe/route-web-messages.md) | web | GET /friends | app/(app)/messages/page.tsx |
| [/movie-match](../fe/route-web-movie-match.md) | web | GET /friends | app/(app)/movie-match/page.tsx |

FE features (directories): [web/friend-chat](../fe/feature-web-friend-chat.md), [web/voice-match](../fe/feature-web-voice-match.md), [web/profile](../fe/feature-web-profile.md), [web/feed](../fe/feature-web-feed.md), [web/movie-match](../fe/feature-web-movie-match.md), [web/notification](../fe/feature-web-notification.md), [web/wallet](../fe/feature-web-wallet.md)

UI action map card for /chat/:friendUserId: : Gửi text / ảnh / Mark read / mute / Gọi voice → /call / Movie Match với bạn / Notification khi có message

User actions that mutate this feature's data:
- useMuteConversation -> POST /conversations/{id}/mute (apps/web/src/features/friend-chat/components/conversation-thread.tsx:169)
- useSendFriendMessage -> POST /conversations/{id}/messages (apps/web/src/features/friend-chat/components/message-composer.tsx:63)
- useFollowProfile -> POST /profiles/{profileUserId}/follow, DELETE /profiles/{profileUserId}/follow (apps/web/src/features/profile/components/follow-list-view.tsx:37)

Realtime events the frontend listens to for this feature:
- friend.message in /chat/:friendUserId (apps/web/src/features/friend-chat/components/conversation-thread.tsx:64)

## 3. API

| endpoint | auth | idempotency | throttle | FE callers |
|---|---|---|---|---|
| GET /profiles/{profileUserId}/follow-counts ([friend](../api/friend.md)) | JWT |  |  | 0 |
| GET /profiles/{profileUserId}/followers ([friend](../api/friend.md)) | JWT |  |  | 1 |
| GET /profiles/{profileUserId}/following ([friend](../api/friend.md)) | JWT |  |  | 1 |
| GET /profiles/{profileUserId}/actions ([friend](../api/friend.md)) | JWT |  |  | 0 |
| POST /profiles/{profileUserId}/follow ([friend](../api/friend.md)) | JWT |  |  | 0 |
| DELETE /profiles/{profileUserId}/follow ([friend](../api/friend.md)) | JWT |  |  | 0 |
| POST /profiles/{profileUserId}/conversation ([friend](../api/friend.md)) | JWT |  |  | 0 |
| GET /friends ([friend](../api/friend.md)) | JWT |  |  | 1 |
| GET /friends/connections ([friend](../api/friend.md)) | JWT |  |  | 1 |
| GET /friends/{friendUserId}/conversation ([friend](../api/friend.md)) | JWT |  |  | 0 |
| GET /conversations/{id}/messages ([friend](../api/friend.md)) | JWT |  |  | 1 |
| GET /conversations/{id}/streak ([friend](../api/friend.md)) | JWT |  |  | 0 |
| POST /conversations/{id}/read ([friend](../api/friend.md)) | JWT |  |  | 1 |
| POST /conversations/{id}/mute ([friend](../api/friend.md)) | JWT |  |  | 1 |
| POST /conversations/{id}/messages ([friend](../api/friend.md)) | JWT | required |  | 1 |


## 4. Backend flows (service map)

- [friend-list-and-conversation-reads](../flows/friend-list-and-conversation-reads.md): Danh sách bạn, đọc tin và streak (5 endpoints of this feature)
- [friend-follow-lists-and-connections](../flows/friend-follow-lists-and-connections.md): Danh sách follower/following và danh sách bạn bè (4 endpoints of this feature)
- [friend-profile-follow-and-actions](../flows/friend-profile-follow-and-actions.md): Theo dõi và xem hành động khả dụng trên hồ sơ (3 endpoints of this feature)
- [friend-open-conversation-from-profile](../flows/friend-open-conversation-from-profile.md): Mở chat từ hồ sơ trước khi là bạn (2 endpoints of this feature)
- [message-push-notification](../flows/message-push-notification.md): Tin nhắn bạn bè: realtime và Web Push (1 endpoints of this feature)

## 5. Data

- tables owned by the module(s): `conversation_member_states`, `conversation_streaks`, `conversations`, `friendships`, `messages`, `profile_chat_contacts`, `profile_follows` (see ../db/friend.md)
- status vocabularies enforced by CHECK:
  - friendships: soul_match | voice_match | palm_match | movie_match

## 6. Events

- friend.message: producers friend.sendMessage; FE consumers 2
- friend.streak.increased: producers friend.sendMessage; FE consumers 0
- friend.streak.at_risk: producers friend.warnOne; FE consumers 0

## 7. Errors

- error code groups: [FriendErrors](../errors/frienderrors.md), [ProfileSocialErrors](../errors/profilesocialerrors.md)
- codes the FE references for this feature: none

## 8. Configuration

env vars read by the module(s): `FRIEND_MESSAGE_MAX_LENGTH`, `STREAK_WARNING_CHECK_INTERVAL_MS`, `REDIS_URL`, `PROFILE_DIRECT_MESSAGE_DAILY_FIRST_CHAT_THRESHOLD`, `STREAK_WARNING_HOURS`, `STREAK_MILESTONE_DAYS` (see ../config/)

## 9. Tests

- friend: 4 unit spec files, 1 integration spec files
- e2e files that call these endpoints: none
- FE specs: apps/web/src/features/friend-chat/api.spec.ts, apps/web/src/features/friend-chat/components/conversation-inbox.spec.tsx, apps/web/src/features/friend-chat/components/conversation-thread.spec.tsx, apps/web/src/features/friend-chat/components/friends-directory.spec.tsx, apps/web/src/features/friend-chat/components/message-list.spec.tsx, apps/web/src/features/friend-chat/send-message-schema.spec.ts, apps/web/src/features/matching/api.spec.ts, apps/web/src/features/matching/components/incoming-invites.spec.tsx, apps/web/src/features/matching/components/match-actions.spec.tsx, apps/web/src/features/matching/components/queue-status-panel.spec.tsx, apps/web/src/features/matching/components/speedup-button.spec.tsx, apps/web/src/features/matching/join-queue-schema.spec.ts

## 10. Known issues (linked from the ledger)

- P065 [open] Production chạy bản cũ: route profile 404, thông báo Facebook cũ
- P006 [fixed] Swagger thiếu cursor pagination → client sinh kiểu never

See ../issues/index.md.
