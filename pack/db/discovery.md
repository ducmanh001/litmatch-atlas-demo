# Database tables owned by module discovery

id: db:discovery
module: module:discovery
tables: discovery_settings, user_locations
see: ../modules/discovery.md

### table: discovery_settings

Cài đặt hiển thị Nearby của 1 user — 1:1 với `users` (docs/services/discovery-service.md § Nearby). `nearbyVisible` mặc định **false** (opt-in). Reciprocity: chưa opt-in thì không xem được nearby của người khác (`NearbyService.listNearby` tự check trước khi query — không phải cột này quyết định người khác thấy mình, mà quyết định CHÍNH USER có được xem nearby hay không, đối xứng 2 chiều vì ai cũng phải bật mới xem được nhau). Tắt `nearbyVisible` PHẢI xoá `user_locations` cùng transaction (`NearbyService.setVisible`) — không giữ toạ độ cũ của user đã tắt tính năng.

Entity: DiscoverySetting (apps/core-api/src/modules/discovery/entities/discovery-setting.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| user_id | uuid |  |  | PK FK→users.id |  |
| nearby_visible | boolean |  | false |  |  |

Access: discovery(owner) r2/w1 via raw-sql+repository [apps/core-api/src/modules/discovery/nearby.service.ts:111, apps/core-api/src/modules/discovery/nearby.service.ts:144]

### table: user_locations

Vị trí Nearby của 1 user — 1:1 với `users` (docs/services/discovery-service.md § Nearby). Toạ độ đã QUANTIZE ~500m NGAY LÚC GHI (`NearbyService.setLocation`) — bảng này KHÔNG BAO GIỜ chứa toạ độ thô. Đây là dòng bảo vệ đầu tiên trong 3 lớp chống trilateration (quantize tại nguồn + jitter tất định theo cặp-theo-ngày lúc đọc + rate limit ghi/đọc). `updatedAt` dùng để derive "còn tươi" (`DISCOVERY_LOCATION_FRESHNESS_HOURS`) khi đọc — vị trí quá hạn tự biến mất khỏi kết quả nearby, KHÔNG có cron dọn hàng riêng cho bảng này. Tắt `DiscoverySetting.nearbyVisible` PHẢI xoá dòng tương ứng ở đây cùng transaction.

Entity: UserLocation (apps/core-api/src/modules/discovery/entities/user-location.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| user_id | uuid |  |  | PK FK→users.id |  |
| lat_quantized | double precision |  |  |  |  |
| lon_quantized | double precision |  |  |  |  |
| updated_at | timestamp with time zone |  | now() |  |  |

Constraints: check chk_user_locations_lat (((lat_quantized >= ('-90'::integer)::double precision) AND (lat_quantized <= (90)::double precision))); check chk_user_locations_lon (((lon_quantized >= ('-180'::integer)::double precision) AND (lon_quantized <= (180)::double precision)))

Indexes: idx_user_locations_lat_lon (lat_quantized, lon_quantized)

Access: discovery(owner) r2/w2 via raw-sql+repository [apps/core-api/src/modules/discovery/nearby.service.ts:99, apps/core-api/src/modules/discovery/nearby.service.ts:117]

