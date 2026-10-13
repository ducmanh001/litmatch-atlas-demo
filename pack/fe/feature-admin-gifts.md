# admin feature gifts

id: fe:feature:admin:gifts
directory: apps/admin/src/features/gifts/ (3 source files, 1 components, 1 spec files)

routes whose data comes from this feature: [/gifts](route-admin-gifts.md)

## Hooks (data layer)

| hook | endpoint(s) | query key | polling | stale | invalidates on success | defined at |
|---|---|---|---|---|---|---|
| useGiftsList | GET /admin/gifts | giftsKeys.all |  | 5000 |  | apps/admin/src/features/gifts/api.ts:15 |
| useCreateGift | POST /admin/gifts |  |  |  | invalidateQueries({ queryKey: giftsKeys.all }) | apps/admin/src/features/gifts/api.ts:26 |
| useUpdateGift | PATCH /admin/gifts/{id} |  |  |  | invalidateQueries({ queryKey: giftsKeys.all }) | apps/admin/src/features/gifts/api.ts:37 |

## Components

- apps/admin/src/features/gifts/pages/gifts-page.tsx: 4 interactive elements, uses useT, useGiftsList, useCreateGift, useUpdateGift, useForm

## Validation schemas

- createGiftSchema (apps/admin/src/features/gifts/create-gift-schema.ts:3): code=z.string().min(1, 'Bắt buộc').max(64); name=z.string().min(1, 'Bắt buộc').max(128); priceDiamond=z.coerce.number().int().min(1, 'Phải >= 1')

## Tests

- apps/admin/src/features/gifts/pages/gifts-page.spec.tsx
