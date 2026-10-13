# REST API of palm-match

id: api:palm-match
module: module:palm-match
prefix: /api/v1; success body {data, meta?}; errors are DomainException codes (see ../errors/)
see: ../modules/palm-match.md

### GET /palm-match/reading

Trả nội dung bói giải trí {category, content, forDate}, deterministic theo hash(userId, category, ngày UTC); chỉ cần JWT, không lưu gì.

- handler: PalmMatchController.getReading (apps/core-api/src/modules/palm-match/palm-match.controller.ts:37); operationId: PalmMatchController_getReading
- auth: Bearer JWT
- params: query:category*:enum(love|career|health|general), query:targetName:string
- response 200 PalmMatchReadingDto: category:enum(love|career|health|general), content:string, forDate:string
- FE callers: none in this repo (mobile/third party or dead)

### POST /palm-match/queue

Vào hàng đợi (idempotent) và thử ghép ngay với entry cũ nhất còn hợp lệ; mỗi ứng viên phải qua SafetyService.canPair, serialize bằng advisory lock.

- handler: PalmMatchController.joinQueue (apps/core-api/src/modules/palm-match/palm-match.controller.ts:54); operationId: PalmMatchController_joinQueue
- auth: Bearer JWT
- response 200 PalmMatchStateDto: state:enum(idle|queued|active|completed), sessionId:string(uuid), queuedAt:string(date-time), expiresAt:string(date-time), myFlipped:boolean, opponentFlipped:boolean, mySign:PalmZodiacSignDto, mySign.key:string, mySign.symbol:string, mySign.name:string, opponentSign:PalmZodiacSignDto, opponentSign.key:string, opponentSign.symbol:string, opponentSign.name:string, compatibilityPercent:number, fortune:string, myRating:enum(like|skip), outcome:enum(matched|not_matched|expired|cancelled), partnerUserId:string
- FE callers: web apps/web/src/features/palm-match/api.ts:52

### GET /palm-match/current

State phục hồi/poll của queue hoặc phiên hiện tại, đồng thời lazy-expire hạn queue/phiên; là kênh DUY NHẤT để client biết đã được ghép.

- handler: PalmMatchController.getCurrent (apps/core-api/src/modules/palm-match/palm-match.controller.ts:66); operationId: PalmMatchController_getCurrent
- auth: Bearer JWT
- response 200 PalmMatchStateDto: state:enum(idle|queued|active|completed), sessionId:string(uuid), queuedAt:string(date-time), expiresAt:string(date-time), myFlipped:boolean, opponentFlipped:boolean, mySign:PalmZodiacSignDto, mySign.key:string, mySign.symbol:string, mySign.name:string, opponentSign:PalmZodiacSignDto, opponentSign.key:string, opponentSign.symbol:string, opponentSign.name:string, compatibilityPercent:number, fortune:string, myRating:enum(like|skip), outcome:enum(matched|not_matched|expired|cancelled), partnerUserId:string
- FE callers: web apps/web/src/features/palm-match/api.ts:27

### POST /palm-match/sessions/{id}/flip

Lật lá của chính caller (idempotent), quyền theo PalmMatchActiveParticipant(userId, sessionId) lấy từ JWT, khoá phiên pessimistic_write.

- handler: PalmMatchController.flip (apps/core-api/src/modules/palm-match/palm-match.controller.ts:77); operationId: PalmMatchController_flip
- auth: Bearer JWT
- params: path:id*:string
- response 200 PalmMatchStateDto: state:enum(idle|queued|active|completed), sessionId:string(uuid), queuedAt:string(date-time), expiresAt:string(date-time), myFlipped:boolean, opponentFlipped:boolean, mySign:PalmZodiacSignDto, mySign.key:string, mySign.symbol:string, mySign.name:string, opponentSign:PalmZodiacSignDto, opponentSign.key:string, opponentSign.symbol:string, opponentSign.name:string, compatibilityPercent:number, fortune:string, myRating:enum(like|skip), outcome:enum(matched|not_matched|expired|cancelled), partnerUserId:string
- FE callers: none in this repo (mobile/third party or dead)

### POST /palm-match/sessions/{id}/rating

Chốt like/skip một lần sau khi cả hai đã lật; cả hai like thì tạo Friendship + Conversation trong cùng transaction qua FriendService.ensureFriendship.

- handler: PalmMatchController.rate (apps/core-api/src/modules/palm-match/palm-match.controller.ts:88); operationId: PalmMatchController_rate
- auth: Bearer JWT
- params: path:id*:string
- body RatePalmMatchDto: rating*:enum(like|skip)
- response 200 PalmMatchStateDto: state:enum(idle|queued|active|completed), sessionId:string(uuid), queuedAt:string(date-time), expiresAt:string(date-time), myFlipped:boolean, opponentFlipped:boolean, mySign:PalmZodiacSignDto, mySign.key:string, mySign.symbol:string, mySign.name:string, opponentSign:PalmZodiacSignDto, opponentSign.key:string, opponentSign.symbol:string, opponentSign.name:string, compatibilityPercent:number, fortune:string, myRating:enum(like|skip), outcome:enum(matched|not_matched|expired|cancelled), partnerUserId:string
- FE callers: none in this repo (mobile/third party or dead)

### DELETE /palm-match/current

Huỷ queue, huỷ phiên đang active cho cả hai hoặc dismiss kết quả terminal để giải phóng slot enqueue lượt mới.

- handler: PalmMatchController.dismissCurrent (apps/core-api/src/modules/palm-match/palm-match.controller.ts:102); operationId: PalmMatchController_dismissCurrent
- auth: Bearer JWT
- response 200 PalmMatchStateDto: state:enum(idle|queued|active|completed), sessionId:string(uuid), queuedAt:string(date-time), expiresAt:string(date-time), myFlipped:boolean, opponentFlipped:boolean, mySign:PalmZodiacSignDto, mySign.key:string, mySign.symbol:string, mySign.name:string, opponentSign:PalmZodiacSignDto, opponentSign.key:string, opponentSign.symbol:string, opponentSign.name:string, compatibilityPercent:number, fortune:string, myRating:enum(like|skip), outcome:enum(matched|not_matched|expired|cancelled), partnerUserId:string
- FE callers: web apps/web/src/features/palm-match/api.ts:84

