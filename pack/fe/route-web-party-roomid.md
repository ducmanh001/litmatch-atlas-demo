# web route /party/:roomId

id: fe:route:web:/party/:roomId
file: apps/web/src/app/(app)/party/[roomId]/page.tsx
layout: app (login required: AppChromeWithAuth)
title: Phòng nhóm
reached source files: 26 (static import closure); data features: party-room

Access: every route in the (app) group renders inside AppChromeWithAuth (AuthGate): no session -> redirected to /login. Guests can log in with a device id but several actions are limited (see "Guest and capability gating").

## Data it reads (GET)

| endpoint | hook | query key | polling | stale time | module | defined at |
|---|---|---|---|---|---|---|
| GET /users/me | useCurrentUser | currentUserKey |  | Infinity | [user](../api/user.md) | apps/web/src/shared/auth/use-current-user.ts:20 |
| GET /party/rooms/{id} | useRoomDetail | partyRoomKeys.detail(roomId) | (query) => partyRoomRefetchInterval( query.state.data?.room.status, realtimeConnected, ) |  | [party-room](../api/party-room.md) | apps/web/src/features/party-room/api.ts:107 |
| GET /gifts | useGiftCatalog | ['gifts', 'catalog'] as const |  |  | [gift](../api/gift.md) | apps/web/src/features/party-room/api.ts:287 |
| GET /party/rooms/{id}/comments | useRoomComments | partyRoomKeys.comments(roomId) | realtimeConnected ? PARTY_ROOM_HEALTHY_REFETCH_INTERVAL_MS : PARTY_ROOM_DETAIL_REFETCH_INTERVAL_MS |  | [party-room](../api/party-room.md) | apps/web/src/features/party-room/api.ts:157 |

## Actions that change data (mutations)

