# REST API of party-room

id: api:party-room
module: module:party-room
prefix: /api/v1; success body {data, meta?}; errors are DomainException codes (see ../errors/)
see: ../modules/party-room.md

### POST /party/rooms

Tạo phòng: transaction lưu party_rooms + membership host (speaker_limit và livekit_url snapshot), rồi createRoom LiveKit tường minh và mint token host; SFU fail thì đóng phòng (error) + 503; throttle 5/phút, 1 user chỉ 1 phòng active.

- handler: PartyRoomController.create (apps/core-api/src/modules/party-room/party-room.controller.ts:50); operationId: PartyRoomController_create
- auth: Bearer JWT; throttle: { default: { limit: 5, ttl: minutes(1) } }
- body CreatePartyRoomDto: title*:string[maxLength=500], category:enum(talk|sing|friend|study|other)[default="talk"]
- response 200 JoinPartyRoomDto: room:PartyRoomDto, room.id:string, room.hostUserId:string, room.title:string, room.status:enum(active|closed), room.speakerLimit:number, room.category:enum(talk|sing|friend|study|other), room.memberCount:number, room.closeReason:enum(host_left|finished|swept|error|admin_closed), room.createdAt:string(date-time), room.hostDisconnectedAt:string(date-time), membership:PartyRoomMemberDto, membership.userId:string, membership.role:enum(host|speaker|audience), membership.joinedAt:string(date-time), membership.disconnectedAt:string(date-time), membership.nickname:string, membership.speakerInvitePending:boolean, token:string, livekitUrl:string
- FE callers: none in this repo (mobile/third party or dead)

### GET /party/rooms

Liệt kê phòng active kèm số member, phân trang cursor, lọc q/category; chỉ đọc DB.

- handler: PartyRoomController.list (apps/core-api/src/modules/party-room/party-room.controller.ts:65); operationId: PartyRoomController_list
- auth: Bearer JWT
- params: query:q:string, query:category:enum(talk|sing|friend|study|other), query:limit:number, query:cursor:string
- response 200 PartyRoomListDto: data:array<PartyRoomDto>, data[].id:string, data[].hostUserId:string, data[].title:string, data[].status:enum(active|closed), data[].speakerLimit:number, data[].category:enum(talk|sing|friend|study|other), data[].memberCount:number, data[].closeReason:enum(host_left|finished|swept|error|admin_closed), data[].createdAt:string(date-time), data[].hostDisconnectedAt:string(date-time), meta:object, meta.nextCursor:string
- FE callers: none in this repo (mobile/third party or dead)

### GET /party/rooms/{id}

Chi tiết phòng + member active, nickname lấy từ User (findByIds); là nguồn sự thật mà client poll fallback để biết hostDisconnectedAt/status.

- handler: PartyRoomController.get (apps/core-api/src/modules/party-room/party-room.controller.ts:81); operationId: PartyRoomController_get
- auth: Bearer JWT
- params: path:id*:string
- response 200 PartyRoomDetailDto: room:PartyRoomDto, room.id:string, room.hostUserId:string, room.title:string, room.status:enum(active|closed), room.speakerLimit:number, room.category:enum(talk|sing|friend|study|other), room.memberCount:number, room.closeReason:enum(host_left|finished|swept|error|admin_closed), room.createdAt:string(date-time), room.hostDisconnectedAt:string(date-time), members:array<PartyRoomMemberDto>, members[].userId:string, members[].role:enum(host|speaker|audience), members[].joinedAt:string(date-time), members[].disconnectedAt:string(date-time), members[].nickname:string, members[].speakerInvitePending:boolean
- FE callers: none in this repo (mobile/third party or dead)

### GET /party/rooms/{id}/comments

Lấy comment mới nhất theo cursor (đi lùi); chỉ member của phòng mới đọc được.

- handler: PartyRoomController.listComments (apps/core-api/src/modules/party-room/party-room.controller.ts:97); operationId: PartyRoomController_listComments
- auth: Bearer JWT
- params: path:id*:string, query:limit:number, query:cursor:string
- response 200 PartyRoomCommentsPageDto: items:array<PartyRoomCommentDto>, items[].id:string, items[].roomId:string, items[].senderUserId:string, items[].content:string, items[].sentAt:string(date-time), meta:object, meta.nextCursor:string
- FE callers: none in this repo (mobile/third party or dead)

### POST /party/rooms/{id}/comments

Gửi comment: bắt buộc Idempotency-Key (unique DB, replay không bắn lại), throttle 60/phút, kiểm member active dưới lock phòng rồi publish party.comment.created sau commit.

- handler: PartyRoomController.createComment (apps/core-api/src/modules/party-room/party-room.controller.ts:116); operationId: PartyRoomController_createComment
- auth: Bearer JWT; throttle: { default: { limit: 60, ttl: minutes(1) } }; requires Idempotency-Key header
- params: path:id*:string, header:Idempotency-Key*:string
- body CreatePartyRoomCommentDto: content*:string[maxLength=1000]
- response 201 PartyRoomCommentDto: id:string, roomId:string, senderUserId:string, content:string, sentAt:string(date-time)
- FE callers: none in this repo (mobile/third party or dead)

### POST /party/rooms/{id}/join

