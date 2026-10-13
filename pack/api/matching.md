# REST API of matching

id: api:matching
module: module:matching
prefix: /api/v1; success body {data, meta?}; errors are DomainException codes (see ../errors/)
see: ../modules/matching.md

### POST /matching/invites

Tạo lời mời Voice/Soul tới invitee: chặn tự mời, rate limit Redis theo giờ, từ chối kín (oracle-safe) nếu invitee nằm trong hidden-set, lưu MatchInvite pending (unique pending theo cặp) rồi tạo notification + push.

- handler: InviteController.createInvite (apps/core-api/src/modules/matching/controllers/invite.controller.ts:52); operationId: InviteController_createInvite
- auth: Bearer JWT; throttle: { default: { limit: 10, ttl: minutes(1) } }
- body CreateInviteDto: inviteeUserId*:string, matchType*:enum(soul|voice)
- response 201 MatchInviteDto: id:string, inviterUserId:string, inviteeUserId:string, matchType:enum(soul|voice), status:enum(pending|accepted|declined|expired|cancelled), expiresAt:string(date-time), sessionId:string, createdAt:string(date-time), inviterProfile:PublicProfileDto, inviterProfile.id:string, inviterProfile.nickname:string, inviterProfile.gender:enum(unknown|male|female|other), inviterProfile.avatarId:string, inviterProfile.interests:array<string>
- FE callers: none in this repo (mobile/third party or dead)

### GET /matching/invites

Inbox lời mời ĐANG pending gửi tới mình (cursor); re-check hidden-set Safety mỗi lần đọc và batch-load profile người mời qua UserService.

- handler: InviteController.listReceived (apps/core-api/src/modules/matching/controllers/invite.controller.ts:66); operationId: InviteController_listReceived
- auth: Bearer JWT
- params: query:limit:number, query:cursor:string
- response 200 MatchInvitesPageDto: items:array<MatchInviteDto>, items[].id:string, items[].inviterUserId:string, items[].inviteeUserId:string, items[].matchType:enum(soul|voice), items[].status:enum(pending|accepted|declined|expired|cancelled), items[].expiresAt:string(date-time), items[].sessionId:string, items[].createdAt:string(date-time), items[].inviterProfile:PublicProfileDto, items[].inviterProfile.id:string, items[].inviterProfile.nickname:string, items[].inviterProfile.gender:enum(unknown|male|female|other), items[].inviterProfile.avatarId:string, items[].inviterProfile.interests:array<string>, nextCursor:string
- FE callers: none in this repo (mobile/third party or dead)

### GET /matching/invites/{id}

Chi tiết một lời mời, chỉ inviter hoặc invitee xem được (người khác 403) kèm inviterProfile.

- handler: InviteController.getInvite (apps/core-api/src/modules/matching/controllers/invite.controller.ts:81); operationId: InviteController_getInvite
- auth: Bearer JWT
- params: path:id*:string
- response 200 MatchInviteDto: id:string, inviterUserId:string, inviteeUserId:string, matchType:enum(soul|voice), status:enum(pending|accepted|declined|expired|cancelled), expiresAt:string(date-time), sessionId:string, createdAt:string(date-time), inviterProfile:PublicProfileDto, inviterProfile.id:string, inviterProfile.nickname:string, inviterProfile.gender:enum(unknown|male|female|other), inviterProfile.avatarId:string, inviterProfile.interests:array<string>
- FE callers: none in this repo (mobile/third party or dead)

### POST /matching/invites/{id}/accept

Invitee chấp nhận: transaction riêng precheck lazy-expire + canPair (fail thì invite thành declined), rồi 1 transaction tạo 2 MatchTicket confirmed + MatchSession confirmed, idempotent theo inviteId, publish match.confirmed sau commit.

