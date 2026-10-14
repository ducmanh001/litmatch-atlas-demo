# CI/CD: pipelines, quality gates, release, images: images

id: system:cicd:images

Written by an AI agent from the code; every file:line quote was checked automatically (exists within ±3 lines) but NO independent verification pass has run yet. Treat claims as strong leads; open the cited file before editing.

### #1

- image: Image workspace dev (không phát hành)
- dockerfile: Dockerfile.dev
- base: node:22-alpine@sha256:16e22a550f3863206a3f701448c45f7912c6896a62de43add43bb9c86130c3e2
- stages: một stage; mount source và node_modules lúc chạy
- runsAsUser: root lúc khởi động; docker/dev-entrypoint.sh đọc UID/GID của /workspace rồi `exec su-exec` để chạy process bằng đúng UID/GID đó
- healthcheck: Không có trong Dockerfile (docker-compose.dev.yml khai healthcheck cho từng service)
- notableHardening: Base ghim digest; Ngoại lệ Trivy DS-0002 cho Dockerfile.dev có ghi lý do (root có chủ đích, tự hạ quyền bằng su-exec)
- builtBy: docker compose (docker-compose.dev.yml, `pnpm dev:up` / `pnpm dev:rebuild`)
- evidence (evidence): Dockerfile.dev:3, Dockerfile.dev:9, Dockerfile.dev:19, docker/dev-entrypoint.sh:16, docker-compose.dev.yml:6, .trivyignore.yaml:10

### #2

- image: litmatch/core-api:<tag>
- dockerfile: apps/core-api/Dockerfile
- base: node:22-alpine@sha256:16e22a550f3863206a3f701448c45f7912c6896a62de43add43bb9c86130c3e2
- stages: một stage runtime; cần `pnpm nx build core-api` tạo dist/apps/core-api trước khi docker build
- runsAsUser: node (USER node)
- ports: 3000
- healthcheck: wget -qO- http://127.0.0.1:3000/health/live; interval 30s, timeout 3s, start-period 15s, retries 3
- notableHardening: Base ghim digest; `pnpm install --prod --frozen-lockfile --ignore-scripts`; Sau khi cài: corepack disable, xóa npm và cache pnpm; Chạy non-root; không có mã nguồn hay devDependency trong image
- builtBy: scripts/ci/local.mjs (job `docker` của CI, tag local-<sha>) và scripts/release/release.mjs `build` (tag <releaseTag>); không bước nào push lên registry
- evidence (evidence): apps/core-api/Dockerfile:3, apps/core-api/Dockerfile:7, apps/core-api/Dockerfile:9, apps/core-api/Dockerfile:12, apps/core-api/Dockerfile:13, apps/core-api/Dockerfile:15, scripts/ci/local.mjs:816, scripts/release/release.mjs:142

### #3

- image: litmatch/signaling-gateway:<tag>
- dockerfile: apps/signaling-gateway/Dockerfile
- base: node:22-alpine@sha256:16e22a550f3863206a3f701448c45f7912c6896a62de43add43bb9c86130c3e2
- stages: một stage runtime; cần `pnpm nx build signaling-gateway` trước
- runsAsUser: node (USER node)
- ports: 3001
- healthcheck: wget -qO- http://127.0.0.1:3001/health/live; interval 30s, timeout 3s, start-period 15s, retries 3
- notableHardening: Base ghim digest; `pnpm install --prod --frozen-lockfile --ignore-scripts`; Sau khi cài: corepack disable, xóa npm và cache pnpm; Chạy non-root
- builtBy: scripts/ci/local.mjs (job `docker`) và scripts/release/release.mjs `build`; không push registry
- evidence (evidence): apps/signaling-gateway/Dockerfile:2, apps/signaling-gateway/Dockerfile:6, apps/signaling-gateway/Dockerfile:11, apps/signaling-gateway/Dockerfile:12, apps/signaling-gateway/Dockerfile:14, scripts/ci/local.mjs:820

### #4

- image: litmatch/web:<tag>
- dockerfile: apps/web/Dockerfile
- base: node:22-alpine@sha256:16e22a550f3863206a3f701448c45f7912c6896a62de43add43bb9c86130c3e2
- stages: một stage runtime; cần `pnpm nx build web` tạo apps/web/.next/standalone trước
- runsAsUser: node (USER node; file COPY --chown=node:node)
- ports: 4300
- healthcheck: wget -qO- http://127.0.0.1:4300/; interval 30s, timeout 3s, start-period 15s, retries 3
- notableHardening: Base ghim digest; Xóa npm, corepack, yarn, pnpm khỏi image runtime để giảm CVE transitive; Chạy non-root; chỉ chứa output standalone của Next.js
- builtBy: scripts/ci/local.mjs (job `docker`) và scripts/release/release.mjs `build`; profile hosted-free không dùng image này (web chạy Cloudflare Workers qua OpenNext)
- evidence (evidence): apps/web/Dockerfile:2, apps/web/Dockerfile:9, apps/web/Dockerfile:5, apps/web/Dockerfile:4, apps/web/Dockerfile:15, apps/web/Dockerfile:12, scripts/ci/local.mjs:825

