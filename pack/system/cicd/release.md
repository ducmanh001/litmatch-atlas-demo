# CI/CD: pipelines, quality gates, release, images: release

id: system:cicd:release

Written by an AI agent from the code; every file:line quote was checked automatically (exists within ±3 lines) but NO independent verification pass has run yet. Treat claims as strong leads; open the cited file before editing.

### flow

### PR phải có `CI required` xanh trước khi merge vào main (checklist PR + runbook); CI cũng chạy trên …

- order: 1
- where: GitHub PR / merge queue (cài đặt không thấy trong repo)
- evidence (evidence): .github/PULL_REQUEST_TEMPLATE.md:36, .github/workflows/ci.yml:7

### Commit vào main kích hoạt lại workflow CI (sự kiện push); chỉ run push của main mới được release tự…

- order: 2
- where: .github/workflows/ci.yml
- evidence (evidence): .github/workflows/ci.yml:4, .github/workflows/hosted-release.yml:28

### CI chạy quality → (test song song docker) → `CI required`; kết luận workflow là success chỉ khi cả …

- order: 3
- where: .github/workflows/ci.yml
- evidence (evidence): .github/workflows/ci.yml:169, .github/workflows/ci.yml:167

### Workflow `Hosted release` tự chạy khi CI của main hoàn tất với conclusion success và biến repo HOST…

- order: 4
- where: .github/workflows/hosted-release.yml (job deploy)
- evidence (evidence): .github/workflows/hosted-release.yml:5, .github/workflows/hosted-release.yml:26, .github/workflows/hosted-release.yml:30

### Chỉ đường dispatch: SHA phải đủ 40 hex và `gh api` phải thấy ít nhất một run `ci.yml` completed, pu…

- order: 5
- where: hosted-release.yml bước "Require a green CI gate for this commit"
- evidence (evidence): .github/workflows/hosted-release.yml:82, .github/workflows/hosted-release.yml:90

### Checkout đúng SHA phát hành rồi cài dependency từ lockfile frozen.

- order: 6
- where: hosted-release.yml
- evidence (evidence): .github/workflows/hosted-release.yml:72, .github/workflows/hosted-release.yml:91

### Chạy migration forward-only vào PostgreSQL hosted (`migration:run --transaction each`) TRƯỚC khi đổ…

- order: 7
- where: hosted-release.yml → nx run core-api:migration-run
- evidence (evidence): .github/workflows/hosted-release.yml:93, apps/core-api/project.json:36

### Trigger build Northflank cho core-api và signaling-gateway theo đúng SHA (song song), poll tối đa 1…

- order: 8
- where: scripts/release/northflank-deploy.mjs
- evidence (evidence): scripts/release/northflank-deploy.mjs:87, scripts/release/northflank-deploy.mjs:62, scripts/release/northflank-deploy.mjs:70

### Deploy web (OpenNext → Cloudflare Worker `litmatch-web`) và admin (Cloudflare Pages `litmatch-admin…

- order: 9
- where: hosted-release.yml → nx run-many -t cloudflare-deploy -p web,admin
- evidence (evidence): .github/workflows/hosted-release.yml:97, apps/web/project.json:42, apps/admin/project.json:21

### Smoke 4 URL công khai đồng thời với retry có giới hạn; thất bại thì job đỏ (sau khi đã deploy).

- order: 10
- where: scripts/release/public-smoke.mjs
- evidence (evidence): .github/workflows/hosted-release.yml:104, scripts/release/public-smoke.mjs:6

### flowSingleNode

### Chạy tay trên VM tại commit/tag đã xanh; từ chối nếu worktree có thay đổi tracked.

- order: 1
- where: pnpm release:deploy (scripts/release/release.mjs deploy)
- evidence (evidence): scripts/release/release.mjs:242, docs/runbooks/zero-cost-production.md:81

### Preflight: validate env (secret >= độ dài, không placeholder, telemetry bắt buộc), kiểm production …

- order: 2
- where: release.mjs preflight() + release-config.mjs
- evidence (evidence): scripts/release/release.mjs:91, scripts/release/release.mjs:92, scripts/release/release-config.mjs:60

### Build: pnpm install frozen, nx build core-api/signaling-gateway/web/admin, rồi docker build 4 image…