- handler: InviteController.accept (apps/core-api/src/modules/matching/controllers/invite.controller.ts:93); operationId: InviteController_accept
- auth: Bearer JWT
- params: path:id*:string
- response 200 MatchInviteAcceptedDto: invite:MatchInviteDto, invite.id:string, invite.inviterUserId:string, invite.inviteeUserId:string, invite.matchType:enum(soul|voice), invite.status:enum(pending|accepted|declined|expired|cancelled), invite.expiresAt:string(date-time), invite.sessionId:string, invite.createdAt:string(date-time), invite.inviterProfile:PublicProfileDto, invite.inviterProfile.id:string, invite.inviterProfile.nickname:string, invite.inviterProfile.gender:enum(unknown|male|female|other), invite.inviterProfile.avatarId:string, invite.inviterProfile.interests:array<string>, sessionId:string, inviteeTicketId:string
- FE callers: none in this repo (mobile/third party or dead)

### POST /matching/invites/{id}/decline

Invitee từ chối lời mời pending (chỉ invitee, lazy-expire theo expiresAt tại thời điểm hành động).

- handler: InviteController.decline (apps/core-api/src/modules/matching/controllers/invite.controller.ts:111); operationId: InviteController_decline
- auth: Bearer JWT
- params: path:id*:string
- response 200 MatchInviteDto: id:string, inviterUserId:string, inviteeUserId:string, matchType:enum(soul|voice), status:enum(pending|accepted|declined|expired|cancelled), expiresAt:string(date-time), sessionId:string, createdAt:string(date-time), inviterProfile:PublicProfileDto, inviterProfile.id:string, inviterProfile.nickname:string, inviterProfile.gender:enum(unknown|male|female|other), inviterProfile.avatarId:string, inviterProfile.interests:array<string>
- FE callers: none in this repo (mobile/third party or dead)

### POST /matching/invites/{id}/cancel

Inviter huỷ lời mời đã gửi trước khi có phản hồi (chỉ inviter).

- handler: InviteController.cancel (apps/core-api/src/modules/matching/controllers/invite.controller.ts:122); operationId: InviteController_cancel
- auth: Bearer JWT
- params: path:id*:string
- response 200 MatchInviteDto: id:string, inviterUserId:string, inviteeUserId:string, matchType:enum(soul|voice), status:enum(pending|accepted|declined|expired|cancelled), expiresAt:string(date-time), sessionId:string, createdAt:string(date-time), inviterProfile:PublicProfileDto, inviterProfile.id:string, inviterProfile.nickname:string, inviterProfile.gender:enum(unknown|male|female|other), inviterProfile.avatarId:string, inviterProfile.interests:array<string>
- FE callers: none in this repo (mobile/third party or dead)

### POST /matching/tickets

Vào hàng đợi: Throttle 10 req/phút, bắt buộc Idempotency-Key, 1 transaction khoá user + trừ quota ngày (guest cần X-Guest-Device-Token) + trừ DIA nếu useDiamond + tạo ticket queued; sau commit mới ZADD vào Redis. 409 nếu đã có ticket active.

- handler: MatchingController.joinQueue (apps/core-api/src/modules/matching/matching.controller.ts:46); operationId: MatchingController_joinQueue
- auth: Bearer JWT; throttle: { default: { limit: 10, ttl: minutes(1) } }; requires Idempotency-Key header
- params: header:X-Guest-Device-Token:string, header:Idempotency-Key*:string
- body JoinQueueDto: matchType*:enum(soul|voice), useDiamond:boolean[default=false], genderPreference:enum(any|male|female)[default="any"]
- response 201 TicketDto: id:string, matchType:enum(soul|voice), status:enum(queued|matched|confirmed|expired|cancelled), paidDiamond:boolean, speedupPriceDiamond:number, region:string, ageBand:number, genderPreference:enum(any|male|female), sessionId:string, enqueuedAt:string(date-time), createdAt:string(date-time)
- FE callers: none in this repo (mobile/third party or dead)

### GET /matching/tickets/current

Phục hồi UI sau reload: trả ticket queued/matched của chính user (hoặc null) kèm giá speedup theo tier VIP.

