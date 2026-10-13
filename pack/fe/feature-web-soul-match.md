# web feature soul-match

id: fe:feature:web:soul-match
directory: apps/web/src/features/soul-match/ (9 source files, 7 components, 4 spec files)

routes whose data comes from this feature: [/matching/soul/:sessionId](route-web-matching-soul-sessionid.md)

## Hooks (data layer)

| hook | endpoint(s) | query key | polling | stale | invalidates on success | defined at |
|---|---|---|---|---|---|---|
| useSoulSession | GET /soul-match/sessions/{id} | soulMatchKeys.session(sessionId) | (query) => isOpenPhase(query.state.data?.phase) ? SOUL_SESSION_REFETCH_INTERVAL_MS : false |  |  | apps/web/src/features/soul-match/api.ts:34 |
| useSoulMessages | GET /soul-match/sessions/{id}/messages | soulMatchKeys.messages(sessionId) | SOUL_MESSAGES_REFETCH_INTERVAL_MS |  |  | apps/web/src/features/soul-match/api.ts:50 |
| useSendSoulMessage | POST /soul-match/sessions/{id}/messages |  |  |  | invalidateQueries({ queryKey: soulMatchKeys.messages(sessionId), }); invalidateQueries({ queryKey: soulMatchKeys.session(sessionId), }) | apps/web/src/features/soul-match/api.ts:74 |
| useRateSession | POST /soul-match/sessions/{id}/rating |  |  |  | invalidateQueries({ queryKey: soulMatchKeys.session(sessionId), }) | apps/web/src/features/soul-match/api.ts:101 |
| useEndSoulSession | POST /soul-match/sessions/{id}/end |  |  |  | invalidateQueries({ queryKey: soulMatchKeys.session(sessionId), }) | apps/web/src/features/soul-match/api.ts:122 |
| useSoulPartner | GET /soul-match/sessions/{id}/partner | soulMatchKeys.partner(sessionId) |  |  |  | apps/web/src/features/soul-match/api.ts:138 |

## Components

- apps/web/src/features/soul-match/components/soul-chat-phase-view.tsx: 0 interactive elements, uses useQueryClient, useSoulSession, useEndSoulSession, useState, useRef, useEffect, useRealtimeEvent
- apps/web/src/features/soul-match/components/soul-countdown-badge.tsx: 0 interactive elements, uses useSoulSession, useState, useEffect
- apps/web/src/features/soul-match/components/soul-message-composer.tsx: 1 interactive elements, uses useForm, useSendSoulMessage, useIdempotencyKey
- apps/web/src/features/soul-match/components/soul-message-list.tsx: 0 interactive elements, uses useSoulMessages, useEffect
- apps/web/src/features/soul-match/components/soul-partner-card.tsx: 0 interactive elements, uses useSoulPartner
- apps/web/src/features/soul-match/components/soul-rating-buttons.tsx: 1 interactive elements, uses useRateSession
- apps/web/src/features/soul-match/components/soul-result-screen.tsx: 0 interactive elements, uses useSoulPartner

## Validation schemas

- sendMessageSchema (apps/web/src/features/soul-match/send-message-schema.ts:4): content=z.string().trim().min(1, 'Nhập nội dung tin nhắn').max(1000)

## Realtime events

- soul.message apps/web/src/features/soul-match/components/soul-chat-phase-view.tsx:71
- soul.matched apps/web/src/features/soul-match/components/soul-chat-phase-view.tsx:78
- soul.ended apps/web/src/features/soul-match/components/soul-chat-phase-view.tsx:85

## Tests

- apps/web/src/features/soul-match/api.spec.ts
- apps/web/src/features/soul-match/components/soul-chat-phase-view.spec.tsx
- apps/web/src/features/soul-match/components/soul-countdown-badge.spec.tsx
- apps/web/src/features/soul-match/send-message-schema.spec.ts
