# web feature support

id: fe:feature:web:support
directory: apps/web/src/features/support/ (1 source files, 0 components, 0 spec files)

routes whose data comes from this feature: [/help](route-web-help.md)

## Hooks (data layer)

| hook | endpoint(s) | query key | polling | stale | invalidates on success | defined at |
|---|---|---|---|---|---|---|
| useMySupportTickets | GET /support/tickets/me | supportKeys.mine |  |  |  | apps/web/src/features/support/api.ts:15 |
| useCreateSupportTicket | POST /support/tickets |  |  |  | invalidateQueries({ queryKey: supportKeys.mine }) | apps/web/src/features/support/api.ts:27 |

## Components



## Tests

none
