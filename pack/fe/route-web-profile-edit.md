# web route /profile/edit

id: fe:route:web:/profile/edit
file: apps/web/src/app/(app)/profile/edit/page.tsx
layout: app (login required: AppChromeWithAuth)
title: Chỉnh sửa hồ sơ
reached source files: 19 (static import closure); data features: avatar, profile

Access: every route in the (app) group renders inside AppChromeWithAuth (AuthGate): no session -> redirected to /login. Guests can log in with a device id but several actions are limited (see "Guest and capability gating").

## UI action map card (human-authored, not verified against code)

PROFILE EDIT /profile/edit: Từ Profile / More

:
  - Đổi avatar
  - Nickname / ngày sinh
  - Giới tính / sở thích
  - Đang tìm kiếm / độ tuổi
  - Lưu → cập nhật Home/Profile

## Data it reads (GET)

| endpoint | hook | query key | polling | stale time | module | defined at |
|---|---|---|---|---|---|---|
| GET /users/me | useCurrentUser | currentUserKey |  | Infinity | [user](../api/user.md) | apps/web/src/shared/auth/use-current-user.ts:20 |
| GET /avatar/me | useMyAvatar | avatarKeys.me |  |  | [avatar](../api/avatar.md) | apps/web/src/features/avatar/api.ts:22 |
| GET /avatar/catalog | useAvatarCatalog | avatarKeys.catalog |  | 5 * 60 * 1000 | [avatar](../api/avatar.md) | apps/web/src/features/avatar/api.ts:32 |
| GET /avatar/me/items | useMyAvatarItems | avatarKeys.myItems |  |  | [avatar](../api/avatar.md) | apps/web/src/features/avatar/api.ts:44 |

## Actions that change data (mutations)

| UI element and label | handler | hook | endpoint | module | call site |
|---|---|---|---|---|---|
| (no matching UI element found; called from equip) |  | useEquipAvatarItem | PUT /avatar/me/equip | [avatar](../api/avatar.md) | apps/web/src/features/avatar/components/avatar-builder.tsx:87 |
| (no matching UI element found; called from onSelect) |  | useClaimAvatarItem | POST /avatar/items/{assetId}/claim | [avatar](../api/avatar.md) | apps/web/src/features/avatar/components/avatar-builder.tsx:107 |
| (no matching UI element found; called from onSelect) |  | useBuyAvatarItem | POST /avatar/items/{assetId}/buy | [avatar](../api/avatar.md) | apps/web/src/features/avatar/components/avatar-builder.tsx:123 |
| (no matching UI element found; called from ProfileForm) |  | useUpdateProfile | PATCH /users/me | [user](../api/user.md) | apps/web/src/features/profile/components/profile-form.tsx:80 |

## Interactive elements (8)

- button.onClick Đổi ảnh đại diện -> () => setBuilderOpen((open) => !open) (apps/web/src/features/profile/components/edit-profile-view.tsx:104)
- button.onClick Đổi ảnh đại diện -> () => setBuilderOpen((open) => !open) (apps/web/src/features/profile/components/edit-profile-view.tsx:114)
- button.onClick (no literal label) -> () => setActiveSlot(slot) (apps/web/src/features/avatar/components/avatar-builder.tsx:155)
- button.onClick `${item.name}${                   equipped ? ' (đang dùng)' : owned ? ' (đã sở h -> () => void onSelect(item) (apps/web/src/features/avatar/components/avatar-builder.tsx:194)
- form.onSubmit (no literal label) -> onSubmit (apps/web/src/features/profile/components/profile-form.tsx:132)
- button.onClick (no literal label) -> () => form.setValue('gender', option.value, { shouldValidate: true, s… (apps/web/src/features/profile/components/profile-form.tsx:170)
- button.onClick (no literal label) -> () => toggleInterest(tag) (apps/web/src/features/profile/components/profile-form.tsx:221)
- button.onClick (no literal label) -> () => form.setValue('seekingGender', option.value, { shouldDirty: tru… (apps/web/src/features/profile/components/profile-form.tsx:251)

## Realtime events listened

none

## Navigation destinations

- /profile via link (apps/web/src/features/profile/components/edit-profile-view.tsx:62)

## Error codes handled in the FE

none referenced by code string (errors are shown through generic ApiError message handling)

## Guest and capability gating

- no isGuest check
- no capability check

## Form validation (zod)

- updateProfileSchema (apps/web/src/features/profile/update-profile-schema.ts:4): nickname=z.string().trim().min(2, 'Tối thiểu 2 ký tự').max(50); gender=z.enum(['unknown', 'male', 'female', 'other']); birthDate=z .string() .refine( (value) => value === '' || !Number.isNaN(Date.parse(value)…; region=z .string() .trim() .refine( (value) => value === undefined || value === '' || …; interests=z .array(z.string().trim().min(1).max(32)) .max(5, 'Tối đa 5 sở thích'); seekingGender=z.enum(['male', 'female', 'any']); seekingAgeMin=z.number().int().min(18).max(99); seekingAgeMax=z.number().int().min(18).max(99)

## Browser storage and cookies

- staleTime Infinity (apps/web/src/shared/auth/use-current-user.ts:25)
- staleTime 5 * 60 * 1000 (apps/web/src/features/avatar/api.ts:36)
- staleTime 30_000 (apps/web/src/features/profile/api.ts:182)
- cookie-write  (apps/web/src/shared/i18n/locale-store.ts:37)

## i18n keys used (0)



## Tests

- apps/web/src/app/(app)/profile/edit/page.spec.tsx
- apps/web/src/features/avatar/components/avatar-builder.spec.tsx
- apps/web/src/features/profile/components/follow-list-view.spec.tsx
- apps/web/src/features/profile/components/profile-stats.spec.tsx
- apps/web/src/features/profile/components/profile-view.spec.tsx
- apps/web/src/features/profile/components/public-profile-view.spec.tsx
- apps/web/src/features/profile/update-profile-schema.spec.ts

## Backend

modules: [user](../modules/user.md), [avatar](../modules/avatar.md)

service-map flows touching these endpoints: [avatar-buy-item](../flows/avatar-buy-item.md)
