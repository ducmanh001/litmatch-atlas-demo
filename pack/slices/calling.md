# Feature slice: calling

id: slice:calling
status: implemented; owner: core-api/calling

Voice-call lifecycle and settlement

This file follows ONE feature through every layer. Layers are joined by the actual endpoint calls found in the frontend code, so it shows what the code does, not what a plan says.

## 1. Product contract and docs

- registry contracts: openapi/core-api.json, docs/services/calling-service.md, docs/06-domain-rules.md
- registry verification tests: apps/core-api/src/modules/calling/calling.integration.spec.ts
- service docs: docs/services/calling-service.md

## 2. Frontend

| route | app | endpoints of this feature it calls | page |
|---|---|---|---|
| [/matching/voice/:sessionId](../fe/route-web-matching-voice-sessionid.md) | web | GET /calling/calls/{id}; POST /calling/calls/{id}/end; POST /calling/match-sessions/{matchSessionId}/end; POST /calling/calls/{id}/like; POST /calling/match-sessions/{matchSessionId}/join; POST /calling/friends/{friendUserId}/join | app/(app)/matching/voice/[sessionId]/page.tsx |
| [/chat/:friendUserId/call](../fe/route-web-chat-frienduserid-call.md) | web | GET /calling/calls/{id}; POST /calling/calls/{id}/end; POST /calling/match-sessions/{matchSessionId}/join; POST /calling/friends/{friendUserId}/join | app/(app)/chat/[friendUserId]/call/page.tsx |
| [/chat/:friendUserId](../fe/route-web-chat-frienduserid.md) | web | POST /calling/friends/{friendUserId}/join | app/(app)/chat/[friendUserId]/page.tsx |

FE features (directories): [web/voice-match](../fe/feature-web-voice-match.md), [web/friend-chat](../fe/feature-web-friend-chat.md)

User actions that mutate this feature's data:
- useLikeCall -> POST /calling/calls/{id}/like (apps/web/src/features/voice-match/components/voice-call-room.tsx:249)
- useEndCall -> POST /calling/calls/{id}/end (apps/web/src/features/voice-match/components/voice-call-room.tsx:268)
- useLikeCall -> POST /calling/calls/{id}/like (apps/web/src/features/voice-match/components/voice-call-room.tsx:284)
- useEndCall -> POST /calling/calls/{id}/end (apps/web/src/features/friend-chat/components/friend-call-room.tsx:140)
- useJoinFriendCall -> POST /calling/friends/{friendUserId}/join (apps/web/src/features/friend-chat/components/conversation-thread.tsx:267)

Realtime events the frontend listens to for this feature:
- call.ended in /matching/voice/:sessionId (apps/web/src/features/voice-match/components/voice-call-room.tsx:200)
- call.ended in /chat/:friendUserId/call (apps/web/src/features/friend-chat/components/friend-call-room.tsx:94)

## 3. API

| endpoint | auth | idempotency | throttle | FE callers |
|---|---|---|---|---|
| POST /calling/match-sessions/{matchSessionId}/join ([calling](../api/calling.md)) | JWT |  | { default: { limit: 20, ttl: minutes(1) } } | 0 |
| POST /calling/friends/{friendUserId}/join ([calling](../api/calling.md)) | JWT |  | { default: { limit: 20, ttl: minutes(1) } } | 0 |
| GET /calling/calls/{id} ([calling](../api/calling.md)) | JWT |  |  | 1 |
| POST /calling/calls/{id}/end ([calling](../api/calling.md)) | JWT |  |  | 1 |
| POST /calling/match-sessions/{matchSessionId}/end ([calling](../api/calling.md)) | JWT |  |  | 0 |
| POST /calling/calls/{id}/like ([calling](../api/calling.md)) | JWT |  |  | 1 |
| POST /calling/webhooks/livekit ([calling](../api/calling.md)) | public |  |  | 0 |


## 4. Backend flows (service map)

- [matching-to-call](../flows/matching-to-call.md): Từ hàng đợi ghép đôi đến cuộc gọi thoại (3 endpoints of this feature)
- [calling-end-and-ticker](../flows/calling-end-and-ticker.md): Kết thúc cuộc gọi và đồng hồ cuộc gọi (2 endpoints of this feature)
- [calling-like-after-call](../flows/calling-like-after-call.md): Like sau cuộc gọi để thành bạn (1 endpoints of this feature)
- [friend-direct-call](../flows/friend-direct-call.md): Gọi trực tiếp cho bạn bè (1 endpoints of this feature)

## 5. Data

- tables owned by the module(s): `call_sessions`, `voice_match_reactions` (see ../db/calling.md)
- status vocabularies enforced by CHECK:
  - call_sessions: voice_match | friend
  - call_sessions: completed | free_limit | insufficient_balance | pending_timeout
  - call_sessions: pending | active | ended

## 6. Events

- call.ended: producers calling.cleanupEndedCall; FE consumers 2

## 7. Errors

- error code groups: [CallingErrors](../errors/callingerrors.md)
- codes the FE references for this feature: CALLING_SESSION_NOT_FOUND, CALLING_SESSION_NOT_CALLABLE, CALLING_CALL_ENDED

## 8. Configuration

env vars read by the module(s): `CALLING_FREE_CALL_SECONDS`, `CALLING_TOKEN_TTL_SECONDS`, `LIVEKIT_URL`, `LIVEKIT_REGION_URLS`, `CALLING_TICKER_INTERVAL_MS`, `CALLING_PENDING_TIMEOUT_SECONDS`, `CALLING_RECONNECT_WINDOW_SECONDS`, `REDIS_URL` (see ../config/)

## 9. Tests

- calling: 3 unit spec files, 1 integration spec files
- e2e files that call these endpoints: apps/core-api-e2e/src/core-api/livekit-webhook-content-type.spec.ts
- FE specs: apps/web/src/features/friend-chat/api.spec.ts, apps/web/src/features/friend-chat/components/conversation-inbox.spec.tsx, apps/web/src/features/friend-chat/components/conversation-thread.spec.tsx, apps/web/src/features/friend-chat/components/friends-directory.spec.tsx, apps/web/src/features/friend-chat/components/message-list.spec.tsx, apps/web/src/features/friend-chat/send-message-schema.spec.ts, apps/web/src/features/matching/api.spec.ts, apps/web/src/features/matching/components/incoming-invites.spec.tsx, apps/web/src/features/matching/components/match-actions.spec.tsx, apps/web/src/features/matching/components/queue-status-panel.spec.tsx, apps/web/src/features/matching/components/speedup-button.spec.tsx, apps/web/src/features/matching/join-queue-schema.spec.ts

## 10. Known issues (linked from the ledger)

- P071 [fixed] Cổng gọi điện có thể bypass sau khi block (bắt được khi tự review)

See ../issues/index.md.
