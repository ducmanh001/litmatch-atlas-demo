# REST API of support

id: api:support
module: module:support
prefix: /api/v1; success body {data, meta?}; errors are DomainException codes (see ../errors/)
see: ../modules/support.md

### POST /support/tickets

Gửi phiếu hỗ trợ (category + message tối đa 2000 ký tự) ở trạng thái open; bắt buộc header Idempotency-Key, cùng key gửi lại thì trả phiếu cũ, khác nội dung thì 409.

- handler: SupportController.create (apps/core-api/src/modules/support/support.controller.ts:40); operationId: SupportController_create
- auth: Bearer JWT; requires Idempotency-Key header
- params: header:Idempotency-Key*:string
- body CreateSupportTicketDto: category*:enum(feedback|bug|idea), message*:string[minLength=5 maxLength=2000]
- response 201 SupportTicketDto: id:string, userId:string, category:enum(feedback|bug|idea), message:string, status:enum(open|in_progress|resolved|closed), staffResponse:string, createdAt:string(date-time), updatedAt:string(date-time)
- FE callers: none in this repo (mobile/third party or dead)

### GET /support/tickets/me

Liệt kê phiếu của chính mình theo cursor (createdAt, id) giảm dần để theo dõi trạng thái và staffResponse.

- handler: SupportController.listMine (apps/core-api/src/modules/support/support.controller.ts:55); operationId: SupportController_listMine
- auth: Bearer JWT
- params: query:limit:number, query:cursor:string
- response 200 SupportTicketsPageDto: items:array<SupportTicketDto>, items[].id:string, items[].userId:string, items[].category:enum(feedback|bug|idea), items[].message:string, items[].status:enum(open|in_progress|resolved|closed), items[].staffResponse:string, items[].createdAt:string(date-time), items[].updatedAt:string(date-time), meta:object, meta.nextCursor:string
- FE callers: none in this repo (mobile/third party or dead)

