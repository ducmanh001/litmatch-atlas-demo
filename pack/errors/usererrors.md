# Error codes UserErrors

id: errors:UserErrors
module: module:user

| code | http status at throw sites | throw sites | meaning | declared |
|---|---|---|---|---|
| USER_PROFILE_NOT_FOUND | NOT_FOUND | 3 |  | apps/core-api/src/modules/user/user.errors.ts:3 |
| USER_PROFILE_BIRTH_DATE_INVALID | UNPROCESSABLE_ENTITY | 1 |  | apps/core-api/src/modules/user/user.errors.ts:4 |
| USER_SEEKING_AGE_RANGE_INVALID | UNPROCESSABLE_ENTITY | 1 | Khoảng tuổi "Đang tìm kiếm" min > max sau khi gộp giá trị mới với giá trị đã lưu. | apps/core-api/src/modules/user/user.errors.ts:6 |