- order: 3
- where: release.mjs build()
- evidence (evidence): scripts/release/release.mjs:98, scripts/release/release.mjs:70

### Bật postgres, redis, kafka và chờ healthy.

- order: 4
- where: release.mjs deploy()
- evidence (evidence): scripts/release/release.mjs:245

### Backup PostgreSQL bằng pg_dump định dạng custom vào deploy/production/backups; lỗi backup dừng trướ…

- order: 5
- where: release.mjs backup()
- evidence (evidence): scripts/release/release.mjs:189, scripts/release/release.mjs:198

### Migration forward bằng `pnpm db:migrate` vào cổng host của PostgreSQL.

- order: 6
- where: release.mjs migrate()
- evidence (evidence): scripts/release/release.mjs:207

### Khởi động toàn bộ stack, rồi smoke TLS 4 URL một lần (không retry) và ghi `.release-state.json` (cu…

- order: 7
- where: release.mjs deploy() → smoke() → writeState()
- evidence (evidence): scripts/release/release.mjs:248, scripts/release/release.mjs:211, scripts/release/release.mjs:225

### flowKubernetes

### Xem trước YAML: `kubectl kustomize k8s/overlays/<env>`.

- order: 1
- where: k8s/README.md
- evidence (evidence): k8s/README.md:39

### Apply thủ công: `kubectl apply -k k8s/overlays/staging|production`; docs mô tả CI đổi tag image bằn…

- order: 2
- where: k8s/README.md
- evidence (evidence): k8s/README.md:43, k8s/README.md:68

### Theo dõi rollout bằng `kubectl -n litmatch rollout status deployment/core-api`.

- order: 3
- where: k8s/README.md
- evidence (evidence): k8s/README.md:57

### environments

### local-dev

- target: Máy developer: Docker Compose (Postgres, Redis, Kafka, LiveKit, core-api, signaling, admin, web) hoặc host-native với hạ tầng Compose; có thêm devcontainer.
- trigger: `pnpm dev:up` hoặc `pnpm bootstrap` (thủ công)
- configFiles: docker-compose.yml; docker-compose.dev.yml; docker-compose.observability.yml; Dockerfile.dev; docker/dev-entrypoint.sh; .env.example; apps/admin/.env.example; apps/web/.env.example; .devcontainer/devcontainer.json
- evidence (evidence): docker-compose.yml:1, package.json:18, package.json:17

### ci (GitHub Actions)

- target: Runner ubuntu-latest tạm thời với service container postgres:16-alpine và redis:7-alpine; secret ứng dụng là giá trị CI-only cố định.
- trigger: push main, pull_request, merge_group, workflow_dispatch
- configFiles: .github/workflows/ci.yml; scripts/ci/local.mjs
- evidence (evidence): .github/workflows/ci.yml:32, .github/workflows/ci.yml:58

### hosted-free alpha (môi trường public duy nhất có pipeline tự động)

- target: Northflank Developer Sandbox (core-api, signaling-gateway, PostgreSQL), Cloudflare Workers (web qua OpenNext) và Pages (admin), Upstash Redis, LiveKit Cloud; demo/alpha không SLA.
- trigger: Tự động sau CI push xanh trên main nếu HOSTED_RELEASE_ENABLED=true; hoặc dispatch với sha
- configFiles: .github/workflows/hosted-release.yml; deploy/hosted/Dockerfile.core-api; deploy/hosted/Dockerfile.signaling-gateway; deploy/hosted/core-api.env.example; deploy/hosted/signaling-gateway.env.example; deploy/hosted/r2-cors.json; apps/web/wrangler.jsonc; apps/web/open-next.config.ts; apps/admin/public/_redirects; scripts/release/northflank-deploy.mjs; scripts/release/public-smoke.mjs
- evidence (evidence): docs/adr/0009-hosted-free-alpha-release-profile.md:17, docs/adr/0009-hosted-free-alpha-release-profile.md:19, apps/web/wrangler.jsonc:3

### single-node production (Compose + Caddy + LiveKit)

