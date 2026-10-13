# Environment variables DISCOVERY_*

id: config:DISCOVERY
secret values are never printed

| var | app | class | type/valid | default | required | set in | meaning |
|---|---|---|---|---|---|---|---|
| DISCOVERY_GUEST_VISIBLE | core-api | other | boolean | false |  | .env.example:357 | Discovery — browse-only W1 (docs/services/discovery-service.md) Guest chưa gắn phone/social có xuất hiện trong browse không — chặn farm guest làm loãng pool |
| DISCOVERY_AGE_BUCKETS | core-api | other | string | 18,25,31,41 |  | .env.example:359 | Mốc tuổi tăng dần, phân tách dấu phẩy — bucket rộng, không lộ tuổi chính xác (vd 18,25,31,41 → 18-24, 25-30, 31-40, 41+); parse mảng ở service, không parse ở Joi cho đơn giản |
| DISCOVERY_LOCATION_QUANTIZE_DEGREES | core-api | other | number | 0.0045 |  | .env.example:363 | Nearby — W4 (docs/services/discovery-service.md § Nearby) Lưới quantize toạ độ tại nguồn (độ) — ~0.0045° xấp xỉ 500m ở vĩ độ Việt Nam (10-21°N); xấp xỉ do 1° kinh độ co lại theo c… |
| DISCOVERY_LOCATION_FRESHNESS_HOURS | core-api | tuning | number | 24 |  | .env.example:365 | Vị trí quá hạn tự biến mất khỏi nearby (derive khi đọc, không cron dọn `user_locations`) |
| DISCOVERY_NEARBY_RADIUS_KM | core-api | other | number | 20 |  | .env.example:367 | Bán kính tìm kiếm tối đa — dùng làm bounding-box prefilter trước khi tính haversine chính xác |
| DISCOVERY_DISTANCE_BUCKETS_KM | core-api | other | string | 1,3,5,10,20 |  | .env.example:369 | Mốc khoảng cách tăng dần (km), phân tách dấu phẩy — API chỉ trả bucket, không bao giờ trả khoảng cách/toạ độ chính xác (vd 1,3,5,10,20 → "<1km", "1-3km", ... "20km+") |
| DISCOVERY_LOCATION_UPDATE_RATE_LIMIT_PER_HOUR | core-api | tuning | number | 12 |  | .env.example:371 | Chống spam cập nhật vị trí (rò rỉ lộ trình di chuyển) — số lần ghi vị trí tối đa/giờ |
| DISCOVERY_NEARBY_QUERY_RATE_LIMIT_PER_HOUR | core-api | tuning | number | 30 |  | .env.example:373 | Chống dò quét trilateration bằng nhiều truy vấn liên tiếp — số lần truy vấn nearby tối đa/giờ |
| DISCOVERY_NEARBY_CANDIDATE_CAP | core-api | other | number | 500 |  | .env.example:375 | Trần an toàn số candidate lấy từ DB trước khi sort/paginate ở app (MVP: bounding-box btree + haversine, không PostGIS — xem docs/services/discovery-service.md § Nearby) |
