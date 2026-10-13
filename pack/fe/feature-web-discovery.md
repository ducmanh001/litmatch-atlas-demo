# web feature discovery

id: fe:feature:web:discovery
directory: apps/web/src/features/discovery/ (5 source files, 4 components, 1 spec files)

routes whose data comes from this feature: [/discovery](route-web-discovery.md)

## Hooks (data layer)

| hook | endpoint(s) | query key | polling | stale | invalidates on success | defined at |
|---|---|---|---|---|---|---|
| useBrowse | GET /discovery/browse | discoveryKeys.browse(filter) |  |  |  | apps/web/src/features/discovery/api.ts:33 |
| useNearby | GET /discovery/nearby | discoveryKeys.nearby(filter) |  |  |  | apps/web/src/features/discovery/api.ts:54 |
| useSetNearbyVisible | PUT /discovery/nearby/visible |  |  |  | invalidateQueries({ queryKey: ['discovery'] }) | apps/web/src/features/discovery/api.ts:74 |
| useSetLocation | PUT /discovery/nearby/location |  |  |  |  | apps/web/src/features/discovery/api.ts:89 |

## Components

- apps/web/src/features/discovery/components/discovery-card.tsx: 0 interactive elements, uses useState
- apps/web/src/features/discovery/components/discovery-detail-sheet.tsx: 4 interactive elements, uses useId, useRef, useState, useEffect
- apps/web/src/features/discovery/components/discovery-filters.tsx: 4 interactive elements, uses no hooks
- apps/web/src/features/discovery/components/nearby-opt-in.tsx: 2 interactive elements, uses useSetLocation, useSetNearbyVisible, useState

## Tests

- apps/web/src/features/discovery/components/discovery-detail-sheet.spec.tsx
