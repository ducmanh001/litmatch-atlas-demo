# web route /chat/:friendUserId/call

id: fe:route:web:/chat/:friendUserId/call
file: apps/web/src/app/(app)/chat/[friendUserId]/call/page.tsx
layout: app (login required: AppChromeWithAuth)
title: Cuộc gọi
reached source files: 17 (static import closure); data features: friend-chat, voice-match

Access: every route in the (app) group renders inside AppChromeWithAuth (AuthGate): no session -> redirected to /login. Guests can log in with a device id but several actions are limited (see "Guest and capability gating").

## Data it reads (GET)

| endpoint | hook | query key | polling | stale time | module | defined at |
|---|---|---|---|---|---|---|
| GET /users/{id} | usePartnerProfile | friendChatKeys.partnerProfile(friendUserId) |  |  | [user](../api/user.md) | apps/web/src/features/friend-chat/api.ts:76 |
| GET /calling/calls/{id} | useCall | voiceMatchKeys.call(callId ?? 'none') | (query) => isActiveCallStatus(query.state.data?.status) ? VOICE_CALL_REFETCH_INTERVAL_MS : false |  | [calling](../api/calling.md) | apps/web/src/features/voice-match/api.ts:74 |

## Actions that change data (mutations)

| UI element and label | handler | hook | endpoint | module | call site |
|---|---|---|---|---|---|
| button.onClick: Kết thúc cuộc gọi | handleEndCall | useEndCall | POST /calling/calls/{id}/end | [calling](../api/calling.md) | apps/web/src/features/friend-chat/components/friend-call-room.tsx:140 |

Other write endpoints reachable (called outside a detected .mutate): POST /calling/match-sessions/{matchSessionId}/join (useJoinCall apps/web/src/features/voice-match/api.ts:28); POST /calling/friends/{friendUserId}/join (useJoinFriendCall apps/web/src/features/voice-match/api.ts:51)

## Interactive elements (5)

- button.onClick Quay lại tin nhắn -> () => router.replace(`/chat/${friendUserId}`) (apps/web/src/features/friend-chat/components/friend-call-room.tsx:166)
- button.onClick Thử kết nối lại -> retry (apps/web/src/features/friend-chat/components/friend-call-room.tsx:199)
- button.onClick Kết nối lại -> retry (apps/web/src/features/friend-chat/components/friend-call-room.tsx:234)
- button.onClick micIsMuted ? 'Bật mic' : 'Tắt mic' -> toggleMute (apps/web/src/features/friend-chat/components/friend-call-room.tsx:244)
- button.onClick Kết thúc cuộc gọi -> handleEndCall (apps/web/src/features/friend-chat/components/friend-call-room.tsx:252)

## Realtime events listened

- call.ended (apps/web/src/features/friend-chat/components/friend-call-room.tsx:94)
- (dynamic: event) (apps/web/src/shared/realtime/use-realtime-event.ts:17)

## Navigation destinations

- /chat/{param} via router.replace (apps/web/src/features/friend-chat/components/friend-call-room.tsx:169)

## Error codes handled in the FE

none referenced by code string (errors are shown through generic ApiError message handling)

## Guest and capability gating

- no isGuest check
- no capability check

## Form validation (zod)

- joinQueueSchema (apps/web/src/features/matching/join-queue-schema.ts:9): matchType=z.enum(['soul', 'voice']); useDiamond=z.boolean(); genderPreference=z.enum(['any', 'male', 'female'])

## Browser storage and cookies

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

modules: [user](../modules/user.md), [calling](../modules/calling.md)

service-map flows touching these endpoints: [matching-to-call](../flows/matching-to-call.md), [calling-end-and-ticker](../flows/calling-end-and-ticker.md), [friend-direct-call](../flows/friend-direct-call.md)
