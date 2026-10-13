# Error codes GiftErrors

id: errors:GiftErrors
module: module:gift

| code | http status at throw sites | throw sites | meaning | declared |
|---|---|---|---|---|
| GIFT_GIFT_NOT_FOUND | NOT_FOUND | 4 | Quà không tồn tại hoặc đã tắt khỏi catalog. | apps/core-api/src/modules/gift/gift.errors.ts:4 |
| GIFT_SELF_GIFT_FORBIDDEN | BAD_REQUEST | 3 | Không tự tặng quà cho chính mình (không có ý nghĩa kinh tế, chỉ để farm điểm). | apps/core-api/src/modules/gift/gift.errors.ts:6 |
| GIFT_SENDER_NOT_IN_ROOM | FORBIDDEN | 1 | Người tặng không phải member active của phòng. | apps/core-api/src/modules/gift/gift.errors.ts:8 |
| GIFT_RECEIVER_NOT_IN_ROOM | CONFLICT | 1 | Người nhận không phải member active của phòng. | apps/core-api/src/modules/gift/gift.errors.ts:10 |
| GIFT_CODE_ALREADY_EXISTS | CONFLICT | 1 | Admin tạo quà với `code` đã tồn tại (unique constraint `uq_gifts_code`). | apps/core-api/src/modules/gift/gift.errors.ts:12 |
