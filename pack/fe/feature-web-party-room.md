# web feature party-room

id: fe:feature:web:party-room
directory: apps/web/src/features/party-room/ (11 source files, 8 components, 7 spec files)

routes whose data comes from this feature: [/feed](route-web-feed.md), [/home](route-web-home.md), [/party](route-web-party.md), [/party/:roomId](route-web-party-roomid.md), [/video](route-web-video.md)

## Hooks (data layer)

| hook | endpoint(s) | query key | polling | stale | invalidates on success | defined at |
|---|---|---|---|---|---|---|
| useRoomList | GET /party/rooms | [ ...partyRoomKeys.list, filters?.q ?? '', filters?.category ?? 'all', ] |  |  |  | apps/web/src/features/party-room/api.ts:55 |
| useCreateRoom | POST /party/rooms |  |  |  | invalidateQueries({ queryKey: partyRoomKeys.list }) | apps/web/src/features/party-room/api.ts:83 |
| useRoomDetail | GET /party/rooms/{id} | partyRoomKeys.detail(roomId) | (query) => partyRoomRefetchInterval( query.state.data?.room.status, realtimeConnected, ) |  |  | apps/web/src/features/party-room/api.ts:102 |
| useJoinRoom | POST /party/rooms/{id}/join |  |  |  | setQueryData(partyRoomKeys.detail(roomId)); invalidateQueries({ queryKey: partyRoomKeys.list }) | apps/web/src/features/party-room/api.ts:120 |
| useRoomComments | GET /party/rooms/{id}/comments | partyRoomKeys.comments(roomId) | realtimeConnected ? PARTY_ROOM_HEALTHY_REFETCH_INTERVAL_MS : PARTY_ROOM_DETAIL_REFETCH_INTERVAL_MS |  |  | apps/web/src/features/party-room/api.ts:152 |
| useSendRoomComment | POST /party/rooms/{id}/comments |  |  |  |  | apps/web/src/features/party-room/api.ts:173 |
| useLeaveRoom | POST /party/rooms/{id}/leave |  |  |  | invalidateQueries({ queryKey: partyRoomKeys.detail(roomId), }); invalidateQueries({ queryKey: partyRoomKeys.list }) | apps/web/src/features/party-room/api.ts:188 |
| useChangeRole | POST /party/rooms/{id}/members/{userId}/role |  |  |  | invalidateQueries({ queryKey: partyRoomKeys.detail(roomId), }) | apps/web/src/features/party-room/api.ts:205 |
| useInviteSpeaker | POST /party/rooms/{id}/members/{userId}/speaker-invite |  |  |  | invalidateQueries({ queryKey: partyRoomKeys.detail(roomId), }) | apps/web/src/features/party-room/api.ts:227 |
| useAcceptSpeakerInvite | POST /party/rooms/{id}/speaker-invite/accept |  |  |  | invalidateQueries({ queryKey: partyRoomKeys.detail(roomId), }) | apps/web/src/features/party-room/api.ts:245 |
| useDeclineSpeakerInvite | POST /party/rooms/{id}/speaker-invite/decline |  |  |  | invalidateQueries({ queryKey: partyRoomKeys.detail(roomId), }) | apps/web/src/features/party-room/api.ts:263 |
| useGiftCatalog | GET /gifts | ['gifts', 'catalog'] as const |  |  |  | apps/web/src/features/party-room/api.ts:282 |
| useSendGift | POST /party/rooms/{roomId}/gifts |  |  |  |  | apps/web/src/features/party-room/api.ts:294 |
| useUserProfiles | GET /users/{id} |  |  |  |  | apps/web/src/features/party-room/api.ts:317 |
| usePartyRoomMedia | (none directly) |  |  |  |  | apps/web/src/features/party-room/hooks/use-party-room-media.ts:20 |

## Components

- apps/web/src/features/party-room/components/create-room-form.tsx: 1 interactive elements, uses useRouter, useForm, useCreateRoom
- apps/web/src/features/party-room/components/gift-panel.tsx: 1 interactive elements, uses useGiftCatalog, useSendGift, useIdempotencyKey
- apps/web/src/features/party-room/components/member-list.tsx: 2 interactive elements, uses useChangeRole, useInviteSpeaker
- apps/web/src/features/party-room/components/party-audio.tsx: 0 interactive elements, uses useRef, useEffect
- apps/web/src/features/party-room/components/party-chat.tsx: 2 interactive elements, uses useRoomComments, useSendRoomComment, useState, useRef, useCallback, useMemo, useRealtimeEvent, useEffect
- apps/web/src/features/party-room/components/party-stage.tsx: 7 interactive elements, uses useRouter, useQueryClient, useCurrentUser, useRoomDetail, usePartyRoomMedia, useLeaveRoom, useAcceptSpeakerInvite, useDeclineSpeakerInvite, useState, useGiftCatalog, useRef, useCallback
- apps/web/src/features/party-room/components/room-list.tsx: 4 interactive elements, uses useState, useRoomList, useUserProfiles
- apps/web/src/features/party-room/components/trending-room-cards.tsx: 1 interactive elements, uses useTranslation, useRoomList

## Validation schemas

- createRoomSchema (apps/web/src/features/party-room/create-room-schema.ts:4): title=z.string().trim().min(1, 'Nhập tên phòng').max(100); category=z.enum(['talk', 'sing', 'friend', 'study', 'other'])

## Realtime events

- party.comment.created apps/web/src/features/party-room/components/party-chat.tsx:75
- party.member.joined apps/web/src/features/party-room/components/party-stage.tsx:102
- party.member.left apps/web/src/features/party-room/components/party-stage.tsx:108
- party.member.disconnected apps/web/src/features/party-room/components/party-stage.tsx:114
- party.member.reconnected apps/web/src/features/party-room/components/party-stage.tsx:120
- party.role.changed apps/web/src/features/party-room/components/party-stage.tsx:126
- party.speaker.invite.received apps/web/src/features/party-room/components/party-stage.tsx:132
- party.room.closed apps/web/src/features/party-room/components/party-stage.tsx:138
- party.host.disconnected apps/web/src/features/party-room/components/party-stage.tsx:165
- party.host.reconnected apps/web/src/features/party-room/components/party-stage.tsx:171
- gift.sent apps/web/src/features/party-room/components/party-stage.tsx:177

## Tests

- apps/web/src/features/party-room/api.spec.ts
- apps/web/src/features/party-room/components/member-list.spec.tsx
- apps/web/src/features/party-room/components/party-chat.spec.tsx
- apps/web/src/features/party-room/components/party-stage.spec.tsx
- apps/web/src/features/party-room/components/room-list.spec.tsx
- apps/web/src/features/party-room/create-room-schema.spec.ts
- apps/web/src/features/party-room/hooks/use-party-room-media.spec.tsx
