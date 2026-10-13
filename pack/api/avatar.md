# REST API of avatar

id: api:avatar
module: module:avatar
prefix: /api/v1; success body {data, meta?}; errors are DomainException codes (see ../errors/)
see: ../modules/avatar.md

### GET /avatar/catalog

Liệt kê catalog item đang bật, lọc theo slot tuỳ chọn, sắp theo sortOrder/code; giá do server quyết định. Cần JWT (JwtAuthGuard toàn cục, không @Public).

- handler: AvatarController.listCatalog (apps/core-api/src/modules/avatar/avatar.controller.ts:49); operationId: AvatarController_listCatalog
- auth: Bearer JWT
- response 200 array<AvatarAssetDto>: [].id:string, [].slot:enum(base|hair|face|outfit|accessory), [].code:string, [].name:string, [].imageUrl:string, [].zIndex:number, [].priceDiamond:number
- FE callers: web apps/web/src/features/avatar/api.ts:32

### GET /avatar/me/items

Liệt kê các item avatar user hiện tại đã sở hữu (join user_avatar_items với avatar_assets).

- handler: AvatarController.listMyItems (apps/core-api/src/modules/avatar/avatar.controller.ts:62); operationId: AvatarController_listMyItems
- auth: Bearer JWT
- response 200 array<AvatarAssetDto>: [].id:string, [].slot:enum(base|hair|face|outfit|accessory), [].code:string, [].name:string, [].imageUrl:string, [].zIndex:number, [].priceDiamond:number
- FE callers: web apps/web/src/features/avatar/api.ts:44

### POST /avatar/items/{assetId}/claim

Nhận item miễn phí (priceDiamond = 0): ghi sở hữu idempotent ON CONFLICT DO NOTHING, không đi qua Economy; item có giá bị 400 ASSET_REQUIRES_PURCHASE. Trả 204.

- handler: AvatarController.claim (apps/core-api/src/modules/avatar/avatar.controller.ts:73); operationId: AvatarController_claim
- auth: Bearer JWT
- params: path:assetId*:string
- response 204: 
- FE callers: web apps/web/src/features/avatar/api.ts:55

### POST /avatar/items/{assetId}/buy

Mua item trả phí: bắt buộc Idempotency-Key, Economy.spendDiamond trừ DIA (TransactionType.AvatarPurchase) rồi mới ghi sở hữu idempotent; item miễn phí bị 400 ASSET_IS_FREE; replay không trừ tiền lần hai.

- handler: AvatarController.buy (apps/core-api/src/modules/avatar/avatar.controller.ts:83); operationId: AvatarController_buy
- auth: Bearer JWT; requires Idempotency-Key header
- params: path:assetId*:string, header:Idempotency-Key*:string
- response 200 AvatarBuyResultDto: replayed:boolean
- FE callers: web apps/web/src/features/avatar/api.ts:70

### PUT /avatar/me/equip

Trang bị item vào slot: chống IDOR bằng 3 check (asset active, đúng slot, user đã sở hữu) rồi cập nhật cột slot trên user_avatar_configs, lazy-init config nếu chưa có; trả layer đã resolve.

- handler: AvatarController.equip (apps/core-api/src/modules/avatar/avatar.controller.ts:96); operationId: AvatarController_equip
- auth: Bearer JWT
- body EquipAvatarItemDto: slot*:enum(base|hair|face|outfit|accessory), avatarAssetId*:string
- response 200 AvatarConfigDto: userId:string, layers:array<AvatarAssetDto>, layers[].id:string, layers[].slot:enum(base|hair|face|outfit|accessory), layers[].code:string, layers[].name:string, layers[].imageUrl:string, layers[].zIndex:number, layers[].priceDiamond:number
- FE callers: web apps/web/src/features/avatar/api.ts:90

### GET /avatar/me

Avatar của chính mình: config + danh sách layer đầy đủ để client ghép theo zIndex; lazy-init config mặc định ở lần gọi đầu.

- handler: AvatarController.getMyAvatar (apps/core-api/src/modules/avatar/avatar.controller.ts:113); operationId: AvatarController_getMyAvatar
- auth: Bearer JWT
- response 200 AvatarConfigDto: userId:string, layers:array<AvatarAssetDto>, layers[].id:string, layers[].slot:enum(base|hair|face|outfit|accessory), layers[].code:string, layers[].name:string, layers[].imageUrl:string, layers[].zIndex:number, layers[].priceDiamond:number
- FE callers: web apps/web/src/features/avatar/api.ts:22

### GET /avatar/users/{userId}

Xem avatar người khác: chỉ đọc, không cần là bạn bè (avatar là thông tin hiển thị công khai), chưa có config thì trả layers rỗng; vẫn cần JWT.

- handler: AvatarController.getAvatarOf (apps/core-api/src/modules/avatar/avatar.controller.ts:127); operationId: AvatarController_getAvatarOf
- auth: Bearer JWT
- params: path:userId*:string
- response 200 AvatarConfigDto: userId:string, layers:array<AvatarAssetDto>, layers[].id:string, layers[].slot:enum(base|hair|face|outfit|accessory), layers[].code:string, layers[].name:string, layers[].imageUrl:string, layers[].zIndex:number, layers[].priceDiamond:number
- FE callers: none in this repo (mobile/third party or dead)

