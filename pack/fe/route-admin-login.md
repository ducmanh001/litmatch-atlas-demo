# admin route /login

id: fe:route:admin:/login
file: apps/admin/src/shared/auth/login-page.tsx:16
layout: login
reached source files: 14 (static import closure); data features: none

## Data it reads (GET)

| endpoint | hook | query key | polling | stale time | module | defined at |
|---|---|---|---|---|---|---|
| GET /capabilities | useCapabilities | capabilityKeys.runtime |  | Infinity | [app-shell](../api/app-shell.md) | apps/admin/src/shared/capabilities/api.ts:24 |

## Actions that change data (mutations)

| UI element and label | handler | hook | endpoint | module | call site |
|---|---|---|---|---|---|
| form.onSubmit: (no literal label) | (event) => { if (!isCapabilityUsable(authCapabilities.phoneOtp)) { event.preventDefault(); showToast(authCapabilities.ph | useMutation |  | ? | apps/admin/src/shared/auth/login-page.tsx:202 |
| Button.onClick: auth.openingGoogle (auth.openingGoogle) | () => socialLogin.mutate() | useMutation |  | ? | apps/admin/src/shared/auth/login-page.tsx:251 |
| form.onSubmit: (no literal label) | codeForm.handleSubmit((v) => verifyOtp.mutate({ phone: phase.phone, code: v.code }), ) | useMutation |  | ? | apps/admin/src/shared/auth/login-page.tsx:268 |

Other write endpoints reachable (called outside a detected .mutate): POST /auth/otp/request (LoginPage apps/admin/src/shared/auth/login-page.tsx:90); POST /auth/otp/verify (LoginPage apps/admin/src/shared/auth/login-page.tsx:111); POST /auth/social (LoginPage apps/admin/src/shared/auth/login-page.tsx:134)

## Interactive elements (0)

none

## Realtime events listened

none

## Navigation destinations

none

## Error codes handled in the FE

none referenced by code string (errors are shown through generic ApiError message handling)

## Guest and capability gating

- no isGuest check
- capability checks: useCapabilities() apps/admin/src/shared/auth/login-page.tsx:70; legacyAuthCapabilities() apps/admin/src/shared/auth/login-page.tsx:71; isCapabilityUsable(capability) apps/admin/src/shared/auth/login-page.tsx:127; isCapabilityUsable(authCapabilities.phoneOtp) apps/admin/src/shared/auth/login-page.tsx:196

## Form validation (zod)

- phoneSchema (apps/admin/src/shared/auth/login-page.tsx:29): phone=z.string().regex(VN_LOCAL_PHONE_PATTERN, t('auth.invalidPhone'))
- codeSchema (apps/admin/src/shared/auth/login-page.tsx:33): code=z.string().regex(/^[0-9]{6}$/u, t('auth.invalidOtp'))
- envSchema (apps/admin/src/shared/env.ts:8): VITE_API_URL=z.url(); VITE_AUTH_GOOGLE_CLIENT_ID=z.string().min(1).optional(); VITE_SENTRY_DSN=z.url().optional(); VITE_SENTRY_ENVIRONMENT=z.string().min(1).default('production'); VITE_SENTRY_RELEASE=z.string().max(200).optional(); VITE_PHONE_OTP_ENABLED=z .enum(['true', 'false']) .default('true') .transform((value) => value === 'tr…

## Browser storage and cookies

- staleTime Infinity (apps/admin/src/shared/capabilities/api.ts:32)
- cookie-write  (apps/admin/src/shared/i18n/locale-store.ts:28)

## i18n keys used (30)

- auth.appleUnavailable: 
- auth.changePhone: ← Đổi số điện thoại
- auth.checkingMethods: 
- auth.enterOtpSentTo: 
- auth.facebookUnavailable: 
- auth.googleLoginUnavailable: 
- auth.googleUnavailable: 
- auth.guestAvailable: Có thể dùng tài khoản khách.
- auth.invalidOtp: 
- auth.invalidPhone: 
- auth.invalidPhoneFormat: 
- auth.openingGoogle: 
- auth.or: 
- auth.otp: 
- auth.otpCode: 
- auth.otpSetupFailed: 
- auth.phone: 
- auth.phonePlaceholder: 
- auth.phoneUnavailable: 
- auth.sendOtp: 
- auth.sending: 
- auth.signIn: 
- auth.signInGoogle: 
- auth.signInPhoneOrGoogle: 
- auth.verifying: 
- … 5 more, see i18n-*.md

## Tests

no spec file next to this page or in its features

## Backend

modules: [app-shell](../modules/app-shell.md), [auth](../modules/auth.md)

service-map flows touching these endpoints: [auth-otp-login](../flows/auth-otp-login.md), [auth-social-login](../flows/auth-social-login.md)
