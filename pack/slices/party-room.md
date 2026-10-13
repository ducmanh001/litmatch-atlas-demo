# Feature slice: party-room

id: slice:party-room
status: implemented; owner: core-api/party-room

Party-room lifecycle, LiveKit token and member roles

This file follows ONE feature through every layer. Layers are joined by the actual endpoint calls found in the frontend code, so it shows what the code does, not what a plan says.

## 1. Product contract and docs

- registry contracts: openapi/core-api.json, docs/services/party-room-service.md, docs/services/realtime-gateway.md
- registry verification tests: apps/core-api/src/modules/party-room/party-room.integration.spec.ts
- service docs: docs/services/party-room-service.md

## 2. Frontend

| route | app | endpoints of this feature it calls | page |
|---|---|---|---|
| [/party/:roomId](../fe/route-web-party-roomid.md) | web | GET /party/rooms/{id}; GET /party/rooms/{id}/comments; POST /party/rooms/{id}/leave; POST /party/rooms/{id}/speaker-invite/accept; POST /party/rooms/{id}/speaker-invite/decline; POST /party/rooms/{id}/members/{userId}/role … | app/(app)/party/[roomId]/page.tsx |
| [/party](../fe/route-web-party.md) | web | GET /party/rooms; POST /party/rooms | app/(app)/party/page.tsx |
| [/feed](../fe/route-web-feed.md) | web | GET /party/rooms | app/(app)/feed/page.tsx |
| [/home](../fe/route-web-home.md) | web | GET /party/rooms | app/(app)/home/page.tsx |
| [/](../fe/route-admin-root.md) | admin | GET /party/rooms | features/dashboard/pages/dashboard-page.tsx |

FE features (directories): [web/party-room](../fe/feature-web-party-room.md), [web/notification](../fe/feature-web-notification.md), [web/wallet](../fe/feature-web-wallet.md), [web/feed](../fe/feature-web-feed.md), [admin/dashboard](../fe/feature-admin-dashboard.md), [admin/rooms](../fe/feature-admin-rooms.md)

User actions that mutate this feature's data:
- useLeaveRoom -> POST /party/rooms/{id}/leave (apps/web/src/features/party-room/components/party-stage.tsx:232)
- useAcceptSpeakerInvite -> POST /party/rooms/{id}/speaker-invite/accept (apps/web/src/features/party-room/components/party-stage.tsx:365)
- useDeclineSpeakerInvite -> POST /party/rooms/{id}/speaker-invite/decline (apps/web/src/features/party-room/components/party-stage.tsx:377)
- useChangeRole -> POST /party/rooms/{id}/members/{userId}/role (apps/web/src/features/party-room/components/member-list.tsx:124)
- useInviteSpeaker -> POST /party/rooms/{id}/members/{userId}/speaker-invite (apps/web/src/features/party-room/components/member-list.tsx:178)
- useSendRoomComment -> POST /party/rooms/{id}/comments (apps/web/src/features/party-room/components/party-chat.tsx:113)
- useCreateRoom -> POST /party/rooms (apps/web/src/features/party-room/components/create-room-form.tsx:30)

Realtime events the frontend listens to for this feature:
- party.member.joined in /party/:roomId (apps/web/src/features/party-room/components/party-stage.tsx:102)
- party.member.left in /party/:roomId (apps/web/src/features/party-room/components/party-stage.tsx:108)
- party.member.disconnected in /party/:roomId (apps/web/src/features/party-room/components/party-stage.tsx:114)
- party.member.reconnected in /party/:roomId (apps/web/src/features/party-room/components/party-stage.tsx:120)
- party.role.changed in /party/:roomId (apps/web/src/features/party-room/components/party-stage.tsx:126)
- party.speaker.invite.received in /party/:roomId (apps/web/src/features/party-room/components/party-stage.tsx:132)
- party.room.closed in /party/:roomId (apps/web/src/features/party-room/components/party-stage.tsx:138)
- party.host.disconnected in /party/:roomId (apps/web/src/features/party-room/components/party-stage.tsx:165)
- party.host.reconnected in /party/:roomId (apps/web/src/features/party-room/components/party-stage.tsx:171)
- party.comment.created in /party/:roomId (apps/web/src/features/party-room/components/party-chat.tsx:75)

## 3. API