- target: Một VM ARM64 chạy Docker Compose: postgres, redis, livekit, core-api, signaling-gateway, web, edge (Caddy + admin SPA), alloy tùy chọn; alpha/beta một failure domain.
- trigger: `pnpm release:deploy` chạy tay trên VM
- configFiles: deploy/production/compose.yml; deploy/production/Caddyfile; deploy/production/livekit.yaml; deploy/production/Dockerfile.edge; deploy/production/.env.example; scripts/release/release.mjs; scripts/release/release-config.mjs; scripts/release/production-profile.mjs
- evidence (evidence): docs/adr/0008-zero-cost-single-node-release-profile.md:16, package.json:66

### kubernetes staging / production / production-region-b (scaffold)

- target: Cluster K8s với nginx-ingress; Postgres, Redis, Kafka là dịch vụ managed bên ngoài; production-region-b là scaffold chưa provision.
- trigger: `kubectl apply -k k8s/overlays/<env>` chạy tay; không có pipeline trong repo
- configFiles: k8s/base/**; k8s/overlays/staging/kustomization.yaml; k8s/overlays/production/kustomization.yaml; k8s/overlays/production-region-b/kustomization.yaml; k8s/ingress-nginx/cloudflare-real-ip-configmap.yaml; .trivyignore.yaml
- evidence (evidence): k8s/README.md:43, k8s/README.md:30

### staging (đích của bài kiểm tra tải)

- target: URL do biến GitHub STAGING_API_URL / STAGING_SIGNALING_URL trỏ tới (phải mang tên staging); repo không định nghĩa cách staging được deploy.
- trigger: workflow_dispatch của staging-reliability.yml
- configFiles: .github/workflows/staging-reliability.yml; loadtest/matching-queue.js; loadtest/calling-flow.js; loadtest/signaling-ws.js
- evidence (evidence): .github/workflows/staging-reliability.yml:37, .github/workflows/staging-reliability.yml:39

### smokeChecks

### Container smoke trong CI: core-api và signaling-gateway /health/ready, web `/`, chờ tối đa 30 giây,…

- where: scripts/ci/local.mjs waitForHealthEndpoints() (job `docker`)
- evidence (evidence): scripts/ci/local.mjs:706, scripts/ci/local.mjs:740

### Docker HEALTHCHECK của 4 image runtime (/health/live cho core-api và signaling-gateway, `/` cho web…

- where: apps/*/Dockerfile, deploy/production/Dockerfile.edge; scripts/release/production-profile.mjs
- evidence (evidence): scripts/release/production-profile.mjs:55, scripts/release/production-profile.mjs:62

### K8s: liveness /health/live và readiness /health/ready trên cổng `http` cho core-api và signaling-ga…