Join làm audience (token canPublish=false) hoặc rejoin: đếm PARTY_MAX_MEMBERS dưới lock; rejoin của host/member trong grace xoá cờ disconnected và bắn party.host.reconnected / party.member.reconnected; token mint từ role trong DB.

- handler: PartyRoomController.join (apps/core-api/src/modules/party-room/party-room.controller.ts:132); operationId: PartyRoomController_join
- auth: Bearer JWT; throttle: { default: { limit: 20, ttl: minutes(1) } }
- params: path:id*:string
- response 200 JoinPartyRoomDto: room:PartyRoomDto, room.id:string, room.hostUserId:string, room.title:string, room.status:enum(active|closed), room.speakerLimit:number, room.category:enum(talk|sing|friend|study|other), room.memberCount:number, room.closeReason:enum(host_left|finished|swept|error|admin_closed), room.createdAt:string(date-time), room.hostDisconnectedAt:string(date-time), membership:PartyRoomMemberDto, membership.userId:string, membership.role:enum(host|speaker|audience), membership.joinedAt:string(date-time), membership.disconnectedAt:string(date-time), membership.nickname:string, membership.speakerInvitePending:boolean, token:string, livekitUrl:string
- FE callers: none in this repo (mobile/third party or dead)

### POST /party/rooms/{id}/leave

Rời phòng idempotent: member thường nhả membership + removeParticipant khỏi SFU; host rời chủ động thì đóng phòng ngay (host_left) và báo party.room.closed.

- handler: PartyRoomController.leave (apps/core-api/src/modules/party-room/party-room.controller.ts:149); operationId: PartyRoomController_leave
- auth: Bearer JWT
- params: path:id*:string
- response 200: 
- FE callers: none in this repo (mobile/third party or dead)

### POST /party/rooms/{id}/members/{userId}/role

Host thu quyền speaker về audience dưới lock phòng; role commit trước rồi updateParticipantPublish(canPublish=false) ngoài transaction, lỗi SFU thì rollback role + 503; chỉ host (NOT_HOST 403).

- handler: PartyRoomController.changeRole (apps/core-api/src/modules/party-room/party-room.controller.ts:163); operationId: PartyRoomController_changeRole
- auth: Bearer JWT
- params: path:id*:string, path:userId*:string
- body ChangePartyRoleDto: role*:enum(audience)
- response 200 PartyRoomMemberDto: userId:string, role:enum(host|speaker|audience), joinedAt:string(date-time), disconnectedAt:string(date-time), nickname:string, speakerInvitePending:boolean
- FE callers: none in this repo (mobile/third party or dead)

### POST /party/rooms/{id}/members/{userId}/speaker-invite

Host mời audience lên speaker: chỉ ghi speaker_invite_pending và bắn party.speaker.invite.received tới target; chưa đổi role hay quyền publish.

- handler: PartyRoomController.inviteSpeaker (apps/core-api/src/modules/party-room/party-room.controller.ts:181); operationId: PartyRoomController_inviteSpeaker
- auth: Bearer JWT
- params: path:id*:string, path:userId*:string
- response 200 PartyRoomMemberDto: userId:string, role:enum(host|speaker|audience), joinedAt:string(date-time), disconnectedAt:string(date-time), nickname:string, speakerInvitePending:boolean
- FE callers: none in this repo (mobile/third party or dead)

### POST /party/rooms/{id}/speaker-invite/accept

Target chấp nhận lời mời: đếm speaker so speaker_limit dưới lock phòng, đổi role, updateParticipantPublish(true), bắn party.role.changed; chỉ chính target mới đổi được role của mình.

- handler: PartyRoomController.acceptSpeakerInvite (apps/core-api/src/modules/party-room/party-room.controller.ts:195); operationId: PartyRoomController_acceptSpeakerInvite
- auth: Bearer JWT
- params: path:id*:string
- response 200 PartyRoomMemberDto: userId:string, role:enum(host|speaker|audience), joinedAt:string(date-time), disconnectedAt:string(date-time), nickname:string, speakerInvitePending:boolean
- FE callers: none in this repo (mobile/third party or dead)

### POST /party/rooms/{id}/speaker-invite/decline

Target từ chối lời mời speaker (idempotent), chỉ xoá cờ speaker_invite_pending.

- handler: PartyRoomController.declineSpeakerInvite (apps/core-api/src/modules/party-room/party-room.controller.ts:208); operationId: PartyRoomController_declineSpeakerInvite
- auth: Bearer JWT
- params: path:id*:string
- response 200 PartyRoomMemberDto: userId:string, role:enum(host|speaker|audience), joinedAt:string(date-time), disconnectedAt:string(date-time), nickname:string, speakerInvitePending:boolean
- FE callers: none in this repo (mobile/third party or dead)

### POST /party/webhooks/livekit

Nhận webhook LiveKit (participant_left, room_finished) cho room party-*: verify chữ ký JWT trên raw body, sai thì 401; lọc prefix party-, idempotent, set disconnected_at/host_disconnected_at hoặc đóng phòng.

- handler: PartyLivekitWebhookController.handle (apps/core-api/src/modules/party-room/webhooks/party-livekit-webhook.controller.ts:34); operationId: PartyLivekitWebhookController_handle (NOT in OpenAPI)
- auth: PUBLIC (webhook signature)