| UI element and label | handler | hook | endpoint | module | call site |
|---|---|---|---|---|---|
| button.onClick: (no literal label) | handleLeaveRoom | useLeaveRoom | POST /party/rooms/{id}/leave | [party-room](../api/party-room.md) | apps/web/src/features/party-room/components/party-stage.tsx:232 |
| button.onClick: (no literal label) | () => acceptSpeakerInvite.mutate() | useAcceptSpeakerInvite | POST /party/rooms/{id}/speaker-invite/accept | [party-room](../api/party-room.md) | apps/web/src/features/party-room/components/party-stage.tsx:365 |
| button.onClick: (no literal label) | () => declineSpeakerInvite.mutate() | useDeclineSpeakerInvite | POST /party/rooms/{id}/speaker-invite/decline | [party-room](../api/party-room.md) | apps/web/src/features/party-room/components/party-stage.tsx:377 |
| button.onClick: (no literal label) | () => sendGift.mutate( { giftId: gift.id, receiverUserId, idempotencyKey: key }, { onSuccess: () => { resetKey(); // lay | useSendGift | POST /party/rooms/{roomId}/gifts | [gift](../api/gift.md) | apps/web/src/features/party-room/components/gift-panel.tsx:76 |
| button.onClick: Chuyển xuống khán giả | () => changeRole.mutate({ userId: speaker.userId, role: 'audience', }) | useChangeRole | POST /party/rooms/{id}/members/{userId}/role | [party-room](../api/party-room.md) | apps/web/src/features/party-room/components/member-list.tsx:124 |
| button.onClick: (no literal label) | () => inviteSpeaker.mutate(member.userId) | useInviteSpeaker | POST /party/rooms/{id}/members/{userId}/speaker-invite | [party-room](../api/party-room.md) | apps/web/src/features/party-room/components/member-list.tsx:178 |
| form.onSubmit: (no literal label) | submit | useSendRoomComment | POST /party/rooms/{id}/comments | [party-room](../api/party-room.md) | apps/web/src/features/party-room/components/party-chat.tsx:113 |

Other write endpoints reachable (called outside a detected .mutate): POST /party/rooms/{id}/join (useJoinRoom apps/web/src/features/party-room/api.ts:124)

## Interactive elements (12)

- button.onClick (no literal label) -> media.connect (apps/web/src/features/party-room/components/party-stage.tsx:313)
- button.onClick (no literal label) -> handleLeaveRoom (apps/web/src/features/party-room/components/party-stage.tsx:337)
- button.onClick (no literal label) -> () => acceptSpeakerInvite.mutate() (apps/web/src/features/party-room/components/party-stage.tsx:359)
- button.onClick (no literal label) -> () => declineSpeakerInvite.mutate() (apps/web/src/features/party-room/components/party-stage.tsx:371)
- button.onClick Kết nối lại -> media.connect (apps/web/src/features/party-room/components/party-stage.tsx:394)
- button.onClick micOn ? 'Tắt mic' : 'Bật mic' -> toggleMic (apps/web/src/features/party-room/components/party-stage.tsx:428)
- button.onClick isGiftPanelOpen ? 'Đóng bảng quà tặng' : 'Tặng quà' -> () => setIsGiftPanelOpen((open) => !open) (apps/web/src/features/party-room/components/party-stage.tsx:452)
- button.onClick (no literal label) -> () => sendGift.mutate( { giftId: gift.id, receiverUserId, idempotency… (apps/web/src/features/party-room/components/gift-panel.tsx:70)
- button.onClick Chuyển xuống khán giả -> () => changeRole.mutate({ userId: speaker.userId, role: 'audience', }) (apps/web/src/features/party-room/components/member-list.tsx:119)
- button.onClick (no literal label) -> () => inviteSpeaker.mutate(member.userId) (apps/web/src/features/party-room/components/member-list.tsx:172)
- button.onClick (no literal label) -> () => void comments.fetchNextPage() (apps/web/src/features/party-room/components/party-chat.tsx:153)
- form.onSubmit (no literal label) -> submit (apps/web/src/features/party-room/components/party-chat.tsx:271)

## Realtime events listened

- party.member.joined (apps/web/src/features/party-room/components/party-stage.tsx:102)
- party.member.left (apps/web/src/features/party-room/components/party-stage.tsx:108)
- party.member.disconnected (apps/web/src/features/party-room/components/party-stage.tsx:114)
- party.member.reconnected (apps/web/src/features/party-room/components/party-stage.tsx:120)
- party.role.changed (apps/web/src/features/party-room/components/party-stage.tsx:126)
- party.speaker.invite.received (apps/web/src/features/party-room/components/party-stage.tsx:132)
- party.room.closed (apps/web/src/features/party-room/components/party-stage.tsx:138)
- party.host.disconnected (apps/web/src/features/party-room/components/party-stage.tsx:165)
- party.host.reconnected (apps/web/src/features/party-room/components/party-stage.tsx:171)
- gift.sent (apps/web/src/features/party-room/components/party-stage.tsx:177)
- (dynamic: event) (apps/web/src/shared/realtime/use-realtime-event.ts:17)
- party.comment.created (apps/web/src/features/party-room/components/party-chat.tsx:75)

## Navigation destinations

- /party via link (apps/web/src/features/party-room/components/party-stage.tsx:290)

## Error codes handled in the FE

none referenced by code string (errors are shown through generic ApiError message handling)

## Guest and capability gating

- no isGuest check
- no capability check

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
- apps/web/src/features/party-room/api.spec.ts
- apps/web/src/features/party-room/components/member-list.spec.tsx
- apps/web/src/features/party-room/components/party-chat.spec.tsx
- apps/web/src/features/party-room/components/party-stage.spec.tsx
- apps/web/src/features/party-room/components/room-list.spec.tsx
- apps/web/src/features/party-room/create-room-schema.spec.ts
- apps/web/src/features/party-room/hooks/use-party-room-media.spec.tsx

## Backend

modules: [user](../modules/user.md), [party-room](../modules/party-room.md), [gift](../modules/gift.md)

service-map flows touching these endpoints: [party-room-detail-and-comments](../flows/party-room-detail-and-comments.md), [party-room-speaker-invite](../flows/party-room-speaker-invite.md), [party-room-leave-and-role-change](../flows/party-room-leave-and-role-change.md), [admin-gift-catalog](../flows/admin-gift-catalog.md), [gift-in-party-room](../flows/gift-in-party-room.md), [party-room-lifecycle](../flows/party-room-lifecycle.md)
