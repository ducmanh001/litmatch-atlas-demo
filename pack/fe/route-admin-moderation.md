# admin route /moderation

id: fe:route:admin:/moderation
file: apps/admin/src/features/moderation/pages/moderation-page.tsx:46
layout: admin (RequireAuth + role)
reached source files: 21 (static import closure); data features: moderation

## UI action map card (human-authored, not verified against code)

MODERATION /moderation: Reports · video · support

Tabs / filters:
  - Reports: pending / resolved / dismissed
  - Pending videos / Published videos
  - Support: open / progress / resolved / closed
Review actions:
  - Resolve / dismiss / bulk case
  - Approve / reject / remove video
  - Reply / assign status ticket

## Data it reads (GET)

| endpoint | hook | query key | polling | stale time | module | defined at |
|---|---|---|---|---|---|---|
| GET /admin/reports | useReportsList | reportsKeys.list(status, offset) |  | 5000 | [admin](../api/admin.md) | apps/admin/src/features/moderation/api.ts:25 |
| GET /admin/support/tickets | useAdminSupportTickets | adminSupportKeys.list(status) |  |  | [admin](../api/admin.md) | apps/admin/src/features/moderation/support-api.ts:21 |
| GET /admin/videos/pending | usePendingVideos | pendingVideosKeys.pending |  | 5000 | [admin](../api/admin.md) | apps/admin/src/features/moderation/videos-api.ts:25 |
| GET /admin/videos/published | usePublishedVideos | pendingVideosKeys.published |  | 5000 | [admin](../api/admin.md) | apps/admin/src/features/moderation/videos-api.ts:38 |

## Actions that change data (mutations)

