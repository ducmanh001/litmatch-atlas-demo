# web route /chat/:friendUserId

id: fe:route:web:/chat/:friendUserId
file: apps/web/src/app/(app)/chat/[friendUserId]/page.tsx
layout: app (login required: AppChromeWithAuth)
title: Chat
reached source files: 26 (static import closure); data features: friend-chat, voice-match

Access: every route in the (app) group renders inside AppChromeWithAuth (AuthGate): no session -> redirected to /login. Guests can log in with a device id but several actions are limited (see "Guest and capability gating").

## UI action map card (human-authored, not verified against code)

CHAT /chat/:friendUserId: Từ Friendship / Profile / Friends

:
  - Gửi text / ảnh
  - Mark read / mute
  - Gọi voice → /call
  - Movie Match với bạn
  - Notification khi có message

## Data it reads (GET)

| endpoint | hook | query key | polling | stale time | module | defined at |
|---|---|---|---|---|---|---|
| GET /friends | useFriends | friendChatKeys.friends |  |  | [friend](../api/friend.md) | apps/web/src/features/friend-chat/api.ts:35 |
| GET /users/{id} | usePartnerProfile | friendChatKeys.partnerProfile(friendUserId) |  |  | [user](../api/user.md) | apps/web/src/features/friend-chat/api.ts:76 |
| GET /friends/{friendUserId}/conversation | useConversationWithFriend | friendChatKeys.conversation(friendUserId) |  |  | [friend](../api/friend.md) | apps/web/src/features/friend-chat/api.ts:62 |
| GET /users/me | useCurrentUser | currentUserKey |  | Infinity | [user](../api/user.md) | apps/web/src/shared/auth/use-current-user.ts:20 |
| GET /conversations/{id}/messages | useConversationMessages | friendChatKeys.messages(conversationId) | FRIEND_MESSAGES_REFETCH_INTERVAL_MS |  | [friend](../api/friend.md) | apps/web/src/features/friend-chat/api.ts:91 |

## Actions that change data (mutations)

| UI element and label | handler | hook | endpoint | module | call site |
|---|---|---|---|---|---|
| button.onClick: 🔕 | handleToggleMute | useMuteConversation | POST /conversations/{id}/mute | [friend](../api/friend.md) | apps/web/src/features/friend-chat/components/conversation-thread.tsx:169 |
| button.onClick: 🚫 Chặn người này | handleBlock | useBlockUser | POST /safety/blocks/{targetUserId} | [safety](../api/safety.md) | apps/web/src/features/friend-chat/components/conversation-thread.tsx:199 |
| button.onClick: ⚠️ Báo cáo | handleReport | useReportUser | POST /safety/reports | [safety](../api/safety.md) | apps/web/src/features/friend-chat/components/conversation-thread.tsx:226 |
| button.onClick: canCall ? 'Gọi voice' : 'Follow nhau để gọi' | handleCall | useJoinFriendCall | POST /calling/friends/{friendUserId}/join | [calling](../api/calling.md) | apps/web/src/features/friend-chat/components/conversation-thread.tsx:267 |
| (no matching UI element found; called from MessageComposer) |  | useSendFriendMessage | POST /conversations/{id}/messages | [friend](../api/friend.md) | apps/web/src/features/friend-chat/components/message-composer.tsx:63 |

Other write endpoints reachable (called outside a detected .mutate): POST /conversations/{id}/read (useMarkConversationRead apps/web/src/features/friend-chat/api.ts:140); POST /media/images/upload-intent (uploadImage apps/web/src/shared/media/image-upload.ts:31)

## Interactive elements (10)

