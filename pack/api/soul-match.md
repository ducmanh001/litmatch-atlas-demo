# REST API of soul-match

id: api:soul-match
module: module:soul-match
prefix: /api/v1; success body {data, meta?}; errors are DomainException codes (see ../errors/)
see: ../modules/soul-match.md

### GET /soul-match/sessions/{id}

Poll trạng thái phòng chat ẩn danh (phase chatting|rating|closed, chatEndsAt/ratingEndsAt, verdict CỦA MÌNH, cờ matched); id lạ hoặc không phải thành viên đều trả 404 gộp để không dò được sessionId.

- handler: SoulMatchController.getSession (apps/core-api/src/modules/soul-match/soul-match.controller.ts:47); operationId: SoulMatchController_getSession
- auth: Bearer JWT
- params: path:id*:string
- response 200 SoulSessionViewDto: sessionId:string, phase:enum(chatting|rating|closed), chatEndsAt:string(date-time), ratingEndsAt:string(date-time), myVerdict:enum(rude|boring|like), matched:boolean
- FE callers: none in this repo (mobile/third party or dead)

### GET /soul-match/sessions/{id}/messages

Liệt kê message ẩn danh theo cursor seq, chỉ trả senderRole me|partner (không lộ userId đối phương); phòng đã closed trả 409 SOUL_MATCH_CHAT_NOT_OPEN vì lịch sử ẩn danh bị khoá.

- handler: SoulMatchController.listMessages (apps/core-api/src/modules/soul-match/soul-match.controller.ts:62); operationId: SoulMatchController_listMessages
- auth: Bearer JWT
- params: path:id*:string, query:limit:number, query:cursor:string
- response 200 SoulMessagesPageDto: items:array<SoulMessageDto>, items[].id:string, items[].senderRole:enum(me|partner), items[].content:string, items[].sentAt:string(date-time), meta:object, meta.nextCursor:string
- FE callers: none in this repo (mobile/third party or dead)

### POST /soul-match/sessions/{id}/messages

Gửi message ẩn danh: bắt buộc header Idempotency-Key (unique DB, retry trả lại message cũ), throttle 60 req/phút, chỉ khi phase chatting, re-check UserStatus.Banned, khoá hàng MatchSession rồi lưu và publish soul.message sau commit.

- handler: SoulMatchController.sendMessage (apps/core-api/src/modules/soul-match/soul-match.controller.ts:85); operationId: SoulMatchController_sendMessage
- auth: Bearer JWT; throttle: { default: { limit: 60, ttl: minutes(1) } }; requires Idempotency-Key header
- params: path:id*:string, header:Idempotency-Key*:string
- body SendSoulMessageDto: content*:string[maxLength=2000]
- response 201 SoulMessageDto: id:string, senderRole:enum(me|partner), content:string, sentAt:string(date-time)
- FE callers: none in this repo (mobile/third party or dead)

### POST /soul-match/sessions/{id}/rating

Chốt verdict rude|boring|like một lần (cùng verdict là replay, khác verdict là 409); khi cả hai like thì tạo Friendship + Conversation + import lịch sử chat trong CÙNG transaction rồi publish soul.matched.

- handler: SoulMatchController.rate (apps/core-api/src/modules/soul-match/soul-match.controller.ts:109); operationId: SoulMatchController_rate
- auth: Bearer JWT
- params: path:id*:string
- body RateSessionDto: verdict*:enum(rude|boring|like)
- response 200 RatingResultDto: verdict:enum(rude|boring|like), matched:boolean
- FE callers: none in this repo (mobile/third party or dead)

### POST /soul-match/sessions/{id}/end

Rời phòng: Matching đặt MatchSession sang ended cho cả hai (idempotent, trả 204) rồi publish soul.ended cho cả hai; Friendship đã tạo được giữ nguyên.

- handler: SoulMatchController.endSession (apps/core-api/src/modules/soul-match/soul-match.controller.ts:125); operationId: SoulMatchController_endSession
- auth: Bearer JWT
- params: path:id*:string
- response 204: 
- FE callers: none in this repo (mobile/third party or dead)

### GET /soul-match/sessions/{id}/partner

Trả PublicProfileDto của đối phương chỉ khi FriendService.areFriends=true; chưa match trả 403 SOUL_MATCH_PARTNER_LOCKED (Friendship là nguồn sự thật của unlock).

- handler: SoulMatchController.getPartner (apps/core-api/src/modules/soul-match/soul-match.controller.ts:138); operationId: SoulMatchController_getPartner
- auth: Bearer JWT
- params: path:id*:string
- response 200 PublicProfileDto: id:string, nickname:string, gender:enum(unknown|male|female|other), avatarId:string, interests:array<string>
- FE callers: none in this repo (mobile/third party or dead)

