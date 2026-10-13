# Database tables owned by module admin

id: db:admin
module: module:admin
tables: admin_role_permissions
see: ../modules/admin.md

### table: admin_role_permissions

Entity: AdminRolePermission (apps/core-api/src/modules/admin/entities/admin-role-permission.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| role | character varying(16) |  |  | PK |  |
| permission | character varying(64) |  |  | PK |  |
| enabled | boolean |  |  |  |  |
| updated_at | timestamp with time zone |  | now() |  |  |

Constraints: check chk_admin_role_permissions_role (((role)::text = ANY ((ARRAY['moderator'::character varying, 'admin'::character varying])::text[])))

Access: admin(owner) r3/w1 via repository [apps/core-api/src/modules/admin/admin.service.ts:476, apps/core-api/src/modules/admin/admin.service.ts:491]

