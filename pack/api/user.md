# REST API of user

id: api:user
module: module:user
prefix: /api/v1; success body {data, meta?}; errors are DomainException codes (see ../errors/)
see: ../modules/user.md

### GET /users/me

Đọc hồ sơ đầy đủ của chính mình (MyProfileDto) qua UserService.getByIdOrThrow.

- handler: UserController.getMe (apps/core-api/src/modules/user/user.controller.ts:41); operationId: UserController_getMe
- auth: Bearer JWT
- response 200 MyProfileDto: id:string, nickname:string, gender:enum(unknown|male|female|other), birthDate:string, region:string, avatarId:string, isGuest:boolean, interests:array<string>, seekingGender:enum(male|female|any), seekingAgeMin:number, seekingAgeMax:number
- FE callers: none in this repo (mobile/third party or dead)

### PATCH /users/me

Sửa hồ sơ của mình (nickname, gender, birthDate không ở tương lai, region, interests, seeking*); kiểm khoảng tuổi seeking sau khi gộp giá trị cũ và mới.

- handler: UserController.updateMe (apps/core-api/src/modules/user/user.controller.ts:50); operationId: UserController_updateMe
- auth: Bearer JWT
- body UpdateProfileDto: nickname:string, gender:enum(unknown|male|female|other), birthDate:string, region:string, interests:array<string>, seekingGender:enum(male|female|any), seekingAgeMin:number[minimum=18 maximum=99], seekingAgeMax:number[minimum=18 maximum=99]
- response 200 MyProfileDto: id:string, nickname:string, gender:enum(unknown|male|female|other), birthDate:string, region:string, avatarId:string, isGuest:boolean, interests:array<string>, seekingGender:enum(male|female|any), seekingAgeMin:number, seekingAgeMax:number
- FE callers: none in this repo (mobile/third party or dead)

### GET /users/me/privacy

Đọc privacy settings của mình; chưa có row thì trả DEFAULT_PRIVACY_SETTINGS.

- handler: UserController.getMyPrivacy (apps/core-api/src/modules/user/user.controller.ts:62); operationId: UserController_getMyPrivacy
- auth: Bearer JWT
- response 200 PrivacySettingsDto: showOnlineStatus:boolean, showDistance:boolean, searchableByPhone:boolean, hideProfile:boolean
- FE callers: none in this repo (mobile/third party or dead)

### PUT /users/me/privacy

Ghi privacy settings của mình bằng upsert theo userId (searchableByPhone, hideProfile, showOnlineStatus), giá trị thiếu quay về mặc định.

- handler: UserController.updateMyPrivacy (apps/core-api/src/modules/user/user.controller.ts:71); operationId: UserController_updateMyPrivacy
- auth: Bearer JWT
- body UpdatePrivacySettingsDto: showOnlineStatus*:boolean[default=true], showDistance*:boolean[default=true], searchableByPhone*:boolean[default=false], hideProfile*:boolean[default=false]
- response 200 PrivacySettingsDto: showOnlineStatus:boolean, showDistance:boolean, searchableByPhone:boolean, hideProfile:boolean
- FE callers: none in this repo (mobile/third party or dead)

### GET /users/{id}/presence

Cho biết user khác có đang online không: chỉ true khi họ bật showOnlineStatus VÀ Redis presence còn lease; không kiểm user tồn tại.

- handler: UserController.getPublicPresence (apps/core-api/src/modules/user/user.controller.ts:81); operationId: UserController_getPublicPresence
- auth: Bearer JWT
- params: path:id*:string
- response 200 UserPresenceDto: isOnline:boolean
- FE callers: none in this repo (mobile/third party or dead)

### GET /users/{id}

Hồ sơ công khai tối thiểu (PublicProfileDto) của user khác theo id; không kiểm hideProfile/ban (xem notes).

- handler: UserController.getPublicProfile (apps/core-api/src/modules/user/user.controller.ts:96); operationId: UserController_getPublicProfile
- auth: Bearer JWT
- params: path:id*:string
- response 200 PublicProfileDto: id:string, nickname:string, gender:enum(unknown|male|female|other), avatarId:string, interests:array<string>
- FE callers: none in this repo (mobile/third party or dead)

