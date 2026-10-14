# Cache and state: Redis, locks, idempotency, client state: cacheInvalidationRules

id: system:state:cacheInvalidationRules

Written by an AI agent from the code; every file:line quote was checked automatically (exists within ±3 lines) but NO independent verification pass has run yet. Treat claims as strong leads; open the cited file before editing.

### Mutation hook gọi invalidateQueries/setQueryData/removeQueries theo key factory của feature; ví dụ …

- what: Server state web (TanStack Query)
- evidence: apps/web/src/features/avatar/api.ts:63
- moreEvidence (evidence): apps/web/src/features/profile/api.ts:191, apps/web/src/features/wallet/api.ts:106

### Không tự làm mới theo thời gian; chỉ đổi khi mutation invalidate hoặc khi session bị xoá.

- what: Query staleTime Infinity (số dư ví, current user, capabilities)
- evidence: apps/web/src/features/wallet/api.ts:37
- moreEvidence (evidence): apps/web/src/shared/auth/use-current-user.ts:25

### Khi TokenStore chuyển sang unauthenticated thì queryClient.clear() và reset analytics user; tab khá…

- what: Toàn bộ cache khi đăng xuất hoặc hết phiên (web)
- evidence: apps/web/src/app/providers.tsx:43
- moreEvidence (evidence): libs/api-client/src/lib/token-store.ts:99

### Socket chỉ là kênh delta; sau reconnect onReconnected refetch state nền qua REST, REST là reconcili…

- what: State nền sau khi socket mất rồi nối lại
- evidence: apps/web/src/shared/realtime/socket.ts:23

### Không có denylist; hết hiệu lực theo JWT_ACCESS_TTL_SECONDS (mặc định 900 s); thu hồi phiên chỉ chặ…

- what: Access token đã phát
- evidence: apps/core-api/src/common/guards/jwt-auth.guard.ts:22

### Không có thao tác xoá chủ động ngoài release lease; core-api dọn member quá hạn bằng ZREMRANGEBYSCO…

- what: Presence online
- evidence: apps/core-api/src/modules/user/redis/redis-presence-reader.adapter.ts:15

### Hết hạn tự nhiên theo TTL cửa sổ; throttle hits được rút còn blockDuration khi vượt limit; refund s…

- what: Bộ đếm rate limit và throttle trong Redis
- evidence: apps/core-api/src/common/throttle/redis-throttler-storage.ts:44

### Bị lấy ra bằng ZPOPMIN khi ghép, ZREM khi huỷ/hết hạn; ticket verify fail được ZADD NX lại với scor…

- what: Phần tử trong queue ghép đôi Redis
- evidence: apps/core-api/src/modules/matching/jobs/matcher-worker.service.ts:360

### Không có cache TTL; snapshot được ledger cập nhật cùng transaction và rebuild bằng LedgerService.re…

- what: Wallet snapshot
- evidence: apps/core-api/src/modules/economy/services/ledger.service.ts:277
