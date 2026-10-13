# REST API of gift

id: api:gift
module: module:gift
prefix: /api/v1; success body {data, meta?}; errors are DomainException codes (see ../errors/)
see: ../modules/gift.md

### GET /gifts

Liệt kê catalog quà đang bật (active) với giá server là nguồn sự thật; client map code sang asset/animation. Cần JWT (JwtAuthGuard toàn cục, không @Public).

- handler: GiftController.listCatalog (apps/core-api/src/modules/gift/gift.controller.ts:41); operationId: GiftController_listCatalog
- auth: Bearer JWT
- response 200 array<GiftDto>: [].id:string, [].code:string, [].name:string, [].priceDiamond:number
- FE callers: none in this repo (mobile/third party or dead)

### POST /party/rooms/{roomId}/gifts

Tặng quà trong phòng Party: bắt buộc Idempotency-Key, throttle 30/phút, cả người tặng và người nhận phải là member của phòng active; Economy trừ DIA/cộng PTS + ghi GiftEvent + notification trong 1 transaction, rồi publish gift.sent sau commit.

- handler: GiftController.sendGift (apps/core-api/src/modules/gift/gift.controller.ts:50); operationId: GiftController_sendGift
- auth: Bearer JWT; throttle: { default: { limit: 30, ttl: minutes(1) } }; requires Idempotency-Key header
- params: path:roomId*:string, header:Idempotency-Key*:string
- body SendGiftDto: giftId*:string, receiverUserId*:string
- response 200 GiftEventDto: id:string, giftId:string, giftCode:string, roomId:string, videoId:string, profileUserId:string, senderUserId:string, receiverUserId:string, priceDiamond:number, pointsAwarded:number, createdAt:string(date-time), replayed:boolean
- FE callers: none in this repo (mobile/third party or dead)

### POST /videos/{videoId}/gifts

Tặng quà cho tác giả video: người nhận suy từ video.authorUserId (client không gửi receiverUserId), video phải nhìn thấy được; cùng chốt tiền/idempotency với quà phòng, không fanout realtime, người nhận biết qua notification gift_received.

- handler: GiftController.sendVideoGift (apps/core-api/src/modules/gift/gift.controller.ts:75); operationId: GiftController_sendVideoGift
- auth: Bearer JWT; throttle: { default: { limit: 30, ttl: minutes(1) } }; requires Idempotency-Key header
- params: path:videoId*:string, header:Idempotency-Key*:string
- body SendVideoGiftDto: giftId*:string
- response 200 GiftEventDto: id:string, giftId:string, giftCode:string, roomId:string, videoId:string, profileUserId:string, senderUserId:string, receiverUserId:string, priceDiamond:number, pointsAwarded:number, createdAt:string(date-time), replayed:boolean
- FE callers: none in this repo (mobile/third party or dead)

### POST /profiles/{profileUserId}/gifts

Tặng quà để mở chat trực tiếp từ profile: kiểm tra block hai chiều (trước và lại TRONG transaction), khoá user đích, rồi ledger + GiftEvent + Conversation + ProfileChatContact + notification cùng 1 transaction; retry idempotent không tạo unlock thứ hai.

- handler: GiftController.sendProfileGift (apps/core-api/src/modules/gift/gift.controller.ts:99); operationId: GiftController_sendProfileGift
- auth: Bearer JWT; throttle: { default: { limit: 30, ttl: minutes(1) } }; requires Idempotency-Key header
- params: path:profileUserId*:string, header:Idempotency-Key*:string
- body SendProfileGiftDto: giftId*:string
- response 200 GiftEventDto: id:string, giftId:string, giftCode:string, roomId:string, videoId:string, profileUserId:string, senderUserId:string, receiverUserId:string, priceDiamond:number, pointsAwarded:number, createdAt:string(date-time), replayed:boolean
- FE callers: none in this repo (mobile/third party or dead)