### #5

- image: Northflank core-api (hosted-free)
- dockerfile: deploy/hosted/Dockerfile.core-api
- base: node:22-alpine@sha256:16e22a550f3863206a3f701448c45f7912c6896a62de43add43bb9c86130c3e2
- stages: build (copy toàn repo, pnpm install --frozen-lockfile, nx build core-api); runtime (chỉ dist + prod dependency)
- runsAsUser: node (USER node)
- ports: 3000
- healthcheck: wget -qO- http://127.0.0.1:${PORT}/health/live; interval 30s, timeout 3s, start-period 30s, retries 3
- notableHardening: Cả hai stage ghim digest; Build trong container nên không phụ thuộc dist/ cục bộ; Prod install `--ignore-scripts`, corepack disable, xóa npm/cache; Dockerfile.core-api.dockerignore loại .git, .github, node_modules, dist, .env, .env.*
- builtBy: Northflank combined service build từ Git theo SHA do scripts/release/northflank-deploy.mjs trigger (runbook hosted-free-release); không build trong GitHub Actions
- evidence (evidence): deploy/hosted/Dockerfile.core-api:1, deploy/hosted/Dockerfile.core-api:6, deploy/hosted/Dockerfile.core-api:10, deploy/hosted/Dockerfile.core-api:12, deploy/hosted/Dockerfile.core-api:17, deploy/hosted/Dockerfile.core-api.dockerignore:13, docs/runbooks/hosted-free-release.md:21

### #6

- image: Northflank signaling-gateway (hosted-free)
- dockerfile: deploy/hosted/Dockerfile.signaling-gateway
- base: node:22-alpine@sha256:16e22a550f3863206a3f701448c45f7912c6896a62de43add43bb9c86130c3e2
- stages: build (copy toàn repo, pnpm install --frozen-lockfile, nx build signaling-gateway); runtime (chỉ dist + prod dependency)
- runsAsUser: node (USER node)
- ports: 3001
- healthcheck: wget -qO- http://127.0.0.1:${SIGNALING_PORT}/health/live; interval 30s, timeout 3s, start-period 20s, retries 3
- notableHardening: Cả hai stage ghim digest; Prod install `--ignore-scripts`, corepack disable, xóa npm/cache; Chạy non-root
- builtBy: Northflank combined service build từ Git theo SHA (như core-api hosted)
- evidence (evidence): deploy/hosted/Dockerfile.signaling-gateway:1, deploy/hosted/Dockerfile.signaling-gateway:6, deploy/hosted/Dockerfile.signaling-gateway:10, deploy/hosted/Dockerfile.signaling-gateway:17

### #7

- image: litmatch/edge:<tag> (Caddy + admin SPA)
- dockerfile: deploy/production/Dockerfile.edge
- base: golang:1.26.5-alpine@sha256:0178a641fbb4858c5f1b48e34bdaabe0350a330a1b1149aabd498d0699ff5fb2 (stage build) rồi alpine:3.23@sha256:fd791d74b68913cbb027c6546007b3f0d3bc45125f797758156952bc2d6daf40 (runtime)
- stages: caddy-build (clone Caddy v2.11.4, vá grpc, go build); runtime alpine (Caddy + dist/apps/admin + Caddyfile)
- runsAsUser: nobody:nobody; setcap cap_net_bind_service cho phép bind cổng 80/443
- ports: 80; 443; 443/udp; 2019
- healthcheck: wget -qO- http://127.0.0.1/health; interval 30s, timeout 3s, start-period 15s, retries 3 (Caddyfile trả 200 cho :80/health)
- notableHardening: Cả hai base ghim digest; Tự build Caddy bằng Go đã vá và nâng google.golang.org/grpc@v1.82.1; Runtime không có Go toolchain; chạy non-root với capability tối thiểu; Caddyfile đặt `admin off`
- builtBy: scripts/ci/local.mjs (job `docker`, kèm `caddy validate`) và scripts/release/release.mjs `build`; chỉ dùng cho profile single-node Compose
- notes: Stage build clone Caddy theo tag, `go get` và `go mod tidy` lúc build nên cần mạng và không hermetic hoàn toàn.
- evidence (evidence): deploy/production/Dockerfile.edge:2, deploy/production/Dockerfile.edge:4, deploy/production/Dockerfile.edge:6, deploy/production/Dockerfile.edge:10, deploy/production/Dockerfile.edge:14, deploy/production/Dockerfile.edge:19, deploy/production/Dockerfile.edge:22, deploy/production/Dockerfile.edge:24, deploy/production/Caddyfile:3, scripts/ci/local.mjs:827
