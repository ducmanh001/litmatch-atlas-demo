# CI/CD: pipelines, quality gates, release, images: observations

id: system:cicd:observations

Written by an AI agent from the code; every file:line quote was checked automatically (exists within ±3 lines) but NO independent verification pass has run yet. Treat claims as strong leads; open the cited file before editing.

### quality-gates-run-multiple-times

- finding: Trong một lần CI, job `quality` chạy agent:check, agent:test, workflow lint, format:check và lint; job `test` (profile `ci`) chạy lại cả năm bước, rồi `agent:verify frontend` lại chạy agent:check, agent:test, format:check và lint của admin/web/api-client.
- impact: Tốn thời gian và phút runner; là hệ quả của việc mỗi job gọi nguyên một profile local.
- confidence: high
- evidence (evidence): scripts/ci/local.mjs:1082, scripts/ci/local.mjs:1084, scripts/agent/verify.mjs:191

### release-sh-kafka-missing-from-compose

- finding: release.mjs yêu cầu `compose up --wait ... kafka` nhưng compose production không có service kafka (bị xóa trong commit a1221ec ngày 2026-07-27, theo git log).
- impact: Theo mã, `pnpm release:deploy` có khả năng thất bại ở bước dựng hạ tầng trước backup/migration; production-profile.mjs không bắt được vì REQUIRED_SERVICES không liệt kê kafka.
- confidence: medium
- evidence (evidence): scripts/release/release.mjs:245, scripts/release/production-profile.mjs:7

### security-scanning-not-running

- finding: Gitleaks, Trivy, CodeQL, SBOM, dependency review và `pnpm audit` đều không chạy trong CI hay hook; security-tools.mjs vẫn ghim gitleaks/trivy nhưng chỉ actionlint/shellcheck được dùng.
- impact: Không có lớp tự động nào bắt secret lộ, CVE trong dependency/image hay dependency mới nguy hiểm; Dependabot cũng không mở PR (limit 0).
- confidence: high
- evidence (evidence): scripts/ci/local.mjs:1093, docs/14-rule-enforcement-matrix.md:26, .github/dependabot.yml:10

### frontend-coverage-thresholds-inert

- finding: Ngưỡng coverage Vitest của admin, web, api-client không được enforce vì không đường chạy test nào truyền `--coverage` cho chúng.
- impact: Coverage frontend có thể giảm mà CI vẫn xanh; chỉ core-api có ratchet thật.
- confidence: medium
- evidence (evidence): scripts/ci/local.mjs:566, apps/web/vitest.config.mts:33

### no-standalone-typecheck

- finding: Không có target typecheck trong CI; admin build bằng Vite (không kiểm kiểu), type error chỉ bị bắt nếu đi qua build của web/core-api/signaling hoặc ts-jest.
- impact: Lỗi kiểu trong admin hoặc lib chỉ dùng ở admin có thể lọt.
- confidence: medium
- evidence (evidence): nx.json:84, apps/core-api/webpack.config.js:15

### k8s-has-no-delivery-pipeline

