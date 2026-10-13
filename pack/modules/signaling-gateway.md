# Module signaling-gateway

id: module:signaling-gateway
kind: deployable
code: apps/signaling-gateway

Gateway Socket.IO namespace /signaling: verify JWT của core-api lúc handshake, join socket vào room user:{userId}, relay nguyên văn envelope từ Redis pub/sub realtime:user:* xuống socket, và giữ quota kết nối + presence bằng lease trong Redis. KHÔNG chứa business logic, KHÔNG có DB, KHÔNG ký token và KHÔNG nhận room/event từ client (chỉ có 'ping').

## Facts

- jobs: SignalingGateway
- env vars read: LOG_LEVEL, NODE_ENV, JWT_SECRET, WS_CONNECTION_LEASE_MS, WS_MAX_CONNECTIONS_PER_USER, REDIS_URL, JWT_SECRET_PREVIOUS, SENTRY_DSN, SENTRY_RELEASE, CORS_ORIGINS, SIGNALING_PORT
- tests: 9 unit spec files, 2 integration spec files
- history: 26 commits (7 fixes), last 2026-10-09
- docs: docs/services/realtime-gateway.md (last changed 2026-10-09)
- features: realtime-fanout

## Where to read next

- why it talks to other services: ../service-map/signaling-gateway.md and ../service-map/signaling-gateway-techniques.md
- flows involving it: [admin-room-management](../flows/admin-room-management.md), [auth-guest-login-and-socket](../flows/auth-guest-login-and-socket.md), [calling-end-and-ticker](../flows/calling-end-and-ticker.md), [calling-like-after-call](../flows/calling-like-after-call.md), [friend-streak-warning-job](../flows/friend-streak-warning-job.md), [gift-in-party-room](../flows/gift-in-party-room.md), [matching-invite-accept](../flows/matching-invite-accept.md), [matching-to-call](../flows/matching-to-call.md), [message-push-notification](../flows/message-push-notification.md), [mini-game-session](../flows/mini-game-session.md), [movie-match-anon-chat-finish-reactions](../flows/movie-match-anon-chat-finish-reactions.md), [movie-match-anonymous](../flows/movie-match-anonymous.md), [movie-match-friend-session](../flows/movie-match-friend-session.md), [party-room-detail-and-comments](../flows/party-room-detail-and-comments.md), [party-room-leave-and-role-change](../flows/party-room-leave-and-role-change.md), [party-room-lifecycle](../flows/party-room-lifecycle.md), [party-room-speaker-invite](../flows/party-room-speaker-invite.md), [soul-match-send-message](../flows/soul-match-send-message.md), [soul-match-session](../flows/soul-match-session.md), [story-reply-to-dm](../flows/story-reply-to-dm.md), [user-presence-read](../flows/user-presence-read.md)

## Linked issues (heuristic, public subset)

- P004 [fixed] Adapter CLAUDE.md / skills lệch bản canonical (symlink tay)
- P036 [fixed] Windows: hook/runner không chạy được pnpm (.cmd: 127 / EINVAL)
- P038 [fixed] Windows: Jest không chạy (thiếu VC++ runtime, resolver native)
- P039 [fixed] Pre-push trên Windows đỏ giả do giả định POSIX / công cụ Linux-only
- P041 [fixed] Timeout cứng quá chặt / chạy song song làm test flaky
- P043 [fixed] Nx cache bật cho test có state → có thể trả kết quả cũ
- P044 [fixed] E2E local: server cũ còn sống, reset DB chồng, thiếu baseUrl
- P086 [fixed] Relay realtime gửi trùng event theo số pod (LRN-011)
- P091 [fixed] Xoay JWT_SECRET đăng xuất mọi người

See ../issues/index.md.