| endpoint | auth | idempotency | throttle | FE callers |
|---|---|---|---|---|
| POST /party/rooms ([party-room](../api/party-room.md)) | JWT |  | { default: { limit: 5, ttl: minutes(1) } } | 1 |
| GET /party/rooms ([party-room](../api/party-room.md)) | JWT |  |  | 2 |
| GET /party/rooms/{id} ([party-room](../api/party-room.md)) | JWT |  |  | 1 |
| GET /party/rooms/{id}/comments ([party-room](../api/party-room.md)) | JWT |  |  | 1 |
| POST /party/rooms/{id}/comments ([party-room](../api/party-room.md)) | JWT | required | { default: { limit: 60, ttl: minutes(1) } } | 1 |
| POST /party/rooms/{id}/join ([party-room](../api/party-room.md)) | JWT |  | { default: { limit: 20, ttl: minutes(1) } } | 1 |
| POST /party/rooms/{id}/leave ([party-room](../api/party-room.md)) | JWT |  |  | 1 |
| POST /party/rooms/{id}/members/{userId}/role ([party-room](../api/party-room.md)) | JWT |  |  | 0 |
| POST /party/rooms/{id}/members/{userId}/speaker-invite ([party-room](../api/party-room.md)) | JWT |  |  | 0 |
| POST /party/rooms/{id}/speaker-invite/accept ([party-room](../api/party-room.md)) | JWT |  |  | 0 |
| POST /party/rooms/{id}/speaker-invite/decline ([party-room](../api/party-room.md)) | JWT |  |  | 0 |
| POST /party/webhooks/livekit ([party-room](../api/party-room.md)) | public |  |  | 0 |


## 4. Backend flows (service map)

- [party-room-detail-and-comments](../flows/party-room-detail-and-comments.md): Xem phòng party và bình luận trong phòng (4 endpoints of this feature)
- [party-room-lifecycle](../flows/party-room-lifecycle.md): Vòng đời phòng party với LiveKit (3 endpoints of this feature)
- [party-room-speaker-invite](../flows/party-room-speaker-invite.md): Mời lên nói và phản hồi lời mời (3 endpoints of this feature)
- [party-room-leave-and-role-change](../flows/party-room-leave-and-role-change.md): Rời phòng và đổi vai trò thành viên (2 endpoints of this feature)

## 5. Data

- tables owned by the module(s): `party_room_comments`, `party_room_members`, `party_rooms` (see ../db/party-room.md)

## 6. Events

- party.member.joined: producers party-room.joinRoom; FE consumers 1
- party.member.left: producers party-room.leaveRoom, party-room.finalizeDisconnectedMember; FE consumers 1
- party.member.disconnected: producers party-room.handleParticipantLeft; FE consumers 1
- party.member.reconnected: producers party-room.joinRoom; FE consumers 1
- party.role.changed: producers party-room.changeRole, party-room.publishRoleChanged; FE consumers 1
- party.speaker.invite.received: producers party-room.inviteSpeaker; FE consumers 1
- party.room.closed: producers party-room.closeRoomById; FE consumers 1
- party.host.disconnected: producers party-room.markHostDisconnected; FE consumers 1
- party.host.reconnected: producers party-room.joinRoom; FE consumers 1
- party.comment.created: producers party-room.sendComment; FE consumers 1

## 7. Errors

- error code groups: [PartyRoomErrors](../errors/partyroomerrors.md)
- codes the FE references for this feature: none

## 8. Configuration

env vars read by the module(s): `PARTY_SWEEPER_INTERVAL_MS`, `PARTY_HOST_GRACE_CHECK_INTERVAL_MS`, `PARTY_HOST_DISCONNECT_GRACE_SECONDS`, `PARTY_MEMBER_DISCONNECT_GRACE_SECONDS`, `PARTY_STALE_ROOM_SECONDS`, `PARTY_TITLE_MAX_LENGTH`, `PARTY_MAX_SPEAKERS`, `PARTY_MAX_MEMBERS`, `PARTY_EMPTY_ROOM_TIMEOUT_SECONDS`, `PARTY_COMMENT_MAX_LENGTH`, `LIVEKIT_URL`, `LIVEKIT_REGION_URLS`, `PARTY_TOKEN_TTL_SECONDS`, `REDIS_URL` (see ../config/)

## 9. Tests

- party-room: 1 unit spec files, 1 integration spec files
- e2e files that call these endpoints: apps/core-api-e2e/src/core-api/livekit-webhook-content-type.spec.ts
- FE specs: apps/web/src/features/friend-chat/api.spec.ts, apps/web/src/features/friend-chat/components/conversation-inbox.spec.tsx, apps/web/src/features/friend-chat/components/conversation-thread.spec.tsx, apps/web/src/features/friend-chat/components/friends-directory.spec.tsx, apps/web/src/features/friend-chat/components/message-list.spec.tsx, apps/web/src/features/friend-chat/send-message-schema.spec.ts, apps/web/src/features/party-room/api.spec.ts, apps/web/src/features/party-room/components/member-list.spec.tsx, apps/web/src/features/party-room/components/party-chat.spec.tsx, apps/web/src/features/party-room/components/party-stage.spec.tsx, apps/web/src/features/party-room/components/room-list.spec.tsx, apps/web/src/features/party-room/create-room-schema.spec.ts

## 10. Known issues (public subset of the ledger)

- P006 [fixed] Swagger thiếu cursor pagination → client sinh kiểu never

See ../issues/index.md.
