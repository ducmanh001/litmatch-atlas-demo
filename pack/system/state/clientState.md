# Cache and state: Redis, locks, idempotency, client state: clientState

id: system:state:clientState

Written by an AI agent from the code; every file:line quote was checked automatically (exists within ±3 lines) but NO independent verification pass has run yet. Treat claims as strong leads; open the cited file before editing.

### web

### serverStateCache

- library: @tanstack/react-query; không có global store, state server nằm trong QueryClient

- defaults: staleTime 30000 ms, retry theo shouldRetryQuery (ngân sách retry toàn cục nhỏ, không retry 4xx), refetchOnWindowFocus false, refetchOnReconnect theo shouldRefetchOnReconnect, refetchIntervalInBackground false

### perFeatureStaleTime

### #1

- feature: avatar (api.ts)
- staleTime: 5 * 60 * 1000 (5 phút)
- evidence: apps/web/src/features/avatar/api.ts:36

### #2

- feature: profile (api.ts, 1 query)
- staleTime: 30_000
- evidence: apps/web/src/features/profile/api.ts:182

### #3

- feature: wallet (api.ts, query tại dòng 37)
- staleTime: Infinity
- evidence: apps/web/src/features/wallet/api.ts:37

### #4

- feature: wallet (api.ts, query tại dòng 48)
- staleTime: Infinity
- evidence: apps/web/src/features/wallet/api.ts:48

### #5

- feature: wallet (api.ts, query tại dòng 92)
- staleTime: Infinity
- evidence: apps/web/src/features/wallet/api.ts:92

### #6

- feature: current user (shared/auth)
- staleTime: Infinity
- evidence: apps/web/src/shared/auth/use-current-user.ts:25

### #7

- feature: capabilities runtime (shared)
- staleTime: Infinity
- evidence: apps/web/src/shared/capabilities/api.ts:32

### browserStorage

### localStorage

- key: litmatch-web.csrf-token
- purpose: Persist csrfToken để lần refresh đầu sau reload còn header CSRF; đồng bộ giữa tab qua storage event. Refresh token không nằm ở đây.
- evidence: apps/web/src/shared/api/client.ts:11

### localStorage

- key: litmatch.guestDeviceId
- purpose: deviceId của guest, sinh ở client và gửi lúc đăng nhập guest.
- evidence: apps/web/src/shared/auth/login-form.tsx:35

### localStorage

- key: litmatch-theme
- purpose: Lựa chọn giao diện sáng/tối.
- evidence: apps/web/src/shared/ui/theme-script.ts:1

### localStorage

- key: litmatch-last-dark-mood
- purpose: Nhớ chế độ tối gần nhất cho nút đổi theme.
- evidence: apps/web/src/shared/ui/theme-toggle-button.tsx:14

### memory

- key: accessToken, guestDeviceToken (TokenStore)
- purpose: Chỉ giữ trong bộ nhớ tab, mất khi reload; khôi phục bằng refresh cookie.
- evidence: libs/api-client/src/lib/token-store.ts:85

### cookies

### litmatch-web.locale

- purpose: Ngôn ngữ giao diện, ghi bằng document.cookie.
- attributes: Path=/; Max-Age=31536000; SameSite=Lax; JS-set (không HttpOnly)
- evidence: apps/web/src/shared/i18n/locale-store.ts:37

### litmatch-web.product-analytics-consent

- purpose: Lưu đồng ý analytics.
- attributes: Path=/; Max-Age=31536000 s; SameSite=Lax; JS-set
- evidence: apps/web/src/shared/analytics/product-analytics.ts:28

### refresh_token, csrf_token (do core-api set)

- purpose: Refresh token xoay vòng và nửa cookie của CSRF double-submit; JS không đọc được.
- attributes: httpOnly; Path=/api/v1/auth; secure khi production; sameSite theo AUTH_COOKIE_SAME_SITE
- evidence: apps/core-api/src/modules/auth/auth.cookies.ts:59

- realtimeHandling: Một socket Socket.IO cho cả app; socket là kênh delta, sau khi nối lại onReconnected refetch REST, khi transport khoẻ thì giảm tần suất polling. Một số query có refetchInterval làm fallback: friend-chat, matching, movie-match, notification (unread count), palm-match, party-room, soul-match, voice-match.

- authRefreshFlow: 401 dẫn tới rotateSession single-flight (Web Locks liên tab nếu có) gọi POST /api/v1/auth/refresh với credentials include và header x-csrf-token; thành công thì commit session theo generation rồi retry đúng một lần, thất bại thì xoá session và onSessionExpired. Sau reload, status 'restorable' khi còn csrfToken trong localStorage nên restoreSession gọi refresh; tab khác logout thì tab này xoá session ngay.

- evidence: apps/web/src/app/providers.tsx:23

