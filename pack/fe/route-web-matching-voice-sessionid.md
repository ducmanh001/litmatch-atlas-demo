# web route /matching/voice/:sessionId

id: fe:route:web:/matching/voice/:sessionId
file: apps/web/src/app/(app)/matching/voice/[sessionId]/page.tsx
layout: app (login required: AppChromeWithAuth)
title: Voice Match
reached source files: 18 (static import closure); data features: voice-match

Access: every route in the (app) group renders inside AppChromeWithAuth (AuthGate): no session -> redirected to /login. Guests can log in with a device id but several actions are limited (see "Guest and capability gating").

## Data it reads (GET)

| endpoint | hook | query key | polling | stale time | module | defined at |
|---|---|---|---|---|---|---|
| GET /calling/calls/{id} | useCall | voiceMatchKeys.call(callId ?? 'none') | (query) => isActiveCallStatus(query.state.data?.status) ? VOICE_CALL_REFETCH_INTERVAL_MS : false |  | [calling](../api/calling.md) | apps/web/src/features/voice-match/api.ts:74 |

## Actions that change data (mutations)

| UI element and label | handler | hook | endpoint | module | call site |
|---|---|---|---|---|---|
| (no matching UI element found; called from refreshLikeAndOpenChat) |  | useLikeCall | POST /calling/calls/{id}/like | [calling](../api/calling.md) | apps/web/src/features/voice-match/components/voice-call-room.tsx:249 |
| button.onClick: (no literal label) | handleEndCall | useEndCall | POST /calling/calls/{id}/end | [calling](../api/calling.md) | apps/web/src/features/voice-match/components/voice-call-room.tsx:268 |
| button.onClick: (no literal label) | handleLikeCall | useLikeCall | POST /calling/calls/{id}/like | [calling](../api/calling.md) | apps/web/src/features/voice-match/components/voice-call-room.tsx:284 |

Other write endpoints reachable (called outside a detected .mutate): POST /calling/match-sessions/{matchSessionId}/end (useEndVoiceMatch apps/web/src/features/voice-match/api.ts:109); POST /calling/match-sessions/{matchSessionId}/join (useJoinCall apps/web/src/features/voice-match/api.ts:28); POST /calling/friends/{friendUserId}/join (useJoinFriendCall apps/web/src/features/voice-match/api.ts:51)

## Interactive elements (6)

- button.onClick (no literal label) -> handleEndedLikeCall (apps/web/src/features/voice-match/components/voice-call-room.tsx:332)
- button.onClick Thử kết nối lại -> connect (apps/web/src/features/voice-match/components/voice-call-room.tsx:407)
- button.onClick Kết nối lại -> connect (apps/web/src/features/voice-match/components/voice-call-room.tsx:469)
- button.onClick (no literal label) -> handleLikeCall (apps/web/src/features/voice-match/components/voice-call-room.tsx:488)
- button.onClick (no literal label) -> toggleMute (apps/web/src/features/voice-match/components/voice-call-room.tsx:509)
- button.onClick (no literal label) -> handleEndCall (apps/web/src/features/voice-match/components/voice-call-room.tsx:521)

## Realtime events listened

- call.ended (apps/web/src/features/voice-match/components/voice-call-room.tsx:200)
- (dynamic: event) (apps/web/src/shared/realtime/use-realtime-event.ts:17)

## Navigation destinations

- /home via link (apps/web/src/app/(app)/matching/voice/[sessionId]/page.tsx:19)
- /chat/{param} via router.replace (apps/web/src/features/voice-match/components/voice-call-room.tsx:245)
- /matching via link (apps/web/src/features/voice-match/components/voice-call-room.tsx:375)

## Error codes handled in the FE

- CALLING_CALL_ENDED at apps/web/src/features/voice-match/components/voice-call-room.tsx:91: (error.code === 'CALLING_CALL_ENDED' ||
- CALLING_SESSION_NOT_CALLABLE at apps/web/src/features/voice-match/components/voice-call-room.tsx:92: error.code === 'CALLING_SESSION_NOT_CALLABLE' ||
- CALLING_SESSION_NOT_FOUND at apps/web/src/features/voice-match/components/voice-call-room.tsx:93: error.code === 'CALLING_SESSION_NOT_FOUND')

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

modules: [calling](../modules/calling.md)

service-map flows touching these endpoints: [calling-end-and-ticker](../flows/calling-end-and-ticker.md), [matching-to-call](../flows/matching-to-call.md), [calling-like-after-call](../flows/calling-like-after-call.md), [friend-direct-call](../flows/friend-direct-call.md)
