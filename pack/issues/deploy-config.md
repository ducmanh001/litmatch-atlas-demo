# Issues in area deploy-config

id: issues:deploy-config

### P014: Cấu hình theo máy (tunnel/IP/credential) nằm trong repo (LRN-004)

status: fixed; tier: A; date: 2026-07-18; confidence: high

- problem: ID tunnel, IP LAN hoặc credential theo máy nằm trong cấu hình runtime được track, làm local/release không portable và lộ giả định môi trường.
- root cause: Giá trị cấu hình không do đường env chuẩn sở hữu.
- resolution: Root .env và interpolation có validate sở hữu giá trị local; scripts/agent/config-single-source.test.mjs quét động các bề mặt cấu hình được track.
- evidence: doc docs/reference/lessons-registry.md, file scripts/agent/config-single-source.test.mjs, doc docs/plans/2026-07-18-config-refactor-review.md, doc docs/reference/project-handoff.md

### P021: Redis TLS rediss:// có nguy cơ bị Joi từ chối khi boot

status: fixed; tier: A; date: 2026-07-20; confidence: medium

- problem: Profile hosted-free dùng Upstash rediss:// có nguy cơ bị validation Joi từ chối trước bootstrap.
- root cause: Env validation chỉ chấp nhận scheme redis.
- resolution: Core và signaling chấp nhận cả redis và rediss, từ chối scheme khác; unit test khoá hành vi.
- evidence: doc docs/plans/2026-07-22-hosted-free-release-review.md, doc docs/plans/2026-07-20-hosted-free-release-plan.md

### P026: Production thiếu biến môi trường đăng nhập

status: fixed; tier: A; date: 2026-07-24; confidence: medium

- problem: Production thiếu biến môi trường của luồng đăng nhập nên đăng nhập hỏng.
- root cause: Workflow ci.yml / hosted-release.yml không truyền đủ biến môi trường vào bước build/deploy (chi tiết tên biến không có trong commit).
- resolution: Sửa .github/workflows/ci.yml (120 dòng) và hosted-release.yml để truyền đủ biến (#39).
- evidence: commit fe2aa01

### P047: Ảnh avatar trỏ cdn.litmatch.example không phân giải DNS

status: fixed; tier: A; date: 2026-08-14; confidence: high

- problem: Ảnh avatar/catalog/layer không tải: API trả URL cdn.litmatch.example, domain .example không phân giải DNS.
- root cause: Seed avatar trỏ CDN giả cdn.litmatch.example.
- resolution: Thêm 8 SVG local ở apps/web/public/avatar và migration mới 1757300000000-avatar-public-assets; sửa migration cũ bị hook chặn (migration bất biến) nên hoàn tác.
- evidence: codex-thread 019ffec1, file apps/core-api/src/database/migrations/1757300000000-avatar-public-assets.ts, commit a3f0f61

### P076: Rate limit trong bộ nhớ từng pod + trần kết nối Postgres

status: fixed; tier: B; date: 2026-09-03; confidence: high

- problem: Chạy nhiều replica core-api: hạn mức rate limit nhân N lần, mất khi deploy; tổng kết nối Postgres có thể vượt max_connections.
- root cause: Throttler mặc định đếm trong bộ nhớ từng process; pool kết nối mỗi pod không có trần cấu hình, HPA maxReplicas nhân pool vượt ngân sách.
- resolution: Thêm RedisThrottlerStorage (THROTTLE_STORAGE=redis, Lua script, fallback bộ nhớ khi Redis lỗi) và DATABASE_POOL_MAX; ghi ngân sách 10x10=100 trong k8s/README.md; k8s base đặt redis. Bổ sung sau rà soát độc lập (09/10): K8s base đặt HTTP_TRUST_PROXY_HOPS=1 (trước đó req.ip là IP ingress nên mọi người dùng chung một bucket mỗi route) và core-api cảnh báo lúc boot khi redis + production + hops 0; sàn DATABASE_POOL_MAX 5 vì job singleton giữ 1 kết nối cho advisory lock và cần thêm 1 (đã chứng minh trên Postgres thật); DATABASE_POOL_ACQUIRE_TIMEOUT_MS (mặc định 10 giây) để hết kết nối thì lỗi thay vì treo; sửa số học ngân sách kết nối trong k8s/README.md.
- evidence: commit c1284fc, doc docs/plans/2026-10-08-capacity-and-scaling-plan.md, codex-thread 01a06739, claude-session 1f780174
