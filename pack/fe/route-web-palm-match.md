# web route /palm-match

id: fe:route:web:/palm-match
file: apps/web/src/app/(app)/palm-match/page.tsx
layout: app (login required: AppChromeWithAuth)
title: Palm Match
reached source files: 7 (static import closure); data features: palm-match

Access: every route in the (app) group renders inside AppChromeWithAuth (AuthGate): no session -> redirected to /login. Guests can log in with a device id but several actions are limited (see "Guest and capability gating").

## Data it reads (GET)

| endpoint | hook | query key | polling | stale time | module | defined at |
|---|---|---|---|---|---|---|
| GET /palm-match/current | useCurrentPalmMatch | palmMatchKeys.current | (query) => isPollingPalmMatchState(query.state.data?.state) ? PALM_MATCH_REFETCH_INTERVAL_MS : false |  | [palm-match](../api/palm-match.md) | apps/web/src/features/palm-match/api.ts:27 |

## Actions that change data (mutations)

| UI element and label | handler | hook | endpoint | module | call site |
|---|---|---|---|---|---|
| (no matching UI element found; called from PalmReadingView) |  | useJoinPalmQueue | POST /palm-match/queue | [palm-match](../api/palm-match.md) | apps/web/src/features/palm-match/components/palm-reading-view.tsx:64 |
| (no matching UI element found; called from cancelAndGoHome) |  | useDismissPalmMatch | DELETE /palm-match/current | [palm-match](../api/palm-match.md) | apps/web/src/features/palm-match/components/palm-reading-view.tsx:77 |
| (no matching UI element found; called from restart) |  | useDismissPalmMatch | DELETE /palm-match/current | [palm-match](../api/palm-match.md) | apps/web/src/features/palm-match/components/palm-reading-view.tsx:86 |
| (no matching UI element found; called from restart) |  | useJoinPalmQueue | POST /palm-match/queue | [palm-match](../api/palm-match.md) | apps/web/src/features/palm-match/components/palm-reading-view.tsx:89 |
| button.onClick: (no literal label) | () => state.sessionId && rate.mutate({ sessionId: state.sessionId, rating: 'skip' }) | useRatePalmMatch | POST /palm-match/sessions/{id}/rating | [palm-match](../api/palm-match.md) | apps/web/src/features/palm-match/components/palm-reading-view.tsx:262 |
| button.onClick: (no literal label) | () => state.sessionId && rate.mutate({ sessionId: state.sessionId, rating: 'skip' }) | useRatePalmMatch | POST /palm-match/sessions/{id}/rating | [palm-match](../api/palm-match.md) | apps/web/src/features/palm-match/components/palm-reading-view.tsx:276 |
| (no matching UI element found; called from PalmReadingView) |  | useFlipPalmCard | POST /palm-match/sessions/{id}/flip | [palm-match](../api/palm-match.md) | apps/web/src/features/palm-match/components/palm-reading-view.tsx:303 |

## Interactive elements (8)

- button.onClick Thử lại -> () => void current.refetch() (apps/web/src/features/palm-match/components/palm-reading-view.tsx:113)
- button.onClick (no literal label) -> () => void cancelAndGoHome() (apps/web/src/features/palm-match/components/palm-reading-view.tsx:146)
- button.onClick (no literal label) -> () => void restart() (apps/web/src/features/palm-match/components/palm-reading-view.tsx:181)
- button.onClick Dừng chờ và bói lượt khác -> () => void restart() (apps/web/src/features/palm-match/components/palm-reading-view.tsx:221)
- button.onClick (no literal label) -> () => state.sessionId && rate.mutate({ sessionId: state.sessionId, ra… (apps/web/src/features/palm-match/components/palm-reading-view.tsx:257)
- button.onClick (no literal label) -> () => state.sessionId && rate.mutate({ sessionId: state.sessionId, ra… (apps/web/src/features/palm-match/components/palm-reading-view.tsx:271)
- button.onClick Xem duyên số -> () => setShowFortune(true) (apps/web/src/features/palm-match/components/palm-reading-view.tsx:331)
- button.onClick ariaLabel -> onFlip (apps/web/src/features/palm-match/components/palm-reading-view.tsx:418)

## Realtime events listened

none

## Navigation destinations

- /home via link (apps/web/src/features/palm-match/components/palm-reading-view.tsx:197)
- /users/${state.partnerUserId via link (apps/web/src/features/palm-match/components/palm-reading-view.tsx:175)

## Error codes handled in the FE

none referenced by code string (errors are shown through generic ApiError message handling)

## Guest and capability gating

- no isGuest check
- no capability check

## Browser storage and cookies

- cookie-write  (apps/web/src/shared/i18n/locale-store.ts:37)

## i18n keys used (0)



## Tests

- apps/web/src/features/palm-match/api.spec.ts
- apps/web/src/features/palm-match/components/palm-reading-view.spec.tsx

## Backend

modules: [palm-match](../modules/palm-match.md)

service-map flows touching these endpoints: [palm-match-queue-and-rating](../flows/palm-match-queue-and-rating.md)