- button.onClick 👤 Xem hồ sơ -> () => closeMenuThen(() => router.push(`/users/${partnerData.id}`)) (apps/web/src/features/friend-chat/components/conversation-thread.tsx:123)
- button.onClick 🔕 -> handleToggleMute (apps/web/src/features/friend-chat/components/conversation-thread.tsx:132)
- button.onClick 🚫 Chặn người này -> handleBlock (apps/web/src/features/friend-chat/components/conversation-thread.tsx:139)
- button.onClick ⚠️ Báo cáo -> handleReport (apps/web/src/features/friend-chat/components/conversation-thread.tsx:146)
- button.onClick (no literal label) -> () => { void reportUser .mutateAsync({ targetUserId: partnerData.id, … (apps/web/src/features/friend-chat/components/conversation-thread.tsx:221)
- button.onClick Quay lại tin nhắn -> onBack (apps/web/src/features/friend-chat/components/conversation-thread.tsx:313)
- button.onClick canCall ? 'Gọi voice' : 'Follow nhau để gọi' -> handleCall (apps/web/src/features/friend-chat/components/conversation-thread.tsx:348)
- button.onClick Tuỳ chọn cuộc trò chuyện -> openOptionsMenu (apps/web/src/features/friend-chat/components/conversation-thread.tsx:362)
- form.onSubmit (no literal label) -> onSubmit (apps/web/src/features/friend-chat/components/message-composer.tsx:83)
- button.onClick Đính kèm ảnh -> () => setShowImageInput((shown) => { if (shown) { setImageFile(null);… (apps/web/src/features/friend-chat/components/message-composer.tsx:85)

## Realtime events listened

- friend.message (apps/web/src/features/friend-chat/components/conversation-thread.tsx:64)
- (dynamic: event) (apps/web/src/shared/realtime/use-realtime-event.ts:17)

## Navigation destinations

- /users/{param} via router.push (apps/web/src/features/friend-chat/components/conversation-thread.tsx:126)
- /messages via link (apps/web/src/features/friend-chat/components/conversation-thread.tsx:292)
- /chat/{param}/call via router.push (apps/web/src/features/friend-chat/components/conversation-thread.tsx:273)
- /wallet via link (apps/web/src/features/friend-chat/components/message-composer.tsx:118)

## Error codes handled in the FE

none referenced by code string (errors are shown through generic ApiError message handling)

## Guest and capability gating

- no isGuest check
- no capability check

## Form validation (zod)

- joinQueueSchema (apps/web/src/features/matching/join-queue-schema.ts:9): matchType=z.enum(['soul', 'voice']); useDiamond=z.boolean(); genderPreference=z.enum(['any', 'male', 'female'])

## Browser storage and cookies

- staleTime Infinity (apps/web/src/shared/auth/use-current-user.ts:25)
- cookie-write  (apps/web/src/shared/i18n/locale-store.ts:37)

## i18n keys used (0)



## Tests

- apps/web/src/features/friend-chat/api.spec.ts
- apps/web/src/features/friend-chat/components/conversation-inbox.spec.tsx
- apps/web/src/features/friend-chat/components/conversation-thread.spec.tsx
- apps/web/src/features/friend-chat/components/friends-directory.spec.tsx
- apps/web/src/features/friend-chat/components/message-list.spec.tsx
- apps/web/src/features/friend-chat/send-message-schema.spec.ts
- apps/web/src/features/matching/api.spec.ts
- apps/web/src/features/matching/components/incoming-invites.spec.tsx
- apps/web/src/features/matching/components/match-actions.spec.tsx
- apps/web/src/features/matching/components/queue-status-panel.spec.tsx
- apps/web/src/features/matching/components/speedup-button.spec.tsx
- apps/web/src/features/matching/join-queue-schema.spec.ts
- apps/web/src/features/voice-match/api.spec.ts
- apps/web/src/features/voice-match/components/voice-call-room.spec.tsx
- apps/web/src/features/voice-match/hooks/use-call-room.spec.tsx

## Backend

modules: [friend](../modules/friend.md), [user](../modules/user.md), [safety](../modules/safety.md), [calling](../modules/calling.md), [media](../modules/media.md)

service-map flows touching these endpoints: [friend-list-and-conversation-reads](../flows/friend-list-and-conversation-reads.md), [feed-post-with-image](../flows/feed-post-with-image.md), [friend-direct-call](../flows/friend-direct-call.md), [friend-open-conversation-from-profile](../flows/friend-open-conversation-from-profile.md), [message-push-notification](../flows/message-push-notification.md), [safety-block-and-unblock](../flows/safety-block-and-unblock.md), [safety-report-and-trust-score](../flows/safety-report-and-trust-score.md)
