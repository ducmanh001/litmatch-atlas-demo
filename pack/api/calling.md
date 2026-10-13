# REST API of calling

id: api:calling
module: module:calling
prefix: /api/v1; success body {data, meta?}; errors are DomainException codes (see ../errors/)
see: ../modules/calling.md

### POST /calling/match-sessions/{matchSessionId}/join

Member của voice MatchSession đã confirmed tạo/lấy CallSession (unique match_session_id) và nhận token LiveKit TTL ngắn + livekitUrl theo region; call đã ended trả 409 CALL_ENDED. Throttle 20/phút.

- handler: CallingController.join (apps/core-api/src/modules/calling/calling.controller.ts:30); operationId: CallingController_join
- auth: Bearer JWT; throttle: { default: { limit: 20, ttl: minutes(1) } }
- params: path:matchSessionId*:string
- response 200 JoinCallDto: call:CallDto, call.id:string, call.matchSessionId:string, call.status:enum(pending|active|ended), call.startedAt:string(date-time), call.endedAt:string(date-time), call.endReason:enum(completed|free_limit|insufficient_balance|pending_timeout), call.callKind:enum(voice_match|friend), call.durationSeconds:number, call.billedMinutes:number, call.freeCallEndsAt:string(date-time), call.liked:boolean, call.matched:boolean, call.friendUserId:string, token:string, livekitUrl:string
- FE callers: none in this repo (mobile/third party or dead)

### POST /calling/friends/{friendUserId}/join

Mở/lấy friend call lâu dài (canonical pair, không MatchSession, không free timer) chỉ khi hai bên follow nhau và không block, rồi mint token. Throttle 20/phút; không đủ điều kiện trả 404 chung.

- handler: CallingController.joinFriend (apps/core-api/src/modules/calling/calling.controller.ts:55); operationId: CallingController_joinFriend
- auth: Bearer JWT; throttle: { default: { limit: 20, ttl: minutes(1) } }
- params: path:friendUserId*:string
- response 200 JoinCallDto: call:CallDto, call.id:string, call.matchSessionId:string, call.status:enum(pending|active|ended), call.startedAt:string(date-time), call.endedAt:string(date-time), call.endReason:enum(completed|free_limit|insufficient_balance|pending_timeout), call.callKind:enum(voice_match|friend), call.durationSeconds:number, call.billedMinutes:number, call.freeCallEndsAt:string(date-time), call.liked:boolean, call.matched:boolean, call.friendUserId:string, token:string, livekitUrl:string
- FE callers: none in this repo (mobile/third party or dead)

### GET /calling/calls/{id}

Poll fallback của call.ended: trả trạng thái call + liked/matched/friendUserId, chỉ member (404 gộp chống oracle). Không thuần đọc: gọi LiveKit listParticipants để đối soát phòng khi webhook mất và có thể chuyển call sang joined/active hoặc ended ngay trong request GET này (reconcileCallWithLivekit).

- handler: CallingController.getCall (apps/core-api/src/modules/calling/calling.controller.ts:76); operationId: CallingController_getCall
- auth: Bearer JWT
- params: path:id*:string
- response 200 CallDto: id:string, matchSessionId:string, status:enum(pending|active|ended), startedAt:string(date-time), endedAt:string(date-time), endReason:enum(completed|free_limit|insufficient_balance|pending_timeout), callKind:enum(voice_match|friend), durationSeconds:number, billedMinutes:number, freeCallEndsAt:string(date-time), liked:boolean, matched:boolean, friendUserId:string
- FE callers: none in this repo (mobile/third party or dead)

### POST /calling/calls/{id}/end

Member chủ động kết thúc call, idempotent (lock FOR UPDATE), chốt luôn MatchSession voice ở Matching, dọn room và phát call.ended.

- handler: CallingController.endCall (apps/core-api/src/modules/calling/calling.controller.ts:94); operationId: CallingController_endCall
- auth: Bearer JWT
- params: path:id*:string
- response 200 CallDto: id:string, matchSessionId:string, status:enum(pending|active|ended), startedAt:string(date-time), endedAt:string(date-time), endReason:enum(completed|free_limit|insufficient_balance|pending_timeout), callKind:enum(voice_match|friend), durationSeconds:number, billedMinutes:number, freeCallEndsAt:string(date-time), liked:boolean, matched:boolean, friendUserId:string
- FE callers: none in this repo (mobile/third party or dead)

### POST /calling/match-sessions/{matchSessionId}/end

Rời màn Voice Match: end call nếu đã có, rồi chốt MatchSession ended kể cả khi CallSession chưa được tạo.

- handler: CallingController.endMatchSession (apps/core-api/src/modules/calling/calling.controller.ts:113); operationId: CallingController_endMatchSession
- auth: Bearer JWT
- params: path:matchSessionId*:string
- response 204: 
- FE callers: none in this repo (mobile/third party or dead)

### POST /calling/calls/{id}/like

Ghi consent like immutable khi call active/ended (khoá CallSession, unique reaction); đủ like hai chiều thì tạo Friendship + Conversation trong CÙNG transaction. Friend call bị chặn.

- handler: CallingController.likeCall (apps/core-api/src/modules/calling/calling.controller.ts:126); operationId: CallingController_likeCall
- auth: Bearer JWT
- params: path:id*:string
- response 200 VoiceMatchLikeDto: liked:boolean, matched:boolean, friendUserId:string
- FE callers: none in this repo (mobile/third party or dead)

### POST /calling/webhooks/livekit

Nhận webhook LiveKit (participant_joined/left/connection_aborted, room_finished): verify chữ ký JWT trên raw body rồi mới cập nhật state; room không phải call-* bị bỏ qua, luôn 200 khi đã verify.

- handler: LivekitWebhookController.handle (apps/core-api/src/modules/calling/webhooks/livekit-webhook.controller.ts:34); operationId: LivekitWebhookController_handle (NOT in OpenAPI)
- auth: PUBLIC (webhook signature)

