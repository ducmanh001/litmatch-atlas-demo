# Feature slice: avatar-discovery-mood

id: slice:avatar-discovery-mood
status: implemented; owner: core-api/avatar, core-api/discovery, core-api/mood

Avatar, discovery/nearby and mood

This file follows ONE feature through every layer. Layers are joined by the actual endpoint calls found in the frontend code, so it shows what the code does, not what a plan says.

## 1. Product contract and docs

- registry contracts: openapi/core-api.json, docs/services/avatar-service.md, docs/services/discovery-service.md, docs/services/mood-service.md
- registry verification tests: apps/core-api/src/modules/avatar/avatar.integration.spec.ts, apps/core-api/src/modules/discovery/discovery.integration.spec.ts, apps/core-api/src/modules/mood/mood.integration.spec.ts
- service docs: docs/services/avatar-service.md, docs/services/discovery-service.md, docs/services/mood-service.md

## 2. Frontend

| route | app | endpoints of this feature it calls | page |
|---|---|---|---|
| [/profile/edit](../fe/route-web-profile-edit.md) | web | GET /avatar/me; GET /avatar/catalog; GET /avatar/me/items; POST /avatar/items/{assetId}/claim; POST /avatar/items/{assetId}/buy; PUT /avatar/me/equip | app/(app)/profile/edit/page.tsx |
| [/discovery](../fe/route-web-discovery.md) | web | GET /discovery/browse; GET /discovery/nearby; PUT /discovery/nearby/visible; PUT /discovery/nearby/location | app/(app)/discovery/page.tsx |

FE features (directories): [web/avatar](../fe/feature-web-avatar.md), [web/profile](../fe/feature-web-profile.md), [web/discovery](../fe/feature-web-discovery.md), [web/notification](../fe/feature-web-notification.md), [web/wallet](../fe/feature-web-wallet.md)

UI action map card for /profile/edit: : Đổi avatar / Nickname / ngày sinh / Giới tính / sở thích / Đang tìm kiếm / độ tuổi / Lưu → cập nhật Home/Profile

User actions that mutate this feature's data:
- useEquipAvatarItem -> PUT /avatar/me/equip (apps/web/src/features/avatar/components/avatar-builder.tsx:87)
- useClaimAvatarItem -> POST /avatar/items/{assetId}/claim (apps/web/src/features/avatar/components/avatar-builder.tsx:107)
- useBuyAvatarItem -> POST /avatar/items/{assetId}/buy (apps/web/src/features/avatar/components/avatar-builder.tsx:123)
- useSetNearbyVisible -> PUT /discovery/nearby/visible (apps/web/src/app/(app)/discovery/page.tsx:361)
- useSetLocation -> PUT /discovery/nearby/location (apps/web/src/features/discovery/components/nearby-opt-in.tsx:111)
- useSetNearbyVisible -> PUT /discovery/nearby/visible (apps/web/src/features/discovery/components/nearby-opt-in.tsx:118)

## 3. API

| endpoint | auth | idempotency | throttle | FE callers |
|---|---|---|---|---|
| GET /avatar/catalog ([avatar](../api/avatar.md)) | JWT |  |  | 1 |
| GET /avatar/me/items ([avatar](../api/avatar.md)) | JWT |  |  | 1 |
| POST /avatar/items/{assetId}/claim ([avatar](../api/avatar.md)) | JWT |  |  | 1 |
| POST /avatar/items/{assetId}/buy ([avatar](../api/avatar.md)) | JWT | required |  | 1 |
| PUT /avatar/me/equip ([avatar](../api/avatar.md)) | JWT |  |  | 1 |
| GET /avatar/me ([avatar](../api/avatar.md)) | JWT |  |  | 1 |
| GET /avatar/users/{userId} ([avatar](../api/avatar.md)) | JWT |  |  | 0 |
| GET /discovery/browse ([discovery](../api/discovery.md)) | JWT |  |  | 1 |
| PUT /discovery/nearby/location ([discovery](../api/discovery.md)) | JWT |  |  | 1 |
| PUT /discovery/nearby/visible ([discovery](../api/discovery.md)) | JWT |  |  | 1 |
| GET /discovery/nearby ([discovery](../api/discovery.md)) | JWT |  |  | 1 |
| GET /mood/presets ([mood](../api/mood.md)) | JWT |  |  | 0 |
| GET /mood/status/me ([mood](../api/mood.md)) | JWT |  |  | 0 |
| GET /mood/status/{userId} ([mood](../api/mood.md)) | JWT |  |  | 0 |
| POST /mood/status ([mood](../api/mood.md)) | JWT | required |  | 0 |
| DELETE /mood/status ([mood](../api/mood.md)) | JWT | required |  | 0 |


