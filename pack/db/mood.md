# Database tables owned by module mood

id: db:mood
module: module:mood
tables: mood_presets, mood_status_events
see: ../modules/mood.md

### table: mood_presets

Catalog mood preset — data-driven (seed migration, pattern `avatar_assets`); danh sách hiển thị được cho user chọn là `active=true`, sắp theo `sortOrder`.

Entity: MoodPreset (apps/core-api/src/modules/mood/entities/mood-preset.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | uuid |  | gen_random_uuid() | PK |  |
| code | character varying(64) |  |  | UQ |  |
| label | character varying(128) |  |  |  |  |
| emoji | character varying(8) |  |  |  |  |
| active | boolean |  | true |  |  |
| sort_order | integer |  | 0 |  |  |

Constraints: unique uq_mood_presets_code (code)

Indexes: UNIQUE uq_mood_presets_code (code)

Access: mood(owner) r3/w0 via repository [apps/core-api/src/modules/mood/mood.service.ts:47, apps/core-api/src/modules/mood/mood.service.ts:63]

### table: mood_status_events

Log APPEND-ONLY set/clear mood — không update/xoá dòng cũ. "Mood hiện tại" của 1 user = dòng mới nhất (ORDER BY createdAt DESC LIMIT 1) mà KHÔNG phải `kind=clear` và chưa quá `expiresAt` — derive khi đọc (`MoodService.getCurrentMood`), không cron dọn. `idempotencyKey` unique ở DB (docs/05 § 5.10) — client retry (mất mạng giữa chừng) không tạo 2 dòng cho cùng 1 intent; service tự prefix theo `kind` (docs `mood.constants.ts`).

Entity: MoodStatusEvent (apps/core-api/src/modules/mood/entities/mood-status-event.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | uuid |  | gen_random_uuid() | PK |  |
| user_id | uuid |  |  | FK→users.id |  |
| preset_id | uuid | null |  | FK→mood_presets.id | NULL khi kind=Clear — không có preset cho 1 lần "tắt mood". |
| kind | character varying(8) |  |  |  |  |
| expires_at | timestamp with time zone | null |  |  | Snapshot lúc set — NULL khi kind=Clear (không có khái niệm hết hạn cho việc tắt mood). |
| idempotency_key | character varying(128) |  |  | UQ |  |
| created_at | timestamp with time zone |  | now() |  |  |

Constraints: check chk_mood_status_events_kind (((kind)::text = ANY ((ARRAY['set'::character varying, 'clear'::character varying])::text[]))); check chk_mood_status_events_set_has_preset (((((kind)::text = 'set'::text) AND (preset_id IS NOT NULL) AND (expires_at IS NOT NULL)) OR (((kind)::text = 'clear'::text) AND (preset_id IS NULL) AND (expir…; unique uq_mood_status_events_idempotency_key (idempotency_key)

Indexes: idx_mood_status_events_user_created (user_id, created_at); UNIQUE uq_mood_status_events_idempotency_key (idempotency_key)

Access: mood(owner) r2/w3 via repository [apps/core-api/src/modules/mood/mood.service.ts:83, apps/core-api/src/modules/mood/mood.service.ts:103]