| UI element and label | handler | hook | endpoint | module | call site |
|---|---|---|---|---|---|
| (no matching UI element found; called from ReportsPanel) |  | useDismissReport | POST /admin/reports/{id}/dismiss | [admin](../api/admin.md) | apps/admin/src/features/moderation/components/reports-panel.tsx:395 |
| (no matching UI element found; called from ReportsPanel) |  | useResolveReport | POST /admin/reports/{id}/resolve | [admin](../api/admin.md) | apps/admin/src/features/moderation/components/reports-panel.tsx:401 |
| Button.onClick: (no literal label) | () => { if (pendingBulkAction === null) return; setBulkFailures([]); bulkAction.mutate( { reportIds: selectedReportIds, | useBulkReportAction | POST (dynamic) | ? | apps/admin/src/features/moderation/components/reports-panel.tsx:518 |
| (no matching UI element found; called from SupportTicketsPanel) |  | useUpdateSupportTicket | PATCH /admin/support/tickets/{id} | [admin](../api/admin.md) | apps/admin/src/features/moderation/components/support-tickets-panel.tsx:61 |
| (no matching UI element found; called from PendingVideosPanel) |  | useApproveVideo | POST /admin/videos/{id}/approve | [admin](../api/admin.md) | apps/admin/src/features/moderation/components/video-moderation-panels.tsx:44 |
| (no matching UI element found; called from PendingVideosPanel) |  | useRejectVideo | POST /admin/videos/{id}/reject | [admin](../api/admin.md) | apps/admin/src/features/moderation/components/video-moderation-panels.tsx:49 |
| (no matching UI element found; called from PublishedVideosPanel) |  | useRemoveVideo | POST /admin/videos/{id}/remove | [admin](../api/admin.md) | apps/admin/src/features/moderation/components/video-moderation-panels.tsx:97 |

## Interactive elements (21)

- Button.onClick Lưu bộ lọc -> saveCurrentFilter (apps/admin/src/features/moderation/components/reports-panel.tsx:243)
- button.onClick (no literal label) -> () => updateStatus(savedStatus) (apps/admin/src/features/moderation/components/reports-panel.tsx:250)
- Button.onClick Bỏ qua hàng loạt -> () => setPendingBulkAction('dismiss') (apps/admin/src/features/moderation/components/reports-panel.tsx:272)
- Button.onClick Xử lý hàng loạt -> () => setPendingBulkAction('resolve') (apps/admin/src/features/moderation/components/reports-panel.tsx:278)
- button.onClick Chọn tất cả report đang chờ trên trang -> toggleAll (apps/admin/src/features/moderation/components/reports-panel.tsx:308)
- SelectCaseButton.onClick (no literal label) -> () => toggleSelected(report.id) (apps/admin/src/features/moderation/components/reports-panel.tsx:354)
- Button.onClick Trang trước -> () => updateOffset(Math.max(0, offset - PAGE_SIZE)) (apps/admin/src/features/moderation/components/reports-panel.tsx:416)
- Button.onClick Trang sau -> () => updateOffset(offset + PAGE_SIZE) (apps/admin/src/features/moderation/components/reports-panel.tsx:427)
- button.onClick trace: -> async () => { const copied = await copyToClipboard(failure.traceId); … (apps/admin/src/features/moderation/components/reports-panel.tsx:459)
- Button.onClick Huỷ -> () => setPendingBulkAction(null) (apps/admin/src/features/moderation/components/reports-panel.tsx:506)
- Button.onClick (no literal label) -> () => { if (pendingBulkAction === null) return; setBulkFailures([]); … (apps/admin/src/features/moderation/components/reports-panel.tsx:512)
- button.onClick `${selected ? 'Bỏ chọn' : 'Chọn'} report ${reportId}` -> onClick (apps/admin/src/features/moderation/components/reports-panel.tsx:566)
- Button.onClick `Sao chép deep link report ${report.id}` -> onCopyLink (apps/admin/src/features/moderation/components/reports-panel.tsx:613)
- Button.onClick Bỏ qua -> onDismiss (apps/admin/src/features/moderation/components/reports-panel.tsx:624)
- Button.onClick Đã xử lý -> onResolve (apps/admin/src/features/moderation/components/reports-panel.tsx:632)
- Button.onClick Nhận xử lý -> () => onUpdate('in_progress', response) (apps/admin/src/features/moderation/components/support-tickets-panel.tsx:114)
- Button.onClick Đã giải quyết -> () => onUpdate('resolved', response) (apps/admin/src/features/moderation/components/support-tickets-panel.tsx:124)
- Button.onClick Đóng -> () => onUpdate('closed', response) (apps/admin/src/features/moderation/components/support-tickets-panel.tsx:133)
- Button.onClick Duyệt -> onApprove (apps/admin/src/features/moderation/components/video-moderation-panels.tsx:153)
- Button.onClick Từ chối -> onReject (apps/admin/src/features/moderation/components/video-moderation-panels.tsx:162)
- Button.onClick Gỡ khỏi feed -> onRemove (apps/admin/src/features/moderation/components/video-moderation-panels.tsx:173)

## Realtime events listened

none

## Navigation destinations

none

## Error codes handled in the FE

none referenced by code string (errors are shown through generic ApiError message handling)

## Guest and capability gating

- no isGuest check
- no capability check

## Form validation (zod)

- envSchema (apps/admin/src/shared/env.ts:8): VITE_API_URL=z.url(); VITE_AUTH_GOOGLE_CLIENT_ID=z.string().min(1).optional(); VITE_SENTRY_DSN=z.url().optional(); VITE_SENTRY_ENVIRONMENT=z.string().min(1).default('production'); VITE_SENTRY_RELEASE=z.string().max(200).optional(); VITE_PHONE_OTP_ENABLED=z .enum(['true', 'false']) .default('true') .transform((value) => value === 'tr…

## Browser storage and cookies

- sessionStorage SAVED_FILTERS_KEY (apps/admin/src/features/moderation/components/reports-panel.tsx:55)
- sessionStorage SAVED_FILTERS_KEY (apps/admin/src/features/moderation/components/reports-panel.tsx:153)
- cookie-write  (apps/admin/src/shared/i18n/locale-store.ts:28)
- staleTime 5000 (apps/admin/src/features/moderation/api.ts:36)
- staleTime 5000 (apps/admin/src/features/moderation/videos-api.ts:30)
- staleTime 5000 (apps/admin/src/features/moderation/videos-api.ts:43)

## i18n keys used (14)

- auth.changePhone: ← Đổi số điện thoại
- auth.guestAvailable: Có thể dùng tài khoản khách.
- common.loading: Đang tải…
- common.unknownError: Sự cố không xác định. Vui lòng tải lại.
- language.english: English
- language.vietnamese: Tiếng Việt
- moderation.pendingVideos: 
- moderation.publishedVideos: 
- moderation.reports: 
- moderation.support: 
- state.copyTraceId: 
- state.trace: 
- state.traceCopied: 
- state.traceCopyFailed: 

## Tests

- apps/admin/src/features/moderation/pages/moderation-page.spec.tsx

## Backend

modules: [admin](../modules/admin.md)

service-map flows touching these endpoints: [admin-video-moderation-queue](../flows/admin-video-moderation-queue.md), [admin-report-resolution](../flows/admin-report-resolution.md), [admin-support-tickets](../flows/admin-support-tickets.md), [short-video-publish-moderation](../flows/short-video-publish-moderation.md)
