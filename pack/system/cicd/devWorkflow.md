# CI/CD: pipelines, quality gates, release, images: devWorkflow

id: system:cicd:devWorkflow

Written by an AI agent from the code; every file:line quote was checked automatically (exists within ±3 lines) but NO independent verification pass has run yet. Treat claims as strong leads; open the cited file before editing.

### Chốt objective/out-of-scope/acceptance, rồi nạp đúng docs, invariant và gate của scope (default, do…

- order: 1
- step: Định vị bối cảnh
- command: pnpm agent:context <scope>
- evidence (evidence): AGENTS.md:21, .agents/context-map.json:138

### Cài dependency frozen, bật Postgres/Redis/Kafka, chạy migration, chạy doctor kiểm Node/pnpm/Docker/…

- order: 2
- step: Dựng môi trường dev
- command: pnpm bootstrap  (hoặc pnpm dev:up cho full Compose)
- evidence (evidence): docs/runbooks/local-development.md:56, scripts/doctor.mjs:68

### Test viết cùng thay đổi; schema chỉ đổi bằng migration mới; test chạm tiền dùng Postgres thật với I…

- order: 3
- step: Sửa lát nhỏ cùng test
- command: pnpm nx test <project> --runTestsByPath <file>; pnpm nx lint <project>
- evidence (evidence): AGENTS.md:25, docs/runbooks/quality-gates.md:97

### Commit spec openapi/core-api.json, client libs/api-client/src/generated/core-api.ts và báo cáo docs…

- order: 4
- step: Đồng bộ artifact sinh ra
- command: pnpm openapi:sync (đổi DTO/controller); pnpm docs:generate rồi pnpm docs:check (đổi feature registry/spec)
- evidence (evidence): docs/runbooks/local-development.md:76, docs/runbooks/local-development.md:77

### Verify chạy agent:check, agent:test, openapi:check (nếu có), lint/test/build đúng project, E2E nếu …

- order: 5
- step: Chạy gate theo scope
- command: pnpm agent:verify <scope> --tier=fast  rồi  pnpm agent:verify <scope>
- evidence (evidence): docs/15-commit-guidelines.md:68, scripts/agent/verify.mjs:15

### Husky chạy lint-staged (Prettier ghi đè file staged) rồi `agent:check --staged`; message theo docs/…

- order: 6
- step: Commit (hook pre-commit)
- command: git add <file tường minh>; git commit -m "<type>(<scope>): ..."
- evidence (evidence): .husky/pre-commit:26, docs/15-commit-guidelines.md:12

### Chạy clean quality trong container Node 22, test/build/E2E có DB thật và container smoke (Linux); t…

- order: 7
- step: Push (hook pre-push)
- command: git push  → pnpm ci:preflight
- evidence (evidence): .husky/pre-push:36, docs/runbooks/quality-gates.md:66

### Điền mô tả, luồng nghiệp vụ, bảng giả định (flow nhạy cảm), checklist và bằng chứng test; CODEOWNER…

- order: 8
- step: Mở PR
- command: dùng .github/PULL_REQUEST_TEMPLATE.md
- evidence (evidence): .github/PULL_REQUEST_TEMPLATE.md:12, .github/CODEOWNERS:5

### Cùng gate với `ci:local:quick`, `ci:local`, `ci:local:docker`; push mới vào PR hủy run cũ.

- order: 9
- step: CI trên PR
- command: GitHub Actions: quality → (test ∥ docker) → CI required
- evidence (evidence): .github/workflows/ci.yml:6, .github/workflows/ci.yml:12, .github/workflows/ci.yml:169

### Tài liệu yêu cầu `CI required` xanh; cách merge (squash hay không) và việc bắt buộc check nằm ở cài…

- order: 10
- step: Merge vào main
- command: (thao tác GitHub; merge queue dùng sự kiện merge_group)
- evidence (evidence): docs/runbooks/zero-cost-production.md:80, .github/workflows/ci.yml:7

### Migration → Northflank build theo SHA → Cloudflare web+admin → public smoke. Profile single-node và…

- order: 11
- step: CI trên main rồi release
- command: Hosted release tự chạy khi CI push của main success
- evidence (evidence): .github/workflows/hosted-release.yml:5, .github/workflows/hosted-release.yml:95, .github/workflows/hosted-release.yml:104
