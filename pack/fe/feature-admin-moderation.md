# admin feature moderation

id: fe:feature:admin:moderation
directory: apps/admin/src/features/moderation/ (7 source files, 4 components, 1 spec files)

routes whose data comes from this feature: [/](route-admin-root.md), [/moderation](route-admin-moderation.md)

## Hooks (data layer)

| hook | endpoint(s) | query key | polling | stale | invalidates on success | defined at |
|---|---|---|---|---|---|---|
| useReportsList | GET /admin/reports | reportsKeys.list(status, offset) |  | 5000 |  | apps/admin/src/features/moderation/api.ts:18 |
| useResolveReport | POST /admin/reports/{id}/resolve |  |  |  | invalidateQueries({ queryKey: reportsKeys.all }) | apps/admin/src/features/moderation/api.ts:40 |
| useDismissReport | POST /admin/reports/{id}/dismiss |  |  |  | invalidateQueries({ queryKey: reportsKeys.all }) | apps/admin/src/features/moderation/api.ts:54 |
| useBulkReportAction | POST (dynamic) |  |  |  | invalidateQueries({ queryKey: reportsKeys.all }) | apps/admin/src/features/moderation/api.ts:86 |
| useAdminSupportTickets | GET /admin/support/tickets | adminSupportKeys.list(status) |  |  |  | apps/admin/src/features/moderation/support-api.ts:15 |
| useUpdateSupportTicket | PATCH /admin/support/tickets/{id} |  |  |  | invalidateQueries({ queryKey: adminSupportKeys.all }) | apps/admin/src/features/moderation/support-api.ts:29 |
| usePendingVideos | GET /admin/videos/pending | pendingVideosKeys.pending |  | 5000 |  | apps/admin/src/features/moderation/videos-api.ts:21 |
| usePublishedVideos | GET /admin/videos/published | pendingVideosKeys.published |  | 5000 |  | apps/admin/src/features/moderation/videos-api.ts:34 |
| useApproveVideo | POST /admin/videos/{id}/approve |  |  |  | invalidateQueries({ queryKey: pendingVideosKeys.all }) | apps/admin/src/features/moderation/videos-api.ts:47 |
| useRejectVideo | POST /admin/videos/{id}/reject |  |  |  | invalidateQueries({ queryKey: pendingVideosKeys.all }) | apps/admin/src/features/moderation/videos-api.ts:61 |
| useRemoveVideo | POST /admin/videos/{id}/remove |  |  |  | invalidateQueries({ queryKey: pendingVideosKeys.all }) | apps/admin/src/features/moderation/videos-api.ts:75 |

## Components

- apps/admin/src/features/moderation/components/reports-panel.tsx: 15 interactive elements, uses useSearchParams, useState, useReportsList, useResolveReport, useDismissReport, useBulkReportAction, useMemo, useEffect
- apps/admin/src/features/moderation/components/support-tickets-panel.tsx: 3 interactive elements, uses useState, useAdminSupportTickets, useUpdateSupportTicket
- apps/admin/src/features/moderation/components/video-moderation-panels.tsx: 3 interactive elements, uses usePendingVideos, useApproveVideo, useRejectVideo, usePublishedVideos, useRemoveVideo
- apps/admin/src/features/moderation/pages/moderation-page.tsx: 0 interactive elements, uses useT, useSearchParams

## Tests

- apps/admin/src/features/moderation/pages/moderation-page.spec.tsx
