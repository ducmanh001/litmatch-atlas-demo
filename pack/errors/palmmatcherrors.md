# Error codes PalmMatchErrors

id: errors:PalmMatchErrors
module: module:palm-match

| code | http status at throw sites | throw sites | meaning | declared |
|---|---|---|---|---|
| PALM_MATCH_CATEGORY_EMPTY | CONFLICT | 2 | Không có template active nào cho category — tránh 500/mảng rỗng khi chọn phần tử. | apps/core-api/src/modules/palm-match/palm-match.errors.ts:4 |
| PALM_MATCH_TARGET_NAME_TOO_LONG | UNPROCESSABLE_ENTITY | 1 | `targetName` vượt quá `PALM_MATCH_TARGET_NAME_MAX_LENGTH`. | apps/core-api/src/modules/palm-match/palm-match.errors.ts:6 |
| PALM_MATCH_SESSION_NOT_FOUND | NOT_FOUND | 2 | Session không tồn tại, caller không còn active trong session hoặc không phải participant. | apps/core-api/src/modules/palm-match/palm-match.errors.ts:8 |
| PALM_MATCH_SESSION_FINISHED | CONFLICT | 1 | Session đã terminal nên không nhận thêm flip/rating. | apps/core-api/src/modules/palm-match/palm-match.errors.ts:10 |
| PALM_MATCH_RATING_NOT_OPEN | CONFLICT | 1 | Rating chỉ mở sau khi cả hai participant tự lật lá của mình. | apps/core-api/src/modules/palm-match/palm-match.errors.ts:12 |
| PALM_MATCH_RATING_CONFLICT | CONFLICT | 2 | Một rating đã chốt không được đổi verdict khi retry. | apps/core-api/src/modules/palm-match/palm-match.errors.ts:14 |
