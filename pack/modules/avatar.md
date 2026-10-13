# Module avatar

id: module:avatar
kind: core-api module
code: apps/core-api/src/modules/avatar

Sở hữu catalog layer avatar (avatar_assets, giá là DATA), quyền sở hữu item (user_avatar_items) và cấu hình đang trang bị (user_avatar_configs, 5 slot). Mua item trả phí là việc của Economy (Avatar chỉ gọi spendDiamond), Avatar không ghi ví/sổ cái và không đụng User.avatarId.

## Facts

- tables owned: avatar_assets, user_avatar_configs, user_avatar_items
- API: 7 endpoints, 0 public, 0 admin-only, 1 require Idempotency-Key, 0 with own throttle
- error codes: 5 in AvatarErrors
- tests: 1 unit spec files, 1 integration spec files
- history: 6 commits (0 fixes), last 2026-08-12
- docs: docs/services/avatar-service.md (last changed 2026-07-13)
- features: avatar-discovery-mood

## Where to read next

- tables: ../db/avatar.md
- API contract: ../api/avatar.md
- why it talks to other services: ../service-map/avatar.md and ../service-map/avatar-techniques.md
- error codes: ../errors/avatarerrors.md
- flows involving it: [avatar-buy-item](../flows/avatar-buy-item.md)
