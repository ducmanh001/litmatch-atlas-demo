# web feature avatar

id: fe:feature:web:avatar
directory: apps/web/src/features/avatar/ (2 source files, 1 components, 1 spec files)

routes whose data comes from this feature: [/profile/edit](route-web-profile-edit.md)

## Hooks (data layer)

| hook | endpoint(s) | query key | polling | stale | invalidates on success | defined at |
|---|---|---|---|---|---|---|
| useMyAvatar | GET /avatar/me | avatarKeys.me |  |  |  | apps/web/src/features/avatar/api.ts:18 |
| useAvatarCatalog | GET /avatar/catalog | avatarKeys.catalog |  | 5 * 60 * 1000 |  | apps/web/src/features/avatar/api.ts:28 |
| useMyAvatarItems | GET /avatar/me/items | avatarKeys.myItems |  |  |  | apps/web/src/features/avatar/api.ts:40 |
| useClaimAvatarItem | POST /avatar/items/{assetId}/claim |  |  |  | invalidateQueries({ queryKey: avatarKeys.myItems }) | apps/web/src/features/avatar/api.ts:51 |
| useBuyAvatarItem | POST /avatar/items/{assetId}/buy |  |  |  | invalidateQueries({ queryKey: avatarKeys.myItems }); invalidateQueries({ queryKey: ['wallet', 'me'] }) | apps/web/src/features/avatar/api.ts:66 |
| useEquipAvatarItem | PUT /avatar/me/equip |  |  |  | invalidateQueries({ queryKey: avatarKeys.me }) | apps/web/src/features/avatar/api.ts:86 |

## Components

- apps/web/src/features/avatar/components/avatar-builder.tsx: 2 interactive elements, uses useState, useMyAvatar, useAvatarCatalog, useMyAvatarItems, useClaimAvatarItem, useBuyAvatarItem, useEquipAvatarItem, useIdempotencyKey

## Tests

- apps/web/src/features/avatar/components/avatar-builder.spec.tsx
