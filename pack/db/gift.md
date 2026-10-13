# Database tables owned by module gift

id: db:gift
module: module:gift
tables: gift_events, gifts
see: ../modules/gift.md

### table: gift_events

Log 1 lần tặng quà — ghi trong CÙNG DB transaction với 2 chân ledger (qua `EconomyService.sendGift` withinTransaction). `transactionId` unique: 1 GiftEvent ↔ 1 Transaction tiền, replay idempotency trả lại đúng event cũ. Số tiền/điểm ở đây chỉ là SNAPSHOT hiển thị — nguồn sự thật vẫn là ledger_entries (docs/02).

Entity: GiftEvent (apps/core-api/src/modules/gift/entities/gift-event.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | uuid |  | gen_random_uuid() | PK |  |
| gift_id | uuid |  |  | FK→gifts.id |  |
| room_id | uuid | null |  | FK→party_rooms.id | Context tặng — ĐÚNG 1 trong 3 (CHECK `chk_gift_events_context`): trong Party Room (`roomId`), cho tác giả video (`videoId`) hoặc để mở chat profile (`profileUserId`). |
| sender_user_id | uuid |  |  | FK→users.id |  |
| receiver_user_id | uuid |  |  | FK→users.id |  |
| price_diamond | integer |  |  |  |  |
| points_awarded | integer |  |  |  | 0 khi người nhận là guest (docs/06 § Gift: guest không nhận điểm quy đổi). |
| points_rate_percent | integer |  |  |  | Snapshot GIFT_POINTS_RATE_PERCENT tại thời điểm tặng (economy-service.md § 6). |
| transaction_id | uuid |  |  | FK→transactions.id UQ |  |
| created_at | timestamp with time zone |  | now() |  |  |
| video_id | uuid | null |  |  |  |
| profile_user_id | uuid | null |  | FK→users.id |  |

Constraints: check chk_gift_events_context (((( CASE WHEN (room_id IS NULL) THEN 0 ELSE 1 END + CASE WHEN (video_id IS NULL) THEN 0 ELSE 1 END) + CASE WHEN (profile_user_id IS NULL) THEN 0 ELSE 1 END) =…; check chk_gift_events_no_self_gift ((sender_user_id <> receiver_user_id)); check chk_gift_events_points_lte_price ((points_awarded <= price_diamond)); unique uq_gift_events_transaction (transaction_id)

Indexes: idx_gift_events_receiver_created (receiver_user_id, created_at); idx_gift_events_room_created (room_id, created_at); UNIQUE uq_gift_events_transaction (transaction_id)

Access: gift(owner) r3/w6 via manager+manager.save(instance)+repository [apps/core-api/src/modules/gift/gift.service.ts:226, apps/core-api/src/modules/gift/gift.service.ts:255]

### table: gifts

Catalog quà (docs/services/gift-service.md § 2). Giá là DATA trong DB (đổi bằng UPDATE/admin) — server ĐỌC LẠI giá tại đúng thời điểm tặng, không tin giá client cache (docs/10 § Gift). `code` là khoá ổn định cho client map asset/animation.

Entity: Gift (apps/core-api/src/modules/gift/entities/gift.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | uuid |  | gen_random_uuid() | PK |  |
| code | character varying(64) |  |  | UQ |  |
| name | character varying(128) |  |  |  |  |
| price_diamond | integer |  |  |  |  |
| active | boolean |  | true |  |  |
| sort_order | integer |  | 0 |  |  |
| created_at | timestamp with time zone |  | now() |  |  |
| updated_at | timestamp with time zone |  | now() |  |  |

Constraints: check chk_gifts_price_positive ((price_diamond > 0)); unique uq_gifts_code (code)

Indexes: UNIQUE uq_gifts_code (code)

Access: gift(owner) r6/w3 via repository [apps/core-api/src/modules/gift/gift.service.ts:88, apps/core-api/src/modules/gift/gift.service.ts:96]

