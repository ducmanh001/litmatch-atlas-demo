# web route /login

id: fe:route:web:/login
file: apps/web/src/app/login/page.tsx
layout: login
title: Đăng nhập
reached source files: 16 (static import closure); data features: none

## Data it reads (GET)

| endpoint | hook | query key | polling | stale time | module | defined at |
|---|---|---|---|---|---|---|
| GET /capabilities | useCapabilities | capabilityKeys.runtime |  | Infinity | [app-shell](../api/app-shell.md) | apps/web/src/shared/capabilities/api.ts:24 |

## Actions that change data (mutations)

| UI element and label | handler | hook | endpoint | module | call site |
|---|---|---|---|---|---|
| button.onClick: Đang vào… (auth.guestPending) | () => { if (!isCapabilityUsable(authCapabilities.guest)) { showToast(authCapabilities.guest.message, 'warn'); return; } | useMutation |  | ? | apps/web/src/shared/auth/login-form.tsx:340 |
| form.onSubmit: (no literal label) | (event) => { if (!phoneOtpAvailable) { event.preventDefault(); showToast(authCapabilities.phoneOtp.message, 'warn'); ret | useMutation |  | ? | apps/web/src/shared/auth/login-form.tsx:366 |
| button.onClick: t('auth.signInProvider', { provider: 'Google' }) | () => socialLogin.mutate('google') | useMutation |  | ? | apps/web/src/shared/auth/login-form.tsx:419 |
| button.onClick: t('auth.signInProvider', { provider: 'Google' }) | () => socialLogin.mutate('google') | useMutation |  | ? | apps/web/src/shared/auth/login-form.tsx:433 |
| button.onClick: t('auth.signInProvider', { provider: 'Google' }) | () => socialLogin.mutate('google') | useMutation |  | ? | apps/web/src/shared/auth/login-form.tsx:450 |
| form.onSubmit: (no literal label) | codeForm.handleSubmit((v) => verifyOtp.mutate({ phone: phase.phone, code: v.code }), ) | useMutation |  | ? | apps/web/src/shared/auth/login-form.tsx:473 |
| button.onClick: auth.resendWithCooldown (auth.resendWithCooldown) | () => resendOtp.mutate(phase.phone) | useMutation |  | ? | apps/web/src/shared/auth/login-form.tsx:528 |

Other write endpoints reachable (called outside a detected .mutate): POST /auth/otp/request (postOtpRequest apps/web/src/shared/auth/login-form.tsx:195); POST /auth/otp/verify (LoginForm apps/web/src/shared/auth/login-form.tsx:235); POST /auth/guest (LoginForm apps/web/src/shared/auth/login-form.tsx:250); POST /auth/social (LoginForm apps/web/src/shared/auth/login-form.tsx:279)

## Interactive elements (0)

none

## Realtime events listened

none

## Navigation destinations

- /home via router.replace (apps/web/src/shared/auth/login-form.tsx:287)

## Error codes handled in the FE

none referenced by code string (errors are shown through generic ApiError message handling)

## Guest and capability gating

- no isGuest check
- capability checks: useCapabilities() apps/web/src/shared/auth/login-form.tsx:99; legacyAuthCapabilities(t) apps/web/src/shared/auth/login-form.tsx:100; isCapabilityUsable(capability) apps/web/src/shared/auth/login-form.tsx:266; isCapabilityUsable(authCapabilities.phoneOtp) apps/web/src/shared/auth/login-form.tsx:325; isCapabilityUsable(authCapabilities.guest) apps/web/src/shared/auth/login-form.tsx:336

## Form validation (zod)

- phoneSchema (apps/web/src/shared/auth/login-form.tsx:101): phone=z.string().regex(VN_LOCAL_PHONE_PATTERN, t('auth.phoneInvalid'))
- codeSchema (apps/web/src/shared/auth/login-form.tsx:104): code=z.string().regex(/^[0-9]{6}$/u, t('auth.otpInvalid'))

## Browser storage and cookies

- localStorage GUEST_DEVICE_ID_STORAGE_KEY (apps/web/src/shared/auth/login-form.tsx:41)
- localStorage GUEST_DEVICE_ID_STORAGE_KEY (apps/web/src/shared/auth/login-form.tsx:44)
- staleTime Infinity (apps/web/src/shared/capabilities/api.ts:32)
- cookie-write  (apps/web/src/shared/i18n/locale-store.ts:37)

## i18n keys used (315)

- analytics.accept: Đồng ý
- analytics.consentDescription: Litmatch dùng phân tích thao tác và bản phát lại đã che toàn bộ nội dung chữ/input để tìm lỗi và cải thiện sản phẩm. Bạn có thể từ chối mà vẫn dùng đầy đủ tính năng.
- analytics.consentTitle: Cho phép phân tích trải nghiệm?
- analytics.decline: Từ chối
- analytics.settingsSection: Cải thiện trải nghiệm
- app.back: Quay lại
- app.conversation: Trò chuyện
- app.conversationError: Không tải được trò chuyện.
- app.conversationLoading: Đang tải trò chuyện…
- app.feed: Bảng tin
- app.matching: Ghép đôi
- app.messages: Tin nhắn
- app.more: Thêm
- app.partyEyebrow: Trò chuyện nhóm
- app.post: Bài viết
- app.profile: Hồ sơ
- app.publicProfile: Hồ sơ người dùng
- app.room: Phòng nhóm
- app.videoShort: Video ngắn
- app.wallet: Ví & VIP
- app.walletEyebrow: Số dư & gói VIP
- app.watchTogether: Đang xem chung
- auth.changePhone: ← Đổi số điện thoại
- auth.checkingProviders: Đang kiểm tra phương thức đăng nhập…
- auth.checkingSession: Đang kiểm tra phiên đăng nhập…
- … 290 more, see i18n-*.md

## Tests

no spec file next to this page or in its features

## Backend

modules: [app-shell](../modules/app-shell.md), [auth](../modules/auth.md)

service-map flows touching these endpoints: [auth-otp-login](../flows/auth-otp-login.md), [auth-guest-login-and-socket](../flows/auth-guest-login-and-socket.md), [auth-social-login](../flows/auth-social-login.md)
