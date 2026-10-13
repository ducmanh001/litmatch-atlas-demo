# web feature palm-match

id: fe:feature:web:palm-match
directory: apps/web/src/features/palm-match/ (2 source files, 1 components, 2 spec files)

routes whose data comes from this feature: [/palm-match](route-web-palm-match.md)

## Hooks (data layer)

| hook | endpoint(s) | query key | polling | stale | invalidates on success | defined at |
|---|---|---|---|---|---|---|
| useCurrentPalmMatch | GET /palm-match/current | palmMatchKeys.current | (query) => isPollingPalmMatchState(query.state.data?.state) ? PALM_MATCH_REFETCH_INTERVAL_MS : false |  |  | apps/web/src/features/palm-match/api.ts:23 |
| usePalmStateMutation | (none directly) |  |  |  | setQueryData(palmMatchKeys.current); invalidateQueries({ queryKey: palmMatchKeys.current }) | apps/web/src/features/palm-match/api.ts:37 |
| useJoinPalmQueue | POST /palm-match/queue |  |  |  |  | apps/web/src/features/palm-match/api.ts:50 |
| useFlipPalmCard | POST /palm-match/sessions/{id}/flip |  |  |  |  | apps/web/src/features/palm-match/api.ts:57 |
| useRatePalmMatch | POST /palm-match/sessions/{id}/rating |  |  |  |  | apps/web/src/features/palm-match/api.ts:67 |
| useDismissPalmMatch | DELETE /palm-match/current |  |  |  |  | apps/web/src/features/palm-match/api.ts:82 |

## Components

- apps/web/src/features/palm-match/components/palm-reading-view.tsx: 8 interactive elements, uses useRouter, useCurrentPalmMatch, useJoinPalmQueue, useFlipPalmCard, useRatePalmMatch, useDismissPalmMatch, useRef, useState, useEffect

## Tests

- apps/web/src/features/palm-match/api.spec.ts
- apps/web/src/features/palm-match/components/palm-reading-view.spec.tsx
