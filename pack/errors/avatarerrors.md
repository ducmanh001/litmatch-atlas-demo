# Error codes AvatarErrors

id: errors:AvatarErrors
module: module:avatar

| code | http status at throw sites | throw sites | meaning | declared |
|---|---|---|---|---|
| AVATAR_ASSET_NOT_FOUND | NOT_FOUND | 1 |  | apps/core-api/src/modules/avatar/avatar.errors.ts:3 |
| AVATAR_ASSET_REQUIRES_PURCHASE | BAD_REQUEST | 1 | claim() gọi nhầm item phải mua — dùng buy() thay vì claim(). | apps/core-api/src/modules/avatar/avatar.errors.ts:5 |
| AVATAR_ASSET_IS_FREE | BAD_REQUEST | 1 | buy() gọi nhầm item free — dùng claim() thay vì buy(). | apps/core-api/src/modules/avatar/avatar.errors.ts:7 |
| AVATAR_ASSET_SLOT_MISMATCH | UNPROCESSABLE_ENTITY | 1 |  | apps/core-api/src/modules/avatar/avatar.errors.ts:8 |
| AVATAR_ITEM_NOT_OWNED | FORBIDDEN | 1 |  | apps/core-api/src/modules/avatar/avatar.errors.ts:9 |
