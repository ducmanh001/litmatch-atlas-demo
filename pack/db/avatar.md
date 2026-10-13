# Database tables owned by module avatar

id: db:avatar
module: module:avatar
tables: avatar_assets, user_avatar_configs, user_avatar_items
see: ../modules/avatar.md

### table: avatar_assets

Catalog item avatar — layer ghép hình (docs/services/avatar-service.md § 1). Giá là DATA trong DB (seed ở migration, đổi bằng UPDATE/admin, KHÔNG phải env config) — server đọc lại giá tại thời điểm mua, giống Gift (docs/10 § Gift). `zIndex` quyết định thứ tự ghép layer client-side.

Entity: AvatarAsset (apps/core-api/src/modules/avatar/entities/avatar-asset.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | uuid |  | gen_random_uuid() | PK |  |
| slot | character varying(16) |  |  |  |  |
| code | character varying(64) |  |  | UQ |  |
| name | character varying(128) |  |  |  |  |
| image_url | character varying(2048) |  |  |  |  |
| z_index | integer |  |  |  |  |
| price_diamond | integer |  | 0 |  | 0 = item free/mặc định — claim() không cần đi qua Economy. |
| active | boolean |  | true |  |  |
| sort_order | integer |  | 0 |  |  |

Constraints: check chk_avatar_assets_price_nonneg ((price_diamond >= 0)); unique uq_avatar_assets_code (code)

Indexes: idx_avatar_assets_slot_active (slot, active); UNIQUE uq_avatar_assets_code (code)

Access: avatar(owner) r5/w0 via repository [apps/core-api/src/modules/avatar/avatar.service.ts:39, apps/core-api/src/modules/avatar/avatar.service.ts:48]

### table: user_avatar_configs

Item đang trang bị mỗi slot (docs/services/avatar-service.md § 1) — 1 dòng/user, PK = userId (không dùng `BaseAppEntity` vì PK nghiệp vụ riêng). Lazy-init lúc `getMyAvatar` lần đầu, không hook vào Auth/User lúc đăng ký.

Entity: UserAvatarConfig (apps/core-api/src/modules/avatar/entities/user-avatar-config.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| user_id | uuid |  |  | PK FK→users.id |  |
| base_asset_id | uuid | null |  | FK→avatar_assets.id |  |
| hair_asset_id | uuid | null |  | FK→avatar_assets.id |  |
| face_asset_id | uuid | null |  | FK→avatar_assets.id |  |
| outfit_asset_id | uuid | null |  | FK→avatar_assets.id |  |
| accessory_asset_id | uuid | null |  | FK→avatar_assets.id |  |
| updated_at | timestamp with time zone |  | now() |  |  |

Access: avatar(owner) r4/w3 via manager+manager.save(instance)+repository [apps/core-api/src/modules/avatar/avatar.service.ts:121, apps/core-api/src/modules/avatar/avatar.service.ts:123]

### table: user_avatar_items

Sở hữu item avatar — nguồn sự thật "user có item này chưa" (docs/services/avatar-service.md § 1), tách khỏi `UserAvatarConfig` (đang trang bị) vì 1 user có thể sở hữu nhiều item cùng slot.

Entity: UserAvatarItem (apps/core-api/src/modules/avatar/entities/user-avatar-item.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | uuid |  | gen_random_uuid() | PK |  |
| user_id | uuid |  |  | FK→users.id |  |
| avatar_asset_id | uuid |  |  | FK→avatar_assets.id |  |
| acquired_at | timestamp with time zone |  | now() |  |  |

Constraints: unique uq_user_avatar_items_pair (user_id,avatar_asset_id)

Indexes: UNIQUE uq_user_avatar_items_pair (user_id, avatar_asset_id)

Access: avatar(owner) r2/w1 via querybuilder+repository [apps/core-api/src/modules/avatar/avatar.service.ts:46, apps/core-api/src/modules/avatar/avatar.service.ts:110]

