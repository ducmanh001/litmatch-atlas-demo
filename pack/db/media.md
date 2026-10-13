# Database tables owned by module media

id: db:media
module: module:media
tables: image_assets
see: ../modules/media.md

### table: image_assets

Entity: ImageAsset (apps/core-api/src/modules/media/entities/image-asset.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | uuid |  | gen_random_uuid() | PK |  |
| owner_user_id | uuid |  |  | FK→users.id |  |
| storage_key | character varying(1024) |  |  | UQ |  |
| purpose | character varying(16) |  |  |  |  |
| content_type | character varying(64) |  |  |  |  |
| size_bytes | integer |  |  |  |  |
| status | character varying(16) |  | 'pending'::character varying |  |  |
| created_at | timestamp with time zone |  | now() |  |  |
| updated_at | timestamp with time zone |  | now() |  |  |

Constraints: check chk_image_assets_content_type (((content_type)::text = ANY ((ARRAY['image/jpeg'::character varying, 'image/png'::character varying, 'image/webp'::character varying, 'image/gif'::character v…; check chk_image_assets_purpose (((purpose)::text = ANY ((ARRAY['post'::character varying, 'message'::character varying, 'story'::character varying])::text[]))); check chk_image_assets_size ((size_bytes > 0)); check chk_image_assets_status (((status)::text = ANY ((ARRAY['pending'::character varying, 'ready'::character varying])::text[]))); unique image_assets_storage_key_key (storage_key)

Indexes: idx_image_assets_owner_created (owner_user_id, created_at); idx_image_assets_owner_status (owner_user_id, status); UNIQUE image_assets_storage_key_key (storage_key)

Access: media(owner) r1/w3 via repository [apps/core-api/src/modules/media/media.service.ts:55, apps/core-api/src/modules/media/media.service.ts:68]