- where: k8s/base/*/deployment.yaml, kiểm bằng production-profile.mjs
- evidence (evidence): scripts/release/production-profile.mjs:200, scripts/release/production-profile.mjs:206

### Public smoke của hosted-free: api và signaling `/health/ready`, web `/`, admin `/login`; đồng thời,…

- where: scripts/release/public-smoke.mjs (bước cuối hosted-release.yml)
- evidence (evidence): scripts/release/public-smoke.mjs:29, scripts/release/public-smoke.mjs:8

### Smoke single-node: `curl --fail` bốn URL TLS (api, realtime `/health/ready`, app `/`, admin `/login…

- where: scripts/release/release.mjs smoke()
- evidence (evidence): scripts/release/release.mjs:218, scripts/release/release.mjs:216

### Kiểm tra sau release do người làm: Sentry/Grafana có sample mới, PostHog nếu bật.

- where: docs/runbooks/zero-cost-production.md (thủ công)
- evidence (evidence): docs/runbooks/zero-cost-production.md:93

### rollbackAndRecovery

### Hosted-free: không có bước rollback tự động; release lỗi dừng tại bước lỗi và không tự nâng plan.

- how: Sửa rồi chạy lại. Theo mã, workflow_dispatch nhận SHA bất kỳ của main đã có CI push xanh nên có thể deploy lại SHA cũ, nhưng migration không bị đảo và runbook không mô tả đây là quy trình rollback.
- evidence (evidence): docs/runbooks/hosted-free-release.md:54, .github/workflows/hosted-release.yml:11

### Migration chỉ đi tiến; `pnpm db:revert` tồn tại như công cụ tay nhưng không nằm trong đường release…

- how: Sửa bằng migration mới. Release hosted chạy `migration:run`, single-node chạy `db:migrate`; không có bước revert.
- evidence (evidence): docs/adr/0008-zero-cost-single-node-release-profile.md:36, package.json:62

### Single-node: rollback image về tag trước.

- how: `pnpm release:rollback` đọc `.release-state.json`, dựng lại env với tag previous, `compose up -d core-api signaling-gateway web edge`, smoke rồi hoán đổi current/previous; không revert schema.
- evidence (evidence): scripts/release/release.mjs:257, scripts/release/release.mjs:261, docs/runbooks/zero-cost-production.md:108

### Single-node: khôi phục dữ liệu từ backup pg_dump.

- how: Thủ công bằng `pg_restore` sau khi xác nhận target; không tự động vì ghi đè dữ liệu. Backup nằm cùng VM nên không chống mất VM.
- evidence (evidence): docs/runbooks/zero-cost-production.md:109, docs/runbooks/zero-cost-production.md:96

### K8s: quay lại ReplicaSet trước; core-api/signaling-gateway rolling update không giảm capacity (maxU…

- how: `kubectl -n litmatch rollout undo deployment/core-api` (làm tay).
- evidence (evidence): k8s/README.md:58, k8s/README.md:61

### Hosted-free: Redis Upstash free có thể bị archive; Kafka relay tắt nên outbox giữ event chờ replay.

- how: Restore database Upstash trong console rồi cập nhật REDIS_URL; outbox giữ `published_at` null để replay sau khi bật relay.
- evidence (evidence): docs/runbooks/hosted-free-release.md:56, docs/adr/0009-hosted-free-alpha-release-profile.md:23

- contract: Hosted release chỉ deploy khi biến repo HOSTED_RELEASE_ENABLED = true và commit đã có một run CI push thành công trên main. Đường tự động lấy điều đó từ workflow_run; đường dispatch tự kiểm bằng gh api trên đúng SHA 40 hex.

### contractChecklist

- workflow_run: event = push, head_branch = main, conclusion = success; SHA phát hành = workflow_run.head_sha
- workflow_dispatch: input sha khớp ^[0-9a-f]{40}$ và gh api thấy >= 1 run ci.yml completed với head_sha khớp, head_branch main, event push, conclusion success
- Cài dependency bằng --frozen-lockfile; mọi lệnh Nx có --outputStyle=static; NX_TUI=false (workflow-policy ép bằng agent:check)
- Secrets: NORTHFLANK_API_TOKEN, HOSTED_DATABASE_URL, CLOUDFLARE_API_TOKEN, CLOUDFLARE_ACCOUNT_ID; biến NORTHFLANK_*, PUBLIC_* và các biến build frontend

### versioning

- scheme: Không thấy công cụ versioning hay changelog tự động; bản phát hành được định danh bằng commit SHA (hosted: sha gửi cho Northflank và SENTRY_RELEASE; single-node: 12 ký tự đầu của SHA hoặc RELEASE_TAG). package.json giữ version 0.0.1.

### evidence

### #1

- file: package.json
- line: 3
- quote: "version": "0.0.1",

### #2

- file: .github/workflows/hosted-release.yml
- line: 57
- quote: NEXT_PUBLIC_SENTRY_RELEASE: ${{ inputs.sha || github.event.workflow_run.head_sha }}

### #3

- file: scripts/release/northflank-deploy.mjs
- line: 82
- quote: body: JSON.stringify({ sha: config.sha }),

### #4

- file: scripts/release/release.mjs
- line: 71
- quote: let releaseTag = process.env.RELEASE_TAG ?? gitTag;

### contractEvidence

### #1

- file: .github/workflows/hosted-release.yml
- line: 26
- quote: vars.HOSTED_RELEASE_ENABLED == 'true' &&

### #2

- file: .github/workflows/hosted-release.yml
- line: 29
- quote: github.event.workflow_run.head_branch == 'main' &&

### #3

- file: .github/workflows/hosted-release.yml
- line: 90
- quote: test "${successful_runs}" -ge 1

### #4

- file: scripts/ci/workflow-policy.mjs
- line: 175
- quote: errors.push('Hosted release phải ép NX_TUI=false.');

### #5

- file: scripts/release/northflank-deploy.mjs
- line: 18
- quote: throw new Error('RELEASE_SHA must be a full Git commit SHA');

### #6

- file: docs/runbooks/hosted-free-release.md
- line: 31
- quote: Secrets: `NORTHFLANK_API_TOKEN`, `HOSTED_DATABASE_URL`, `CLOUDFLARE_API_TOKEN`,

### githubConfig

### secrets

- NORTHFLANK_API_TOKEN
- HOSTED_DATABASE_URL
- CLOUDFLARE_API_TOKEN
- CLOUDFLARE_ACCOUNT_ID

### variables

- HOSTED_RELEASE_ENABLED (bật cuối cùng)
- NORTHFLANK_PROJECT_ID
- NORTHFLANK_CORE_SERVICE_ID
- NORTHFLANK_SIGNALING_SERVICE_ID
- PUBLIC_API_URL
- PUBLIC_SIGNALING_URL
- PUBLIC_LIVEKIT_URL
- PUBLIC_WEB_URL
- PUBLIC_ADMIN_URL
- GOOGLE_OAUTH_CLIENT_ID (được để trống)
- FACEBOOK_APP_ID
- FACEBOOK_API_VERSION (mặc định v24.0)
- NEXT_PUBLIC_SENTRY_DSN
- VITE_SENTRY_DSN

### variablesUsedByWorkflowButMissingFromRunbook

- NEXT_PUBLIC_GOOGLE_SITE_VERIFICATION
- NEXT_PUBLIC_PHONE_OTP_ENABLED
- VITE_PHONE_OTP_ENABLED
- NEXT_PUBLIC_POSTHOG_PROJECT_TOKEN
- NEXT_PUBLIC_POSTHOG_HOST

### stagingEnvironmentVariables

- STAGING_API_URL
- STAGING_SIGNALING_URL

- providerSideConfig: Biến runtime của core-api/signaling-gateway nằm trên Northflank (import từ deploy/hosted/*.env.example); JWT_SECRET và REDIS_URL phải giống nhau giữa hai service; CORS_ORIGINS phải chứa chính xác PUBLIC_WEB_URL,PUBLIC_ADMIN_URL.

### evidence

### #1

- file: docs/runbooks/hosted-free-release.md
- line: 31
- quote: Secrets: `NORTHFLANK_API_TOKEN`, `HOSTED_DATABASE_URL`, `CLOUDFLARE_API_TOKEN`,

### #2

- file: docs/runbooks/hosted-free-release.md
- line: 34
- quote: Variables: `NORTHFLANK_PROJECT_ID`, `NORTHFLANK_CORE_SERVICE_ID`,

### #3

- file: docs/runbooks/hosted-free-release.md
- line: 38
- quote: `NEXT_PUBLIC_SENTRY_DSN`, `VITE_SENTRY_DSN` (đều có thể để trống), và cuối cùng

### #4

- file: .github/workflows/hosted-release.yml
- line: 54
- quote: NEXT_PUBLIC_PHONE_OTP_ENABLED: ${{ vars.NEXT_PUBLIC_PHONE_OTP_ENABLED }}

### #5

- file: .github/workflows/hosted-release.yml
- line: 65
- quote: VITE_PHONE_OTP_ENABLED: ${{ vars.VITE_PHONE_OTP_ENABLED }}

### #6

- file: .github/workflows/hosted-release.yml
- line: 59
- quote: NEXT_PUBLIC_POSTHOG_PROJECT_TOKEN: ${{ vars.NEXT_PUBLIC_POSTHOG_PROJECT_TOKEN }}

### #7

- file: .github/workflows/hosted-release.yml
- line: 46
- quote: NEXT_PUBLIC_GOOGLE_SITE_VERIFICATION: ${{ vars.NEXT_PUBLIC_GOOGLE_SITE_VERIFICATION }}

### #8

- file: docs/runbooks/hosted-free-release.md
- line: 41
- quote: Không bật gate cuối cho tới khi mọi URL/env ở Northflank đã khớp. Core `CORS_ORIGINS` phải chứa
