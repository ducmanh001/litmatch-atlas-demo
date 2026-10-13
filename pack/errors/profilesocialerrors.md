# Error codes ProfileSocialErrors

id: errors:ProfileSocialErrors
module: module:friend

| code | http status at throw sites | throw sites | meaning | declared |
|---|---|---|---|---|
| PROFILE_SOCIAL_PROFILE_NOT_AVAILABLE | NOT_FOUND | 2 |  | apps/core-api/src/modules/friend/friend.errors.ts:16 |
| PROFILE_SOCIAL_SELF_ACTION | BAD_REQUEST | 3 |  | apps/core-api/src/modules/friend/friend.errors.ts:17 |
| PROFILE_SOCIAL_MESSAGE_GIFT_REQUIRED | PAYMENT_REQUIRED | 1 |  | apps/core-api/src/modules/friend/friend.errors.ts:18 |
