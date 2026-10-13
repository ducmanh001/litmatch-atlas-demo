# Module discovery

id: module:discovery
kind: core-api module
code: apps/core-api/src/modules/discovery

Sở hữu user_locations (toạ độ đã quantize ~500m) và discovery_settings (opt-in nearbyVisible), cộng logic browse/nearby: ghép bộ lọc, loại block/report, jitter khoảng cách, rate limit. Không sở hữu bảng users/privacy (module user), không sở hữu block/report (safety), không có logic mời Voice/Soul Match (matching).

## Facts

- tables owned: discovery_settings, user_locations
- touches tables of other modules: user_privacy_settings (owner user; read 1, write 0)
- API: 4 endpoints, 0 public, 0 admin-only, 0 require Idempotency-Key, 0 with own throttle
- env vars read: DISCOVERY_GUEST_VISIBLE, DISCOVERY_AGE_BUCKETS, DISCOVERY_LOCATION_UPDATE_RATE_LIMIT_PER_HOUR, DISCOVERY_LOCATION_QUANTIZE_DEGREES, DISCOVERY_NEARBY_QUERY_RATE_LIMIT_PER_HOUR, DISCOVERY_LOCATION_FRESHNESS_HOURS, DISCOVERY_NEARBY_RADIUS_KM, DISCOVERY_DISTANCE_BUCKETS_KM, DISCOVERY_NEARBY_CANDIDATE_CAP, REDIS_URL
- error codes: 7 in DiscoveryErrors
- tests: 3 unit spec files, 1 integration spec files
- history: 5 commits (0 fixes), last 2026-08-01
- docs: docs/services/discovery-service.md (last changed 2026-08-19)
- features: avatar-discovery-mood

## Where to read next

- tables: ../db/discovery.md
- API contract: ../api/discovery.md
- why it talks to other services: ../service-map/discovery.md and ../service-map/discovery-techniques.md
- error codes: ../errors/discoveryerrors.md
- flows involving it: [discovery-browse-and-nearby](../flows/discovery-browse-and-nearby.md)
