# Error codes AdminErrors

id: errors:AdminErrors
module: module:admin

| code | http status at throw sites | throw sites | meaning | declared |
|---|---|---|---|---|
| ADMIN_CANNOT_BAN_SELF | FORBIDDEN | 1 | Admin không được tự ban chính mình — tránh tự khoá mất quyền truy cập. | apps/core-api/src/modules/admin/admin.errors.ts:4 |
| ADMIN_PERMISSION_FORBIDDEN | FORBIDDEN | 1 |  | apps/core-api/src/modules/admin/admin.errors.ts:5 |
| ADMIN_PERMISSION_UNKNOWN | BAD_REQUEST,NOT_FOUND | 3 |  | apps/core-api/src/modules/admin/admin.errors.ts:6 |
| ADMIN_CANNOT_DISABLE_PERMISSION_CONTROL | CONFLICT | 1 |  | apps/core-api/src/modules/admin/admin.errors.ts:7 |
| ADMIN_CANNOT_CHANGE_OWN_ROLE | CONFLICT | 1 |  | apps/core-api/src/modules/admin/admin.errors.ts:8 |
| ADMIN_CANNOT_DEMOTE_LAST_ADMIN | CONFLICT | 1 |  | apps/core-api/src/modules/admin/admin.errors.ts:9 |
