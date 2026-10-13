# web feature matching

id: fe:feature:web:matching
directory: apps/web/src/features/matching/ (10 source files, 7 components, 6 spec files)

routes whose data comes from this feature: [/matching](route-web-matching.md)

## Hooks (data layer)

| hook | endpoint(s) | query key | polling | stale | invalidates on success | defined at |
|---|---|---|---|---|---|---|
| useJoinQueue | POST /matching/tickets |  |  |  | invalidateQueries({ queryKey: matchingKeys.current }) | apps/web/src/features/matching/api.ts:40 |
| useCurrentTicket | GET /matching/tickets/current | matchingKeys.current |  |  |  | apps/web/src/features/matching/api.ts:63 |
| useTicket | GET /matching/tickets/{id} | matchingKeys.ticket(ticketId ?? 'none') | (query) => isPollingStatus(query.state.data?.status) ? MATCHING_TICKET_REFETCH_INTERVAL_MS : false |  |  | apps/web/src/features/matching/api.ts:73 |
| useCancelTicket | DELETE /matching/tickets/{id} |  |  |  | setQueryData(matchingKeys.current); invalidateQueries({ queryKey: matchingKeys.ticket(ticketId), }); invalidateQueries({ queryKey: matchingKeys.current }) | apps/web/src/features/matching/api.ts:94 |
| useConfirmTicket | POST /matching/tickets/{id}/confirm |  |  |  | invalidateQueries({ queryKey: matchingKeys.ticket(ticketId), }); invalidateQueries({ queryKey: matchingKeys.current }) | apps/web/src/features/matching/api.ts:115 |
| useSpeedup | POST /matching/tickets/{id}/speedup |  |  |  | invalidateQueries({ queryKey: matchingKeys.ticket(ticketId), }) | apps/web/src/features/matching/api.ts:134 |
| useCreateInvite | POST /matching/invites |  |  |  |  | apps/web/src/features/matching/invite-api.ts:16 |
| useReceivedInvites | GET /matching/invites | inviteKeys.received |  |  |  | apps/web/src/features/matching/invite-api.ts:31 |
| useAcceptInvite | POST /matching/invites/{id}/accept |  |  |  | invalidateQueries({ queryKey: inviteKeys.received }) | apps/web/src/features/matching/invite-api.ts:41 |
| useDeclineInvite | POST /matching/invites/{id}/decline |  |  |  | invalidateQueries({ queryKey: inviteKeys.received }) | apps/web/src/features/matching/invite-api.ts:56 |

## Components

- apps/web/src/features/matching/components/direct-queue-start.tsx: 1 interactive elements, uses useJoinQueue, useIdempotencyKey, useRef, useCallback, useEffect
- apps/web/src/features/matching/components/incoming-invites.tsx: 3 interactive elements, uses useRouter, useReceivedInvites, useAcceptInvite, useDeclineInvite
- apps/web/src/features/matching/components/match-actions.tsx: 2 interactive elements, uses useRouter, useSearchParams
- apps/web/src/features/matching/components/match-type-picker.tsx: 1 interactive elements, uses useForm, useJoinQueue, useIdempotencyKey, useEffect
- apps/web/src/features/matching/components/matching-scanner.tsx: 0 interactive elements, uses no hooks
- apps/web/src/features/matching/components/queue-status-panel.tsx: 4 interactive elements, uses useRouter, useSearchParams, useQueryClient, useState, useRef, useCurrentTicket, useTicket, useCancelTicket, useConfirmTicket, useCallback, useRealtimeEvent, useEffect
- apps/web/src/features/matching/components/speedup-button.tsx: 1 interactive elements, uses useSpeedup, useIdempotencyKey

## Validation schemas

- joinQueueSchema (apps/web/src/features/matching/join-queue-schema.ts:9): matchType=z.enum(['soul', 'voice']); useDiamond=z.boolean(); genderPreference=z.enum(['any', 'male', 'female'])

## Realtime events

- match.confirmed apps/web/src/features/matching/components/queue-status-panel.tsx:102

## Tests

- apps/web/src/features/matching/api.spec.ts
- apps/web/src/features/matching/components/incoming-invites.spec.tsx
- apps/web/src/features/matching/components/match-actions.spec.tsx
- apps/web/src/features/matching/components/queue-status-panel.spec.tsx
- apps/web/src/features/matching/components/speedup-button.spec.tsx
- apps/web/src/features/matching/join-queue-schema.spec.ts