### moreEvidence

### #1

- file: apps/web/src/app/providers.tsx
- line: 25
- quote: refetchOnWindowFocus: false,

### #2

- file: apps/web/src/app/providers.tsx
- line: 28
- quote: refetchOnReconnect: shouldRefetchOnReconnect,

### #3

- file: apps/web/src/app/providers.tsx
- line: 30
- quote: refetchIntervalInBackground: false,

### #4

- file: apps/web/src/shared/api/query-retry-policy.ts
- line: 7
- quote: React Query retry policy for REST requests

### #5

- file: apps/web/src/shared/realtime/socket.ts
- line: 23
- quote: MỘT socket instance cho cả app

### #6

- file: apps/web/src/shared/realtime/use-realtime-connection.ts
- line: 8
- quote: Transport khỏe thì giảm tần suất reconciliation

### #7

- file: apps/web/src/features/friend-chat/api.ts
- line: 103
- quote: refetchInterval: FRIEND_MESSAGES_REFETCH_INTERVAL_MS,

### #8

- file: apps/web/src/features/notification/api.ts
- line: 54
- quote: refetchInterval: UNREAD_NOTIFICATION_COUNT_REFETCH_INTERVAL_MS,

### #9

- file: libs/api-client/src/lib/client.ts
- line: 121
- quote: Single-flight: N request cùng dính 401 chỉ tạo 1 lần rotation

### #10

- file: libs/api-client/src/lib/client.ts
- line: 81
- quote: const refreshLockName = `litmatch-api-refresh:${new URL(baseUrl).origin}`;

### #11

- file: libs/api-client/src/lib/client.ts
- line: 131
- quote: credentials: 'include',

### #12

- file: libs/api-client/src/lib/token-store.ts
- line: 91
- quote: csrfToken !== null ? 'restorable' : 'unauthenticated';

### #13

- file: libs/api-client/src/lib/token-store.ts
- line: 99
- quote: Tab khác logout — theo ngay, không đợi tự 401.

### admin

### serverStateCache

- library: @tanstack/react-query

- defaults: staleTime 30000 ms, retry 1, refetchOnWindowFocus false

### perFeatureStaleTime

### #1

- feature: dashboard
- staleTime: 30_000
- evidence: apps/admin/src/features/dashboard/api.ts:20

### #2

- feature: gifts
- staleTime: 5000
- evidence: apps/admin/src/features/gifts/api.ts:22

### #3

- feature: moderation (reports)
- staleTime: 5000
- evidence: apps/admin/src/features/moderation/api.ts:36

### #4

- feature: moderation (videos)
- staleTime: 5000
- evidence: apps/admin/src/features/moderation/videos-api.ts:30

### #5

- feature: rooms
- staleTime: 5000
- evidence: apps/admin/src/features/rooms/api.ts:32

### #6

- feature: users
- staleTime: 5000
- evidence: apps/admin/src/features/users/api.ts:41

### #7

- feature: capabilities runtime (shared)
- staleTime: Infinity
- evidence: apps/admin/src/shared/capabilities/api.ts:32

### browserStorage

### localStorage

- key: litmatch-admin.csrf-token
- purpose: Persist csrfToken như web (qua browserCsrfTokenStorage).
- evidence: apps/admin/src/shared/api/client.ts:11

### localStorage

- key: litmatch-admin-theme
- purpose: Lựa chọn giao diện.
- evidence: apps/admin/src/shared/lib/theme-store.ts:5

### sessionStorage

- key: litmatch-admin-report-filters
- purpose: Bộ lọc báo cáo đã lưu trong phiên tab.
- evidence: apps/admin/src/features/moderation/components/reports-panel.tsx:34

### cookies

### litmatch-admin.locale

- purpose: Ngôn ngữ giao diện admin.
- attributes: Path=/; Max-Age=31536000; SameSite=Lax; JS-set (không HttpOnly)
- evidence: apps/admin/src/shared/i18n/locale-store.ts:28

### refresh_token, csrf_token (do core-api set)

- purpose: Cùng cơ chế với web.
- attributes: httpOnly; Path=/api/v1/auth
- evidence: apps/core-api/src/modules/auth/auth.cookies.ts:60

- realtimeHandling: Không thấy kết nối realtime trong admin (xem limits); dữ liệu quản trị stale sau 5 s và được invalidate sau mutation.

- authRefreshFlow: Dùng chung createApiClient và createTokenStore của libs/api-client: 401 thì refresh single-flight rồi retry một lần; csrfToken ở localStorage litmatch-admin.csrf-token.

- evidence: apps/admin/src/app/providers.tsx:13

### moreEvidence

### #1

- file: apps/admin/src/shared/api/client.ts
- line: 13
- quote: export const tokenStore = createTokenStore(
