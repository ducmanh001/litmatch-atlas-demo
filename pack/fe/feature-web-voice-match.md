# web feature voice-match

id: fe:feature:web:voice-match
directory: apps/web/src/features/voice-match/ (3 source files, 1 components, 3 spec files)

routes whose data comes from this feature: [/chat/:friendUserId](route-web-chat-frienduserid.md), [/chat/:friendUserId/call](route-web-chat-frienduserid-call.md), [/matching/voice/:sessionId](route-web-matching-voice-sessionid.md)

## Hooks (data layer)

| hook | endpoint(s) | query key | polling | stale | invalidates on success | defined at |
|---|---|---|---|---|---|---|
| useJoinCall | POST /calling/match-sessions/{matchSessionId}/join |  |  |  | setQueryData(voiceMatchKeys.call(joined.call.id)) | apps/web/src/features/voice-match/api.ts:24 |
| useJoinFriendCall | POST /calling/friends/{friendUserId}/join |  |  |  | setQueryData(voiceMatchKeys.call(joined.call.id)) | apps/web/src/features/voice-match/api.ts:47 |
| useCall | GET /calling/calls/{id} | voiceMatchKeys.call(callId ?? 'none') | (query) => isActiveCallStatus(query.state.data?.status) ? VOICE_CALL_REFETCH_INTERVAL_MS : false |  |  | apps/web/src/features/voice-match/api.ts:67 |
| useEndCall | POST /calling/calls/{id}/end |  |  |  | invalidateQueries({ queryKey: voiceMatchKeys.call(callId), }) | apps/web/src/features/voice-match/api.ts:87 |
| useEndVoiceMatch | POST /calling/match-sessions/{matchSessionId}/end |  |  |  | setQueryData(matchingKeys.current); invalidateQueries({ queryKey: matchingKeys.all }) | apps/web/src/features/voice-match/api.ts:105 |
| useLikeCall | POST /calling/calls/{id}/like |  |  |  |  | apps/web/src/features/voice-match/api.ts:123 |
| useCallRoom | (none directly) |  |  |  |  | apps/web/src/features/voice-match/hooks/use-call-room.ts:19 |

## Components

- apps/web/src/features/voice-match/components/voice-call-room.tsx: 6 interactive elements, uses useQueryClient, useRouter, useCallRoom, useCall, useEndCall, useEndVoiceMatch, useLikeCall, useState, useRef, useEffect, useRealtimeEvent

## Error codes handled

- CALLING_CALL_ENDED apps/web/src/features/voice-match/components/voice-call-room.tsx:91: (error.code === 'CALLING_CALL_ENDED' ||
- CALLING_SESSION_NOT_CALLABLE apps/web/src/features/voice-match/components/voice-call-room.tsx:92: error.code === 'CALLING_SESSION_NOT_CALLABLE' ||
- CALLING_SESSION_NOT_FOUND apps/web/src/features/voice-match/components/voice-call-room.tsx:93: error.code === 'CALLING_SESSION_NOT_FOUND')

## Realtime events

- call.ended apps/web/src/features/voice-match/components/voice-call-room.tsx:200

## Tests

- apps/web/src/features/voice-match/api.spec.ts
- apps/web/src/features/voice-match/components/voice-call-room.spec.tsx
- apps/web/src/features/voice-match/hooks/use-call-room.spec.tsx
