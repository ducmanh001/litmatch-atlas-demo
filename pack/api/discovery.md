# REST API of discovery

id: api:discovery
module: module:discovery
prefix: /api/v1; success body {data, meta?}; errors are DomainException codes (see ../errors/)
see: ../modules/discovery.md

### GET /discovery/browse

Duyệt user theo gender/tuổi (cursor createdAt,id); loại self + block/report 2 chiều + hide_profile + guest/banned; trả DiscoveryCardDto kèm ageBucket, ageMin>ageMax trả 422.

- handler: DiscoveryController.browse (apps/core-api/src/modules/discovery/discovery.controller.ts:33); operationId: DiscoveryController_browse
- auth: Bearer JWT
- params: query:gender:enum(unknown|male|female|other), query:ageMin:number, query:ageMax:number, query:limit:number, query:cursor:string
- response 200 DiscoveryCardsPageDto: items:array<DiscoveryCardDto>, items[].profile:PublicProfileDto, items[].profile.id:string, items[].profile.nickname:string, items[].profile.gender:enum(unknown|male|female|other), items[].profile.avatarId:string, items[].profile.interests:array<string>, items[].ageBucket:string, nextCursor:string
- FE callers: none in this repo (mobile/third party or dead)

### PUT /discovery/nearby/location

Ghi vị trí hiện tại, quantize ~500m ngay lúc ghi (không lưu toạ độ thô); rate limit DISCOVERY_LOCATION_UPDATE_RATE_LIMIT_PER_HOUR, vượt trả 429.

- handler: DiscoveryController.setLocation (apps/core-api/src/modules/discovery/discovery.controller.ts:49); operationId: DiscoveryController_setLocation
- auth: Bearer JWT
- body SetLocationDto: lat*:number[minimum=-90 maximum=90], lon*:number[minimum=-180 maximum=180]
- response 204: 
- FE callers: none in this repo (mobile/third party or dead)

### PUT /discovery/nearby/visible

Bật/tắt opt-in Nearby; tắt thì xoá user_locations cùng transaction với cập nhật discovery_settings.

- handler: DiscoveryController.setVisible (apps/core-api/src/modules/discovery/discovery.controller.ts:62); operationId: DiscoveryController_setVisible
- auth: Bearer JWT
- body SetNearbyVisibleDto: visible*:boolean
- response 204: 
- FE callers: none in this repo (mobile/third party or dead)

### GET /discovery/nearby

Liệt kê người gần: yêu cầu caller đã opt-in và vị trí còn tươi (403/409 nếu không), rate limit truy vấn, chỉ trả distanceBucket đã jitter, không trả toạ độ/km.

- handler: DiscoveryController.nearby (apps/core-api/src/modules/discovery/discovery.controller.ts:75); operationId: DiscoveryController_nearby
- auth: Bearer JWT
- params: query:gender:enum(unknown|male|female|other), query:ageMin:number, query:ageMax:number, query:limit:number, query:cursor:string
- response 200 NearbyCardsPageDto: items:array<NearbyCardDto>, items[].profile:PublicProfileDto, items[].profile.id:string, items[].profile.nickname:string, items[].profile.gender:enum(unknown|male|female|other), items[].profile.avatarId:string, items[].profile.interests:array<string>, items[].distanceBucket:string, nextCursor:string
- FE callers: none in this repo (mobile/third party or dead)

