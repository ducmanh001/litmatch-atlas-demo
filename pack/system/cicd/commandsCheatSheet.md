# CI/CD: pipelines, quality gates, release, images: commandsCheatSheet

id: system:cicd:commandsCheatSheet

Written by an AI agent from the code; every file:line quote was checked automatically (exists within ±3 lines) but NO independent verification pass has run yet. Treat claims as strong leads; open the cited file before editing.

### In bối cảnh bắt buộc của scope: docs đọc trước, đọc khi áp dụng, path thay đổi, invariant và gate c…

- command: pnpm agent:context <scope>
- whenToUse: Trước khi sửa bất kỳ thứ gì; chọn scope theo domain.
- evidence (evidence): package.json:37

### Gate theo scope (frontend, core, economy, matching, calling, content, signaling, media, infra): age…

- command: pnpm agent:verify <scope> [--tier=fast]
- whenToUse: Sau khi sửa trong một scope, trước commit/PR.
- evidence (evidence): package.json:38, scripts/agent/verify.mjs:31

### Repository guard: guard diff, wording trung lập, adapter parity, link Markdown, context-map, workfl…

- command: pnpm agent:check [-- --staged]
- whenToUse: Mọi thay đổi (docs, tooling, code); hook pre-commit chạy bản --staged.
- evidence (evidence): package.json:39

### node --test cho scripts/agent, scripts/ci, scripts/release (guard, runner CI, workflow policy, rele…

- command: pnpm agent:test
- whenToUse: Khi đổi tooling/CI/release; chạy trong CI.
- evidence (evidence): package.json:40

### Cùng nghĩa ci:local:all: clean quality trong container Node 22 + test/build/E2E + container smoke; …

- command: pnpm ci:preflight
- whenToUse: Trước push cuối cùng và trước merge/release; `pnpm ci:local:plan` in kế hoạch mà không chạy.
- evidence (evidence): package.json:50, package.json:48

### Quality nhanh: install frozen, agent check/test, actionlint, format check, lint.

- command: pnpm ci:local:quick
- whenToUse: Vòng lặp nhanh trước khi mở PR; tương đương job quality.
- evidence (evidence): package.json:41

### Profile ci: quality + frontend verify + test có coverage + build backend + E2E (cần Postgres/Redis,…

- command: pnpm ci:local
- whenToUse: Tái hiện job test của GitHub CI.
- evidence (evidence): package.json:44

### Build 4 image, migration vào DB cô lập, chạy 3 container và kiểm /health/ready + caddy validate (ch…

- command: pnpm ci:local:docker
- whenToUse: Khi đổi Dockerfile, build, health endpoint hoặc Caddyfile; tái hiện job docker.
- evidence (evidence): package.json:45

### Quality + OpenAPI check + test/build/E2E chỉ cho Nx project bị ảnh hưởng so với AGENT_BASE_SHA hoặc…

- command: pnpm ci:local:prepush
- whenToUse: Gate nhanh thủ công khi thay đổi hẹp.
- evidence (evidence): package.json:42

### Emit OpenAPI từ core-api rồi sinh lại client TypeScript (openapi/core-api.json và libs/api-client/s…

- command: pnpm openapi:sync
- whenToUse: Mỗi khi đổi controller/DTO; commit cả hai output cùng thay đổi.
- evidence (evidence): package.json:53

### Emit vào thư mục tạm và so với file đã commit; báo drift nếu lệch.

- command: pnpm openapi:check
- whenToUse: Trước PR đổi API; CI chạy qua agent:verify frontend.
- evidence (evidence): package.json:54

### Chạy migration TypeORM (`migration:run --transaction each`) vào DATABASE_URL hiện tại.

- command: pnpm db:migrate
- whenToUse: Sau khi thêm migration hoặc pull migration mới; kiểm target bằng db:status/doctor trước.
- evidence (evidence): package.json:60

### Liệt kê migration đã/ chưa chạy (`migration:show`).

- command: pnpm db:status
- whenToUse: Trước thao tác phá hủy hoặc khi nghi DB lệch.
- evidence (evidence): package.json:61

### Hoàn tác migration gần nhất (`migration:revert --transaction each`).

- command: pnpm db:revert
- whenToUse: Chỉ trên DB local của bạn; release không dùng revert (migration forward-only).
- evidence (evidence): package.json:62

### Sinh lại docs/generated (báo cáo evidence md + docx, handbook docx) từ docs/feature-registry.json; …

- command: pnpm docs:generate
- whenToUse: Sau khi đổi feature registry hoặc các spec này.
- evidence (evidence): package.json:35, scripts/docs/generate.mjs:284

### Chạy test scripts/docs rồi `generate.mjs --check` để chắc output sinh ra không cũ.

- command: pnpm docs:check
- whenToUse: Trước khi dựa vào báo cáo đã sinh hoặc sau khi đổi docs/feature-registry.json; CI không chạy lệnh này.
- evidence (evidence): package.json:36

### Cài frozen lockfile, bật Postgres/Redis/Kafka, migration và doctor.

- command: pnpm bootstrap
- whenToUse: Lần đầu clone repo (host-native).
- evidence (evidence): package.json:17

### Dựng full stack dev bằng Compose (hạ tầng + migrate + core-api + signaling + admin + web + LiveKit)…

- command: pnpm dev:up
- whenToUse: Khi muốn chạy toàn bộ hệ thống cục bộ trong container.
- evidence (evidence): package.json:18

### Kiểm Node theo .nvmrc, pnpm theo packageManager, Docker, .env, credential trong git remote, cổng Po…

- command: pnpm doctor
- whenToUse: Khi môi trường dev hành xử lạ hoặc trước thao tác phá hủy.
- evidence (evidence): package.json:55

### Chạy Jest của core-api trên Postgres thật; có thể thêm --runTestsByPath <file>.

- command: pnpm nx test core-api --skip-nx-cache  (INTEGRATION_DB_URL=...)
- whenToUse: Khi chạm tiền/ledger hoặc logic cần DB thật.
- evidence (evidence): .github/PULL_REQUEST_TEMPLATE.md:24

### Xem target Nx thật của một project (build, test, lint, e2e, migration-run...).

- command: pnpm nx show project <name> --json
- whenToUse: Trước khi chạy target không chắc tên.
- evidence (evidence): README.md:58

### `format` ghi đè bằng Prettier; `format:check` chỉ kiểm tra.

- command: pnpm format  /  pnpm format:check
- whenToUse: Sửa lỗi format mà gate báo; CI chỉ chạy format:check.
- evidence (evidence): package.json:33, package.json:34

### Kiểm hợp đồng profile production (compose, biến bắt buộc, healthcheck image, K8s probe/replica) mà …

- command: pnpm release:profile-check
- whenToUse: Khi đổi compose, Dockerfile runtime hoặc manifest K8s.
- evidence (evidence): package.json:65

### Triển khai single-node trên VM: preflight, build, backup, migration, up, smoke, ghi trạng thái; rol…

- command: pnpm release:deploy  (release:preflight / release:backup / release:smoke / release:rollback)
- whenToUse: Chỉ khi vận hành profile single-node (chạy tay trên VM).
- evidence (evidence): package.json:66, package.json:69

### Xác thực 3 file evidence (loadtest, game-day, slo-dashboard) còn hạn và có đủ check; cần RELIABILIT…

- command: pnpm reliability:production-gate
- whenToUse: Trước khi promote production theo runbook reliability; không nằm trong workflow nào.
- evidence (evidence): package.json:27