## 4. Backend flows (service map)

- [mood-status-visibility](../flows/mood-status-visibility.md): Trạng thái tâm trạng và quyền xem (5 endpoints of this feature)
- [discovery-browse-and-nearby](../flows/discovery-browse-and-nearby.md): Khám phá người dùng và gần đây (4 endpoints of this feature)
- [avatar-buy-item](../flows/avatar-buy-item.md): Mua vật phẩm avatar bằng kim cương (3 endpoints of this feature)

## 5. Data

- tables owned by the module(s): `avatar_assets`, `discovery_settings`, `mood_presets`, `mood_status_events`, `user_avatar_configs`, `user_avatar_items`, `user_locations` (see ../db/avatar.md, ../db/discovery.md, ../db/mood.md)
- status vocabularies enforced by CHECK:
  - mood_status_events: set | clear

## 6. Events

No realtime event is produced by these modules.

## 7. Errors

- error code groups: [AvatarErrors](../errors/avatarerrors.md), [DiscoveryErrors](../errors/discoveryerrors.md), [MoodErrors](../errors/mooderrors.md)
- codes the FE references for this feature: none

## 8. Configuration

env vars read by the module(s): `DISCOVERY_GUEST_VISIBLE`, `DISCOVERY_AGE_BUCKETS`, `DISCOVERY_LOCATION_UPDATE_RATE_LIMIT_PER_HOUR`, `DISCOVERY_LOCATION_QUANTIZE_DEGREES`, `DISCOVERY_NEARBY_QUERY_RATE_LIMIT_PER_HOUR`, `DISCOVERY_LOCATION_FRESHNESS_HOURS`, `DISCOVERY_NEARBY_RADIUS_KM`, `DISCOVERY_DISTANCE_BUCKETS_KM`, `DISCOVERY_NEARBY_CANDIDATE_CAP`, `REDIS_URL`, `MOOD_STATUS_TTL_HOURS` (see ../config/)

## 9. Tests

- avatar: 1 unit spec files, 1 integration spec files
- discovery: 3 unit spec files, 1 integration spec files
- mood: 1 unit spec files, 1 integration spec files
- e2e files that call these endpoints: none
- FE specs: apps/web/src/app/(app)/profile/edit/page.spec.tsx, apps/web/src/features/avatar/components/avatar-builder.spec.tsx, apps/web/src/features/profile/components/follow-list-view.spec.tsx, apps/web/src/features/profile/components/profile-stats.spec.tsx, apps/web/src/features/profile/components/profile-view.spec.tsx, apps/web/src/features/profile/components/public-profile-view.spec.tsx, apps/web/src/features/profile/update-profile-schema.spec.ts, apps/web/src/app/(app)/discovery/page.spec.tsx, apps/web/src/features/discovery/components/discovery-detail-sheet.spec.tsx, apps/web/src/features/notification/api.spec.ts, apps/web/src/features/notification/components/notification-bell.spec.tsx, apps/web/src/features/notification/notification-copy.spec.ts

## 10. Known issues (linked from the ledger)

none linked

See ../issues/index.md.
