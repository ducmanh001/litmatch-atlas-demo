# admin route /gifts

id: fe:route:admin:/gifts
file: apps/admin/src/features/gifts/pages/gifts-page.tsx:54
layout: admin (RequireAuth + role)
reached source files: 16 (static import closure); data features: gifts

## UI action map card (human-authored, not verified against code)

GIFTS /gifts: Catalog quà tặng

Catalog actions:
  - Tạo gift: code / name / price
  - Xem danh sách và trạng thái
  - Sửa price Diamond
  - Enable / Disable gift
Effect:
  - Catalog cập nhật cho Feed / Party / Chat
  - Ghi audit activity

## Data it reads (GET)

| endpoint | hook | query key | polling | stale time | module | defined at |
|---|---|---|---|---|---|---|
| GET /admin/gifts | useGiftsList | giftsKeys.all |  | 5000 | [admin](../api/admin.md) | apps/admin/src/features/gifts/api.ts:19 |

## Actions that change data (mutations)

| UI element and label | handler | hook | endpoint | module | call site |
|---|---|---|---|---|---|
| form.onSubmit: (no literal label) | form.handleSubmit((values) => { createGift.mutate( { ...values, sortOrder: 0 }, { onSuccess: () => { form.reset(); showT | useCreateGift | POST /admin/gifts | [admin](../api/admin.md) | apps/admin/src/features/gifts/pages/gifts-page.tsx:51 |
| (no matching UI element found; called from GiftsPage) |  | useUpdateGift | PATCH /admin/gifts/{id} | [admin](../api/admin.md) | apps/admin/src/features/gifts/pages/gifts-page.tsx:141 |
| (no matching UI element found; called from GiftsPage) |  | useUpdateGift | PATCH /admin/gifts/{id} | [admin](../api/admin.md) | apps/admin/src/features/gifts/pages/gifts-page.tsx:157 |

## Interactive elements (4)

- form.onSubmit (no literal label) -> form.handleSubmit((values) => { createGift.mutate( { ...values, sortO… (apps/admin/src/features/gifts/pages/gifts-page.tsx:48)
- PriceEditor.onSave (no literal label) -> onSavePrice (apps/admin/src/features/gifts/pages/gifts-page.tsx:217)
- Button.onClick gifts.disable (gifts.disable) -> onToggle (apps/admin/src/features/gifts/pages/gifts-page.tsx:225)
- form.onSubmit (no literal label) -> (e) => { e.preventDefault(); const raw = new FormData(e.currentTarget… (apps/admin/src/features/gifts/pages/gifts-page.tsx:247)

## Realtime events listened

none

## Navigation destinations

none

## Error codes handled in the FE

none referenced by code string (errors are shown through generic ApiError message handling)

## Guest and capability gating

- no isGuest check
- no capability check

## Form validation (zod)

- createGiftSchema (apps/admin/src/features/gifts/create-gift-schema.ts:3): code=z.string().min(1, 'Bắt buộc').max(64); name=z.string().min(1, 'Bắt buộc').max(128); priceDiamond=z.coerce.number().int().min(1, 'Phải >= 1')
- envSchema (apps/admin/src/shared/env.ts:8): VITE_API_URL=z.url(); VITE_AUTH_GOOGLE_CLIENT_ID=z.string().min(1).optional(); VITE_SENTRY_DSN=z.url().optional(); VITE_SENTRY_ENVIRONMENT=z.string().min(1).default('production'); VITE_SENTRY_RELEASE=z.string().max(200).optional(); VITE_PHONE_OTP_ENABLED=z .enum(['true', 'false']) .default('true') .transform((value) => value === 'tr…

## Browser storage and cookies

- staleTime 5000 (apps/admin/src/features/gifts/api.ts:22)
- cookie-write  (apps/admin/src/shared/i18n/locale-store.ts:28)

## i18n keys used (32)

- auth.changePhone: ← Đổi số điện thoại
- auth.guestAvailable: Có thể dùng tài khoản khách.
- common.disabled: 
- common.enabled: 
- common.loading: Đang tải…
- common.save: 
- common.tryAgain: 
- common.unknownError: Sự cố không xác định. Vui lòng tải lại.
- gifts.code: 
- gifts.codeColumn: 
- gifts.codePlaceholder: 
- gifts.create: 
- gifts.createTitle: 
- gifts.created: 
- gifts.creating: 
- gifts.disable: 
- gifts.empty: 
- gifts.enable: 
- gifts.name: 
- gifts.namePlaceholder: 
- gifts.onSale: 
- gifts.price: 
- gifts.priceColumn: 
- gifts.priceSaved: 
- gifts.status: 
- … 7 more, see i18n-*.md

## Tests

- apps/admin/src/features/gifts/pages/gifts-page.spec.tsx

## Backend

modules: [admin](../modules/admin.md)

service-map flows touching these endpoints: [admin-gift-catalog](../flows/admin-gift-catalog.md)
