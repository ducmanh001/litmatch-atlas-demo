# web route /matching/soul/:sessionId

id: fe:route:web:/matching/soul/:sessionId
file: apps/web/src/app/(app)/matching/soul/[sessionId]/page.tsx
layout: app (login required: AppChromeWithAuth)
title: Soul Match
reached source files: 20 (static import closure); data features: soul-match

Access: every route in the (app) group renders inside AppChromeWithAuth (AuthGate): no session -> redirected to /login. Guests can log in with a device id but several actions are limited (see "Guest and capability gating").

## Data it reads (GET)

| endpoint | hook | query key | polling | stale time | module | defined at |
|---|---|---|---|---|---|---|
| GET /soul-match/sessions/{id} | useSoulSession | soulMatchKeys.session(sessionId) | (query) => isOpenPhase(query.state.data?.phase) ? SOUL_SESSION_REFETCH_INTERVAL_MS : false |  | [soul-match](../api/soul-match.md) | apps/web/src/features/soul-match/api.ts:38 |
| GET /soul-match/sessions/{id}/messages | useSoulMessages | soulMatchKeys.messages(sessionId) | SOUL_MESSAGES_REFETCH_INTERVAL_MS |  | [soul-match](../api/soul-match.md) | apps/web/src/features/soul-match/api.ts:54 |
| GET /soul-match/sessions/{id}/partner | useSoulPartner | soulMatchKeys.partner(sessionId) |  |  | [soul-match](../api/soul-match.md) | apps/web/src/features/soul-match/api.ts:142 |

## Actions that change data (mutations)

| UI element and label | handler | hook | endpoint | module | call site |
|---|---|---|---|---|---|
| (no matching UI element found; called from SoulMessageComposer) |  | useSendSoulMessage | POST /soul-match/sessions/{id}/messages | [soul-match](../api/soul-match.md) | apps/web/src/features/soul-match/components/soul-message-composer.tsx:39 |
| button.onClick: (no literal label) | () => rate.mutate(verdict) | useRateSession | POST /soul-match/sessions/{id}/rating | [soul-match](../api/soul-match.md) | apps/web/src/features/soul-match/components/soul-rating-buttons.tsx:55 |

Other write endpoints reachable (called outside a detected .mutate): POST /soul-match/sessions/{id}/end (useEndSoulSession apps/web/src/features/soul-match/api.ts:126)

## Interactive elements (2)

- form.onSubmit (no literal label) -> onSubmit (apps/web/src/features/soul-match/components/soul-message-composer.tsx:52)
- button.onClick (no literal label) -> () => rate.mutate(verdict) (apps/web/src/features/soul-match/components/soul-rating-buttons.tsx:51)

## Realtime events listened

- soul.message (apps/web/src/features/soul-match/components/soul-chat-phase-view.tsx:71)
- soul.matched (apps/web/src/features/soul-match/components/soul-chat-phase-view.tsx:78)
- soul.ended (apps/web/src/features/soul-match/components/soul-chat-phase-view.tsx:85)
- (dynamic: event) (apps/web/src/shared/realtime/use-realtime-event.ts:17)

## Navigation destinations

- /matching via link (apps/web/src/features/soul-match/components/soul-result-screen.tsx:68)
- /home via link (apps/web/src/features/soul-match/components/soul-result-screen.tsx:74)

## Error codes handled in the FE

none referenced by code string (errors are shown through generic ApiError message handling)

## Guest and capability gating

- no isGuest check
- no capability check

## Form validation (zod)

- sendMessageSchema (apps/web/src/features/soul-match/send-message-schema.ts:4): content=z.string().trim().min(1, 'Nhập nội dung tin nhắn').max(1000)

## Browser storage and cookies

- cookie-write  (apps/web/src/shared/i18n/locale-store.ts:37)

## i18n keys used (0)



## Tests

- apps/web/src/features/soul-match/api.spec.ts
- apps/web/src/features/soul-match/components/soul-chat-phase-view.spec.tsx
- apps/web/src/features/soul-match/components/soul-countdown-badge.spec.tsx
- apps/web/src/features/soul-match/send-message-schema.spec.ts

## Backend

modules: [soul-match](../modules/soul-match.md)

service-map flows touching these endpoints: [soul-match-room-reads-and-reveal](../flows/soul-match-room-reads-and-reveal.md), [soul-match-session](../flows/soul-match-session.md), [soul-match-send-message](../flows/soul-match-send-message.md)