- handler: MatchingController.getCurrentTicket (apps/core-api/src/modules/matching/matching.controller.ts:74); operationId: MatchingController_getCurrentTicket
- auth: Bearer JWT
- response 200 ActiveTicketResponseDto: ticket:TicketDto, ticket.id:string, ticket.matchType:enum(soul|voice), ticket.status:enum(queued|matched|confirmed|expired|cancelled), ticket.paidDiamond:boolean, ticket.speedupPriceDiamond:number, ticket.region:string, ticket.ageBand:number, ticket.genderPreference:enum(any|male|female), ticket.sessionId:string, ticket.enqueuedAt:string(date-time), ticket.createdAt:string(date-time)
- FE callers: none in this repo (mobile/third party or dead)

### GET /matching/tickets/{id}

Poll trạng thái ticket, chỉ chủ ticket xem được (chống IDOR); đây là fallback khi socket match.confirmed bị lỡ.

- handler: MatchingController.getTicket (apps/core-api/src/modules/matching/matching.controller.ts:89); operationId: MatchingController_getTicket
- auth: Bearer JWT
- params: path:id*:string
- response 200 TicketDto: id:string, matchType:enum(soul|voice), status:enum(queued|matched|confirmed|expired|cancelled), paidDiamond:boolean, speedupPriceDiamond:number, region:string, ageBand:number, genderPreference:enum(any|male|female), sessionId:string, enqueuedAt:string(date-time), createdAt:string(date-time)
- FE callers: none in this repo (mobile/third party or dead)

### DELETE /matching/tickets/{id}

Huỷ ticket của chính mình, chỉ khi còn queued; đổi trạng thái trong DB rồi ZREM khỏi shard Redis.

- handler: MatchingController.cancelTicket (apps/core-api/src/modules/matching/matching.controller.ts:104); operationId: MatchingController_cancelTicket
- auth: Bearer JWT
- params: path:id*:string
- response 200 TicketDto: id:string, matchType:enum(soul|voice), status:enum(queued|matched|confirmed|expired|cancelled), paidDiamond:boolean, speedupPriceDiamond:number, region:string, ageBand:number, genderPreference:enum(any|male|female), sessionId:string, enqueuedAt:string(date-time), createdAt:string(date-time)
- FE callers: none in this repo (mobile/third party or dead)

### POST /matching/tickets/{id}/confirm

Đường tương thích cho session pending_confirm cũ: khoá session trước ticket, đủ 2 bên confirm thì chuyển cả hai ticket + session sang confirmed trong 1 transaction rồi publish match.confirmed + push. UI mới không gọi.

- handler: MatchingController.confirmTicket (apps/core-api/src/modules/matching/matching.controller.ts:117); operationId: MatchingController_confirmTicket
- auth: Bearer JWT
- params: path:id*:string
- response 200 TicketDto: id:string, matchType:enum(soul|voice), status:enum(queued|matched|confirmed|expired|cancelled), paidDiamond:boolean, speedupPriceDiamond:number, region:string, ageBand:number, genderPreference:enum(any|male|female), sessionId:string, enqueuedAt:string(date-time), createdAt:string(date-time)
- FE callers: none in this repo (mobile/third party or dead)

### POST /matching/tickets/{id}/speedup

Mua ưu tiên hàng đợi: chỉ khi ticket queued, trừ DIA qua Economy (key speedup:{user}:{key}, replay không trừ lại), cộng priorityBoostMs trong DB rồi set score Redis tuyệt đối (ZADD XX); không giới hạn số lần.

- handler: MatchingController.speedup (apps/core-api/src/modules/matching/matching.controller.ts:134); operationId: MatchingController_speedup
- auth: Bearer JWT; requires Idempotency-Key header
- params: path:id*:string, header:Idempotency-Key*:string
- response 200 SpeedupResultDto: transactionId:string, replayed:boolean, ticket:TicketDto, ticket.id:string, ticket.matchType:enum(soul|voice), ticket.status:enum(queued|matched|confirmed|expired|cancelled), ticket.paidDiamond:boolean, ticket.speedupPriceDiamond:number, ticket.region:string, ticket.ageBand:number, ticket.genderPreference:enum(any|male|female), ticket.sessionId:string, ticket.enqueuedAt:string(date-time), ticket.createdAt:string(date-time)
- FE callers: none in this repo (mobile/third party or dead)

