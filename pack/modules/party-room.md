# Module party-room

id: module:party-room
kind: core-api module
code: apps/core-api/src/modules/party-room

Sở hữu phòng party (party_rooms), membership theo dòng, comment append-only, lời mời speaker và vòng đời phòng (host rời/rớt, sweeper, grace reconnect). Quyền publish audio được enforce ở LiveKit SFU qua token/updateParticipant; KHÔNG sở hữu hồ sơ user (User), ví/quà (Gift/Economy) và không phải media-server.

## Facts

- tables owned: party_room_comments, party_room_members, party_rooms
- API: 12 endpoints, 1 public, 0 admin-only, 1 require Idempotency-Key, 3 with own throttle
- publishes realtime: party.member.joined, party.member.left, party.member.disconnected, party.member.reconnected, party.role.changed, party.speaker.invite.received, party.room.closed, party.host.disconnected, party.host.reconnected, party.comment.created
- jobs: PartyRoomSweeperService, PartyRoomSweeperService
- env vars read: PARTY_SWEEPER_INTERVAL_MS, PARTY_HOST_GRACE_CHECK_INTERVAL_MS, PARTY_HOST_DISCONNECT_GRACE_SECONDS, PARTY_MEMBER_DISCONNECT_GRACE_SECONDS, PARTY_STALE_ROOM_SECONDS, PARTY_TITLE_MAX_LENGTH, PARTY_MAX_SPEAKERS, PARTY_MAX_MEMBERS, PARTY_EMPTY_ROOM_TIMEOUT_SECONDS, PARTY_COMMENT_MAX_LENGTH, LIVEKIT_URL, LIVEKIT_REGION_URLS, PARTY_TOKEN_TTL_SECONDS, REDIS_URL
- error codes: 17 in PartyRoomErrors (1 used indirectly, not via new DomainException)
- tests: 1 unit spec files, 1 integration spec files
- history: 18 commits (3 fixes), last 2026-08-14
- docs: docs/services/party-room-service.md (last changed 2026-08-14)
- features: party-room

## Where to read next

- tables: ../db/party-room.md
- API contract: ../api/party-room.md
- why it talks to other services: ../service-map/party-room.md and ../service-map/party-room-techniques.md
- error codes: ../errors/partyroomerrors.md
- flows involving it: [admin-dashboard](../flows/admin-dashboard.md), [admin-room-management](../flows/admin-room-management.md), [gift-in-party-room](../flows/gift-in-party-room.md), [party-room-detail-and-comments](../flows/party-room-detail-and-comments.md), [party-room-leave-and-role-change](../flows/party-room-leave-and-role-change.md), [party-room-lifecycle](../flows/party-room-lifecycle.md), [party-room-speaker-invite](../flows/party-room-speaker-invite.md)

## Linked issues (heuristic)

- P006 [fixed] Swagger thiếu cursor pagination → client sinh kiểu never

See ../issues/index.md.