- finding: Image ghcr.io/litmatch/* của K8s không được build/push bởi workflow nào và manifest dùng tag `latest`; apply bằng tay.
- impact: Profile K8s (đích scale/HA theo docs) thiếu đường phát hành lặp lại được và không gắn với `CI required` của commit.
- confidence: high
- evidence (evidence): k8s/README.md:76, k8s/base/core-api/deployment.yaml:42

### hosted-deploy-job-has-no-environment

- finding: Job `deploy` của hosted-release không khai `environment:` nên secrets là secret cấp repo/org và không có approval/wait timer gắn ở file workflow.
- impact: Cổng duy nhất trước production hosted là CI xanh + biến HOSTED_RELEASE_ENABLED; thêm bảo vệ phải đổi workflow và cài đặt GitHub.
- confidence: high
- evidence (evidence): .github/workflows/hosted-release.yml:31, .github/workflows/hosted-release.yml:37

### dispatch-gate-needs-actions-read

- finding: Bước kiểm CI của đường dispatch gọi `gh api .../actions/workflows/ci.yml/runs` trong khi workflow chỉ cấp `checks: read` và `contents: read`.
- impact: Theo tài liệu GitHub, liệt kê workflow runs cần quyền Actions (read) với repo private; cần thử một lần dispatch thật để biết có lỗi quyền hay không.
- confidence: low
- evidence (evidence): .github/workflows/hosted-release.yml:20, .github/workflows/hosted-release.yml:88

### ci-cancel-in-progress-on-main

- finding: Concurrency của CI gom theo `github.ref` và `cancel-in-progress: true`: nếu push thứ hai vào main tới khi CI của push đầu còn đang chạy thì run đầu bị hủy.
- impact: Commit bị hủy không có CI success nên không được release tự động; chỉ commit mới nhất (chứa cả thay đổi trước) được release.
- confidence: high
- evidence (evidence): .github/workflows/ci.yml:11, .github/workflows/ci.yml:12, .github/workflows/hosted-release.yml:30

### agent-check-diff-empty-on-merge-group

- finding: Khi `AGENT_BASE_SHA` rỗng (merge_group, workflow_dispatch) agent:check đổi range sang `HEAD`, tức chỉ xét thay đổi chưa commit trên checkout sạch.
- impact: Các rule trên diff (migration bất biến, apps/* ngoài baseline, ledger append-only, E2E không passWithNoTests) chỉ thực sự được kiểm trên PR và push có base hợp lệ.
- confidence: medium
- evidence (evidence): .github/workflows/ci.yml:48, scripts/agent/repository-check.mjs:177

### nx-task-cache-not-persisted-in-ci

- finding: Workflow không dùng actions/cache cho `.nx/local-ci` và nx.json không cấu hình remote cache; chỉ kho pnpm được cache qua setup-node.
- impact: Build/lint chạy lạnh mỗi lần CI; test và E2E vốn luôn `--skip-nx-cache`.
- confidence: medium
- evidence (evidence): scripts/ci/local.mjs:154, .github/workflows/ci.yml:45

### single-node-smoke-single-attempt

- finding: Smoke của release.mjs gọi curl đúng một lần cho mỗi URL, trong khi public-smoke của hosted retry 30 lần.
- impact: Lần deploy đầu (Caddy đang xin chứng chỉ TLS) có thể báo smoke lỗi dù stack đang lên.
- confidence: medium
- evidence (evidence): scripts/release/release.mjs:218, scripts/release/public-smoke.mjs:6

### ci-runs-everything-not-affected

- finding: GitHub CI luôn chạy toàn bộ test/build/E2E bằng run-many; chỉ profile prepush (thủ công) dùng nx affected.
- impact: Thời gian CI tăng theo kích thước repo; bù lại không có rủi ro bỏ sót do đồ thị affected sai.
- confidence: high
- evidence (evidence): scripts/ci/local.mjs:559, scripts/ci/local.mjs:1069

### migrations-only-tested-on-empty-db

- finding: Migration trong CI chạy trên database mới tạo của service container (docker job và E2E); không có bước chạy migration trên dữ liệu có sẵn hay kiểm rollback.
- impact: Lỗi chỉ lộ với dữ liệu thật (khóa bảng lớn, ràng buộc mới vi phạm dữ liệu cũ) sẽ không bị CI bắt.
- confidence: medium
- evidence (evidence): .github/workflows/ci.yml:58, scripts/ci/local.mjs:781

### migration-runs-before-new-code

- finding: Hosted release migrate trước rồi mới trigger build/deploy code mới, nên trong lúc thay service code cũ chạy trên schema mới.
- impact: Migration phải tương thích ngược với bản code đang chạy (expand/contract); không có kiểm tra tự động cho điều này.
- confidence: medium
- evidence (evidence): .github/workflows/hosted-release.yml:93, .github/workflows/hosted-release.yml:95, docs/adr/0009-hosted-free-alpha-release-profile.md:24

### tracked-wrangler-state

- finding: Thư mục apps/web/.wrangler (cache và state Miniflare) đang được track trong git và `.wrangler/` không nằm trong .gitignore gốc lẫn apps/web/.gitignore; `.open-next/` thì đã được apps/web/.gitignore bỏ qua.
- impact: File trạng thái cục bộ của công cụ deploy bị commit; cần dọn và thêm vào .gitignore.
- confidence: high
- source: git ls-files apps/web/.wrangler và git check-ignore
- evidence (evidence): apps/web/.gitignore:1
