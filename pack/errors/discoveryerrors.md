# Error codes DiscoveryErrors

id: errors:DiscoveryErrors
module: module:discovery

| code | http status at throw sites | throw sites | meaning | declared |
|---|---|---|---|---|
| DISCOVERY_CURSOR_INVALID | BAD_REQUEST | 1 |  | apps/core-api/src/modules/discovery/discovery.errors.ts:3 |
| DISCOVERY_FILTER_INVALID | UNPROCESSABLE_ENTITY | 1 |  | apps/core-api/src/modules/discovery/discovery.errors.ts:4 |
| DISCOVERY_NEARBY_NOT_OPTED_IN | FORBIDDEN | 1 |  | apps/core-api/src/modules/discovery/discovery.errors.ts:5 |
| DISCOVERY_NEARBY_LOCATION_MISSING | CONFLICT | 1 |  | apps/core-api/src/modules/discovery/discovery.errors.ts:6 |
| DISCOVERY_NEARBY_RATE_LIMITED | TOO_MANY_REQUESTS | 2 |  | apps/core-api/src/modules/discovery/discovery.errors.ts:7 |
| DISCOVERY_NEARBY_CURSOR_INVALID | BAD_REQUEST | 1 |  | apps/core-api/src/modules/discovery/discovery.errors.ts:8 |
| DISCOVERY_LOCATION_INVALID | UNPROCESSABLE_ENTITY | 1 |  | apps/core-api/src/modules/discovery/discovery.errors.ts:9 |
