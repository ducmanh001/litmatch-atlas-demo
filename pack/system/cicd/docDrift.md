# CI/CD: pipelines, quality gates, release, images: docDrift

id: system:cicd:docDrift

Written by an AI agent from the code; every file:line quote was checked automatically (exists within ±3 lines) but NO independent verification pass has run yet. Treat claims as strong leads; open the cited file before editing.

### #1

- doc: k8s/README.md (và comment trong k8s/overlays/production/kustomization.yaml)
- claim: CI chạy `kustomize edit set image ... :$GIT_SHA` rồi `kubectl apply -k k8s/overlays/production`; tag image do CI ghi đè bằng git SHA.
- actual: Không workflow hay script nào gọi kustomize/kubectl hay build/push image ghcr.io/litmatch/*; chỉ có hosted-release.yml (Northflank + Cloudflare). Image K8s đang là `latest` placeholder và deploy K8s hoàn toàn thủ công.
- evidence (evidence): k8s/README.md:68, k8s/overlays/production/kustomization.yaml:13, .github/workflows/hosted-release.yml:95

### #2

- doc: docs/plans/2026-07-22-hosted-free-release-review.md (tài liệu có ngày)
- claim: `pnpm ci:preflight` PASS gồm Gitleaks, audit, Trivy filesystem và runtime Trivy.
- actual: Hiện không profile nào chạy Gitleaks/Trivy/audit: `ci:local:security` bị vô hiệu và ma trận enforcement ghi các workflow này disabled (workflow Security đã bị xóa khỏi repo sau ngày đó, theo git log).
- evidence (evidence): docs/plans/2026-07-22-hosted-free-release-review.md:60, scripts/ci/local.mjs:1093, docs/14-rule-enforcement-matrix.md:26

### #3

- doc: docs/runbooks/zero-cost-production.md § 4
- claim: Script release chạy tuần tự: ... start/wait database/cache/broker → backup PostgreSQL → migration forward ...
- actual: release.mjs chạy `up --wait postgres redis kafka` nhưng compose production không có service kafka (REQUIRED_SERVICES cũng không có, outbox relay tắt). Compose từ chối service không tồn tại nên bước này nhiều khả năng làm `pnpm release:deploy` thất bại; chưa chạy thử.
- evidence (evidence): docs/runbooks/zero-cost-production.md:89, scripts/release/release.mjs:245, scripts/release/production-profile.mjs:7, deploy/production/compose.yml:88

### #4

- doc: docs/runbooks/reliability-slo-and-evidence.md § Staging load
- claim: `loadtest.json` phải có checks ["signaling-quota","multi-pod-reconnect","staging-load"]; workflow staging-reliability sinh bằng chứng tải.
- actual: Workflow chỉ ghi `checks:["staging-load"]`; hai check còn lại phải đến từ nguồn khác (ngoài repo), nên artifact của workflow một mình không qua `reliability:production-gate`.
- evidence (evidence): docs/runbooks/reliability-slo-and-evidence.md:55, .github/workflows/staging-reliability.yml:130, scripts/reliability/verify-evidence.mjs:15

### #5

- doc: docs/15-commit-guidelines.md § 15.1
- claim: Cú pháp `<type>(<scope>): ...` là bắt buộc cho mọi commit.
- actual: Không có hook commit-msg hay commitlint; chỉ pre-commit (Prettier + agent:check) và pre-push (ci:preflight). Theo git log, lịch sử squash-merge trên main có nhiều tiêu đề dạng tên nhánh kèm số PR.
- evidence (evidence): docs/15-commit-guidelines.md:9, .husky/pre-commit:27

### #6

- doc: .github/prompts/monitor-ci.prompt.md và skill monitor-ci
- claim: Theo dõi pipeline Nx Cloud và xử lý self-healing fix; yêu cầu workspace đã kết nối Nx Cloud (nx.json có nxCloudId/nxCloudAccessToken).
- actual: nx.json không có nxCloudId/nxCloudAccessToken và ci.yml không có bước Nx Cloud, nên skill này không có dữ liệu để theo dõi (theo tìm kiếm trong repo).
- evidence (evidence): .github/prompts/monitor-ci.prompt.md:43, nx.json:122

### #7

- doc: docs/runbooks/hosted-free-release.md § 2 (GitHub Secrets và Variables)
- claim: Liệt kê danh sách Variables cần tạo cho hosted release.
- actual: hosted-release.yml còn đọc thêm các biến NEXT_PUBLIC_GOOGLE_SITE_VERIFICATION, NEXT_PUBLIC_PHONE_OTP_ENABLED, VITE_PHONE_OTP_ENABLED, NEXT_PUBLIC_POSTHOG_PROJECT_TOKEN, NEXT_PUBLIC_POSTHOG_HOST mà runbook không nhắc; nếu không đặt, giá trị build-time rỗng.
- evidence (evidence): docs/runbooks/hosted-free-release.md:34, .github/workflows/hosted-release.yml:54, .github/workflows/hosted-release.yml:60
