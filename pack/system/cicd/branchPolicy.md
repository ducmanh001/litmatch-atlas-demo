# CI/CD: pipelines, quality gates, release, images: branchPolicy

id: system:cicd:branchPolicy

Written by an AI agent from the code; every file:line quote was checked automatically (exists within ±3 lines) but NO independent verification pass has run yet. Treat claims as strong leads; open the cited file before editing.

- requiredChecks: unknown from repo

- requiredChecksDetail: Branch protection, ruleset, merge queue và số reviewer bắt buộc là cài đặt GitHub, không nằm trong repo. Repo chỉ khóa tên check tổng hợp `CI required` (job `required`) bằng workflow-policy; tài liệu yêu cầu merge khi check này xanh.

### stableCheckNames

- CI required
- Format and lint
- Test, build and E2E
- Container build and runtime smoke

### evidence

### #1

- file: .github/workflows/ci.yml
- line: 167
- quote: name: CI required

### #2

- file: scripts/ci/workflow-policy.mjs
- line: 89
- quote: if (ciConfig.jobs?.required?.name !== 'CI required') {

### #3

- file: docs/runbooks/zero-cost-production.md
- line: 80
- quote: 1. Merge chỉ khi check **CI required** trên GitHub xanh.

### #4

- file: .github/workflows/ci.yml
- line: 7
- quote: merge_group:

### dependabot

- file: .github/dependabot.yml

### ecosystems

- npm (/)
- github-actions (/)
- devcontainers (/)
- docker-compose (/)
- docker (/apps/core-api)
- docker (/apps/signaling-gateway)
- docker (/apps/media-server)

- schedule: Hàng tuần, thứ Hai 03:00-04:30 giờ Asia/Ho_Chi_Minh

- openPullRequestsLimit: 0

- effect: Mọi mục đặt open-pull-requests-limit: 0 nên Dependabot không mở PR cập nhật phiên bản (hành vi của GitHub với giá trị 0). Dependabot security updates là cài đặt riêng, không thấy trong file.

- coverageGaps: /apps/media-server chỉ có compose/yaml, không có Dockerfile; Dockerfile của apps/web, deploy/hosted/*, deploy/production/Dockerfile.edge và Dockerfile.dev không nằm trong thư mục docker nào được khai (theo git ls-files).

### evidence

### #1

- file: .github/dependabot.yml
- line: 10
- quote: open-pull-requests-limit: 0

### #2

- file: .github/dependabot.yml
- line: 6
- quote: interval: weekly

### #3

- file: .github/dependabot.yml
- line: 58
- quote: directory: /apps/media-server

- codeowners: .github/CODEOWNERS

- codeownersDetail: Một owner duy nhất (@ducmanh001) cho `*` và liệt kê riêng .github/, migrations, module economy, docker-compose.yml, signaling-gateway, libs/observability, staging-reliability.yml, runbook reliability và scripts/reliability. Không thấy được việc GitHub có bắt buộc code-owner review hay không.

### codeownersEvidence

### #1

- file: .github/CODEOWNERS
- line: 2
- quote: * @ducmanh001

### #2

- file: .github/CODEOWNERS
- line: 5
- quote: /.github/ @ducmanh001

### #3

- file: .github/CODEOWNERS
- line: 7
- quote: /apps/core-api/src/modules/economy/ @ducmanh001

### notes

- PR template yêu cầu bảng giả định (§ 10.0) cho flow nhạy cảm, tick `review-module verify`, migration mới thay vì sửa, và `pnpm ci:preflight` pass trước lần push cuối.
- Định dạng commit `<type>(<scope>): ...` là quy ước tài liệu; không có commit-msg hook hay commitlint. Theo git log, nhiều commit squash-merge trên main mang tiêu đề dạng tên nhánh kèm số PR (ví dụ "fix/matching-payload-400 (#67)").
- Workflow dùng quyền tối thiểu (`contents: read`), checkout `persist-credentials: false`, action pin SHA; ci.yml không dùng secret của repo.
- Hosted release không khai `environment:` cho job deploy; staging-reliability khai `environment: staging` (reviewer/approval nếu có nằm ở cài đặt GitHub).
- SECURITY.md yêu cầu báo lỗ hổng riêng tư và không đưa credential vào remote URL, issue, CI log hay image.
- Cổng bật/tắt release hosted là biến repo HOSTED_RELEASE_ENABLED, không phải quy tắc nhánh.

### notesEvidence

### #1

- file: .github/PULL_REQUEST_TEMPLATE.md
- line: 12
- quote: ## Bảng giả định & chỗ chặn

### #2

- file: .github/SECURITY.md
- line: 14
- quote: - Never place a token in a Git remote URL, source file, Docker image, issue, or CI log.

### #3

- file: .github/workflows/staging-reliability.yml
- line: 37
- quote: environment: staging
