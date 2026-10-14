# CI/CD: pipelines, quality gates, release, images: localPipeline

id: system:cicd:localPipeline

Written by an AI agent from the code; every file:line quote was checked automatically (exists within ±3 lines) but NO independent verification pass has run yet. Treat claims as strong leads; open the cited file before editing.

- entry: node scripts/ci/local.mjs <quick|prepush|clean|ci-test|ci|docker|security|all> [--dry-run] [--bypass]

- entryNote: Một runner duy nhất cho máy dev, hook pre-push và GitHub CI: workflow chỉ gọi `pnpm ci:local:quick`, `pnpm ci:local` và `pnpm ci:local:docker`. `--dry-run` in kế hoạch mà không chạy lệnh (`pnpm ci:local:plan`).

### profiles

### quick

- command: pnpm ci:local:quick
- stages: install; nx-reset; agent-check; agent-test; workflow-lint; format-check; lint
- when: Trước khi mở PR; đúng nội dung job `quality` của GitHub CI.
- purpose: Gate chất lượng nhanh, không cần Docker: agent check/test, workflow lint, format check, lint.
- evidence (evidence): scripts/ci/local.mjs:1065

### prepush

- command: pnpm ci:local:prepush
- stages: install; nx-reset; agent-check; agent-test; workflow-lint; format-check; lint; openapi-check; test-services; ci-database; affected-signaling; affected-test; affected-build; affected-e2e
- when: Gate nhanh thủ công theo project bị ảnh hưởng; hook pre-push thực tế gọi `ci:preflight`, không phải profile này.
- purpose: Quality + OpenAPI check + test/build/E2E chỉ cho project affected so với AGENT_BASE_SHA hoặc origin/main; chỉ bật DB/Redis khi core-api hoặc signaling bị ảnh hưởng.
- evidence (evidence): scripts/ci/local.mjs:1069

### clean

- command: pnpm ci:local:clean
- stages: clean-container
- when: Khi cần mô phỏng runner Linux sạch cho quality gate; cần Docker.
- purpose: Quality gate check-only trong container Node 22 Linux.
- evidence (evidence): scripts/ci/local.mjs:1074

### ci-test

- command: node scripts/ci/local.mjs ci-test (không có script pnpm riêng)
- stages: install; nx-reset; test-services; ci-database; frontend-verify; signaling-test; unit-integration-coverage; build-backend; e2e
- when: Profile con do profile `all` gọi; CI chạy qua profile `ci`.
- purpose: Test, build backend và E2E có DB/Redis thật.
- evidence (evidence): scripts/ci/local.mjs:1078

### ci

- command: pnpm ci:local
- stages: install; nx-reset; agent-check; agent-test; workflow-lint; format-check; lint; test-services; ci-database; frontend-verify; signaling-test; unit-integration-coverage; build-backend; e2e
- when: Job `test` của GitHub CI; local khi muốn chạy toàn bộ test/build/E2E.
- purpose: runQuality() rồi runTestAndBuild().
- evidence (evidence): scripts/ci/local.mjs:1082

### docker

- command: pnpm ci:local:docker
- stages: install; nx-reset; test-services; ci-database; docker-build-all; docker-migrate; docker-image-core; docker-image-signaling; docker-image-web; docker-image-edge; docker-smoke-start; edge-validate; health-wait
- when: Job `docker` của GitHub CI; trên máy cục bộ chỉ chạy được trên Linux/WSL (host networking).
- purpose: Build 4 image và chạy thử 3 container + kiểm cấu hình Caddy.
- evidence (evidence): scripts/ci/local.mjs:1087

### security

- command: pnpm ci:local:security
- when: Hiện vô hiệu: chỉ in thông báo rồi thoát 0.
- purpose: Không chạy secret scan hay vulnerability scan; tài liệu cấm mô tả nó như security PASS.
- evidence (evidence): scripts/ci/local.mjs:1093

### all

- command: pnpm ci:preflight (cùng nghĩa pnpm ci:local:all; `pnpm ci:local:plan` = all --dry-run)
- stages: parallel-profiles; clean-container; install; nx-reset; test-services; ci-database; frontend-verify; signaling-test; unit-integration-coverage; build-backend; e2e; docker-migrate; docker-image-core; docker-image-signaling; docker-image-web; docker-image-edge; docker-smoke-start; edge-validate; health-wait
- when: Hook pre-push; trước merge/release. Linux: clean và ci-test song song rồi docker smoke dùng lại build; Windows: tuần tự và bỏ qua docker smoke.
- purpose: Phạm vi đầy đủ nhất của máy dev: quality sạch + test/build/E2E + container smoke.
- evidence (evidence): package.json:50

### stages

### install

- label: Install dependencies from the lockfile
- command: pnpm install --frozen-lockfile (thêm --force khi LOCAL_CI_STRICT_CLEAN)
- policyProfile: dependency-install
- timeoutMs: 1440000
- softTimeoutMs: 540000
- purpose: Cài dependency đúng lockfile; chỉ chạy một lần mỗi tiến trình runner.
- evidence (evidence): scripts/ci/local.mjs:250

### nx-reset

- label: Reset Nx daemon and project-graph cache
- command: pnpm nx reset --outputStyle=static
- policyProfile: nx-reset
- timeoutMs: 900000
- softTimeoutMs: 180000
- purpose: Chỉ chạy khi LOCAL_CI_RESET_NX hoặc LOCAL_CI_STRICT_CLEAN; mặc định dùng lại cache Nx bền vững ở .nx/local-ci.
- evidence (evidence): scripts/ci/local.mjs:261

### agent-check

- label: Agent contract and guard checks
- command: pnpm agent:check
- policyProfile: agent
- timeoutMs: 900000
- softTimeoutMs: 180000
- purpose: Guard diff, wording trung lập, adapter parity, symlink, link Markdown, context-map, workflow policy.
- evidence (evidence): scripts/ci/local.mjs:311

### agent-test

- label: Agent guard tests
- command: pnpm agent:test
- policyProfile: agent
- timeoutMs: 900000
- softTimeoutMs: 180000
- purpose: node --test cho scripts/agent, scripts/ci và scripts/release (gồm test của chính runner và production-profile).
- evidence (evidence): scripts/ci/local.mjs:312

### workflow-lint

- label: Validate every GitHub Actions workflow
- command: actionlint -shellcheck=<shellcheck> .github/workflows/*.yml
- policyProfile: workflow-lint
- timeoutMs: 960000
- softTimeoutMs: 360000
- purpose: Tải actionlint 1.7.12 + shellcheck 0.11.0 (kiểm sha256) qua security-tools.mjs rồi lint mọi workflow; chỉ chạy trên host Linux, nơi khác chạy trong container clean.
- evidence (evidence): scripts/ci/local.mjs:1056

### format-check

- label: Format check
- command: pnpm format:check
- policyProfile: format
- timeoutMs: 960000
- softTimeoutMs: 360000
- purpose: prettier --check toàn repo; không tự sửa file.
- evidence (evidence): scripts/ci/local.mjs:314

### lint

- label: Lint every Nx project
- command: pnpm nx run-many -t lint --outputStyle=static
- policyProfile: default
- timeoutMs: 2400000
- softTimeoutMs: 900000
- purpose: ESLint mọi Nx project, gồm @nx/enforce-module-boundaries.
- evidence (evidence): scripts/ci/local.mjs:315

### openapi-check

- label: OpenAPI contract check
- command: pnpm openapi:check
- policyProfile: default
- timeoutMs: 2400000
- softTimeoutMs: 900000
- purpose: Emit OpenAPI vào thư mục tạm và so với openapi/core-api.json + libs/api-client/src/generated/core-api.ts; lệch thì fail.
- evidence (evidence): scripts/ci/local.mjs:395

### test-services

- label: Start local PostgreSQL and Redis
- command: docker compose up --detach --wait postgres redis
- policyProfile: services
- timeoutMs: 960000
- softTimeoutMs: 360000
- purpose: Dùng lại container litmatch-postgres/litmatch-redis nếu khỏe; trong CI (LOCAL_CI_SERVICES_READY=true) dùng service container của job.
- evidence (evidence): scripts/ci/local.mjs:298

### ci-database

- label: Create isolated local CI database
- command: docker exec litmatch-postgres psql ... CREATE DATABASE <tên>
- policyProfile: database
- timeoutMs: 960000
- softTimeoutMs: 360000
- purpose: Tạo database cô lập cho DATABASE_URL (mặc định litmatch_ci) và INTEGRATION_DB_URL (litmatch_test); trong CI LOCAL_CI_DATABASE_READY=true nên bỏ qua.
- evidence (evidence): scripts/ci/local.mjs:641

### frontend-verify

- label: Frontend contract, tests, builds and bundle audit
- command: pnpm agent:verify frontend
- policyProfile: inner-watchdog
- timeoutNote: Stage tự giữ watchdog từng bước bên trong (ownsInnerWatchdogs); timeoutMs của stage ngoài là null, ngưỡng thật nằm ở innerTimeouts.
- innerTimeouts:
  - agent-verify frontend: pnpm agent:check: 3600000
  - agent-verify frontend: pnpm nx run-many: 3600000
  - agent-verify frontend: pnpm format:check: 3600000
- purpose: agent:check, agent:test, openapi:check, format:check, lint/test/build admin+web+api-client và browser bundle audit; tự giữ watchdog từng bước bên trong.
- evidence (evidence): scripts/ci/local.mjs:542

### signaling-test

- label: Signaling Redis integration tests with isolated CPU
- command: pnpm nx test signaling-gateway --coverage --skip-nx-cache --outputStyle=static
- policyProfile: signaling
- timeoutMs: 1440000
- softTimeoutMs: 540000
- purpose: Chạy riêng vì suite lease Redis dùng TTL tối thiểu, tranh CPU với Jest pool lớn sẽ giả lập replica chết.
- evidence (evidence): scripts/ci/local.mjs:551

### unit-integration-coverage

- label: Remaining unit and integration tests with coverage
- command: pnpm nx run-many -t test --coverage --skip-nx-cache --exclude=admin,web,api-client,signaling-gateway --outputStyle=static
- policyProfile: unit-integration-test
- timeoutMs: 3600000
- softTimeoutMs: 1800000
- purpose: Test còn lại (core-api, các lib) với coverage; nơi duy nhất áp ngưỡng coverage của core-api.
- evidence (evidence): scripts/ci/local.mjs:559

### build-backend

- label: Build backend projects
- command: pnpm nx run-many -t build --exclude=admin,web,api-client --outputStyle=static
- policyProfile: build
- timeoutMs: 3600000
- softTimeoutMs: 1800000
- purpose: Build core-api và signaling-gateway (frontend đã build trong frontend-verify); đánh dấu projectsBuilt để docker smoke dùng lại output.
- evidence (evidence): scripts/ci/local.mjs:569

### e2e

- label: End-to-end smoke tests
- command: pnpm nx run-many -t e2e --skip-nx-cache --parallel=1 --outputStyle=static
- policyProfile: e2e
- timeoutMs: 3600000
- softTimeoutMs: 1800000
- purpose: E2E core-api-e2e, signaling-gateway-e2e và web Playwright, tuần tự vì dùng chung database.
- evidence (evidence): scripts/ci/local.mjs:578

### affected-signaling

- label: Affected signaling tests (isolated)
- command: pnpm nx test signaling-gateway --skip-nx-cache --outputStyle=static
- policyProfile: signaling
- timeoutMs: 1440000
- softTimeoutMs: 540000
- purpose: Chỉ khi signaling-gateway bị ảnh hưởng; chạy cô lập như signaling-test.
- evidence (evidence): scripts/ci/local.mjs:421

### affected-test

- label: Affected unit and integration tests
- command: pnpm nx affected -t test --skip-nx-cache --exclude=signaling-gateway --base <sha> --parallel=1
- policyProfile: unit-integration-test
- timeoutMs: 3600000
- softTimeoutMs: 1800000
- purpose: Test các project bị ảnh hưởng so với AGENT_BASE_SHA hoặc origin/main.
- evidence (evidence): scripts/ci/local.mjs:429

### affected-build

- label: Affected builds
- command: pnpm nx affected -t build --base <sha> --parallel=1
- policyProfile: build
- timeoutMs: 3600000
- softTimeoutMs: 1800000
- purpose: Build các project bị ảnh hưởng.
- evidence (evidence): scripts/ci/local.mjs:438

### affected-e2e

- label: Affected end-to-end tests
- command: pnpm nx affected -t e2e --skip-nx-cache --base <sha> --parallel=1
- policyProfile: e2e
- timeoutMs: 3600000
- softTimeoutMs: 1800000
- purpose: E2E các project bị ảnh hưởng.
- evidence (evidence): scripts/ci/local.mjs:445

### docker-build-all

- label: Build all projects for Docker images
- command: pnpm nx run-many -t build --outputStyle=static
- policyProfile: docker
- timeoutMs: 3600000
- softTimeoutMs: 2700000
- purpose: Build toàn bộ project cho Dockerfile cần dist/; bỏ qua khi output đã được test/build kiểm chứng.
- evidence (evidence): scripts/ci/local.mjs:773

### docker-migrate

- label: Run database migrations in the isolated local CI database
- command: pnpm db:migrate
- policyProfile: database
- timeoutMs: 960000
- softTimeoutMs: 360000
- purpose: Chạy mọi migration vào DATABASE_URL cô lập trước khi bật container.
- evidence (evidence): scripts/ci/local.mjs:781

### docker-image-core

- label: Build Core API image
- command: docker build --file apps/core-api/Dockerfile --tag litmatch/core-api:local-<sha> .
- policyProfile: build
- timeoutMs: 3600000
- softTimeoutMs: 1800000
- purpose: Build image core-api; trong GitHub dùng `buildx build --load` với cache type=gha.
- evidence (evidence): scripts/ci/local.mjs:815

### docker-image-signaling

- label: Build Signaling Gateway image
- command: docker build --file apps/signaling-gateway/Dockerfile --tag litmatch/signaling-gateway:local-<sha> .
- policyProfile: signaling
- timeoutMs: 1440000
- softTimeoutMs: 540000
- purpose: Build image signaling-gateway.
- evidence (evidence): scripts/ci/local.mjs:818

### docker-image-web

- label: Build Web image
- command: docker build --file apps/web/Dockerfile --tag litmatch/web:local-<sha> .
- policyProfile: build
- timeoutMs: 3600000
- softTimeoutMs: 1800000
- purpose: Build image web Next.js standalone.
- evidence (evidence): scripts/ci/local.mjs:824

### docker-image-edge

- label: Build Edge image
- command: docker build --file deploy/production/Dockerfile.edge --tag litmatch/edge:local-<sha> .
- policyProfile: docker
- timeoutMs: 3600000
- softTimeoutMs: 2700000
- purpose: Build image Caddy edge kèm admin SPA.
- evidence (evidence): scripts/ci/local.mjs:827

### docker-smoke-start

- label: Start Core API smoke container
- command: docker run --detach --network host ... (core-api, signaling-gateway, web)
- policyProfile: docker
- timeoutMs: 3600000
- softTimeoutMs: 2700000
- purpose: Chạy core-api, signaling-gateway (NODE_ENV=production, secret CI-only, storage S3 giả lập local, provider ngoài tắt) và web (cổng 4300) với `--network host`.
- evidence (evidence): scripts/ci/local.mjs:833

### edge-validate

- label: Validate Edge configuration
- command: docker run --rm --entrypoint caddy <edge image> validate --config /etc/caddy/Caddyfile
- policyProfile: docker
- timeoutMs: 3600000
- softTimeoutMs: 2700000
- purpose: Kiểm cú pháp Caddyfile; không chạy edge thật.
- evidence (evidence): scripts/ci/local.mjs:924

### health-wait

- label: (vòng lặp curl, không qua stage runner)
- command: curl --fail http://127.0.0.1:3000/health/ready, :3001/health/ready, :4300/
- policyProfile: none
- timeoutMs: 30000
- purpose: Chờ cả ba endpoint sẵn sàng, tối đa 30 lần cách 1 giây; quá hạn in log container và fail.
- evidence (evidence): scripts/ci/local.mjs:740

### clean-container

- label: Run quality gate in a clean Node 22 Linux container
- command: docker run --rm ... node:22-bookworm@sha256:<digest> bash -lc "<install; agent; workflow lint; format; lint>"
- policyProfile: inner-watchdog
- timeoutNote: Stage tự giữ watchdog từng bước bên trong (ownsInnerWatchdogs); timeoutMs của stage ngoài là null, ngưỡng thật nằm ở innerTimeouts.
- innerTimeouts:
  - clean: install dependencies: 1440000
  - clean: prepare Nx: 2400000
  - clean: agent check: 2400000
  - clean: agent tests: 900000
  - clean: workflow lint: 960000
  - clean: format check: 960000
  - clean: lint: 2400000
- purpose: Chạy quality gate check-only trong container Linux sạch; mỗi bước con có watchdog riêng (clean: install dependencies, agent check, agent tests, workflow lint, format check, lint).
- evidence (evidence): scripts/ci/local.mjs:502

### parallel-profiles

- label: Parallel clean profile
- command: spawn `local.mjs clean` và `local.mjs ci-test` đồng thời (chỉ Linux)
- policyProfile: aggregate
- timeoutMs: 3600000
- softTimeoutMs: 1800000
- purpose: Profile `all` chạy clean và ci-test song song bằng run-stage.mjs; Windows chạy tuần tự trừ khi LOCAL_CI_PARALLEL=true.
- evidence (evidence): scripts/ci/local.mjs:946

### hooks

### #1

- hook: .husky/pre-commit
- runs: `pnpm exec lint-staged` (prettier --write --ignore-unknown trên file staged) rồi `pnpm agent:check -- --staged`
- blocksOn: Một trong hai lệnh thoát khác 0 (guard đọc nội dung từ git index khi --staged); LITMATCH_CI_BYPASS/CI_BYPASS=1|true|yes|on bỏ qua toàn bộ hook.
- evidence (evidence): .husky/pre-commit:26, .husky/pre-commit:27, .husky/pre-commit:4, package.json:73

### #2

- hook: .husky/pre-push
- runs: `pnpm ci:preflight` (= local.mjs all) sau khi ép NX_TUI/NX_DAEMON/NX_NATIVE_COMMAND_RUNNER=false, TERM=dumb
- blocksOn: Bất kỳ stage nào của profile `all` fail (clean quality, test/build/E2E, docker smoke trên Linux); bypass tương tự pre-commit.
- evidence (evidence): .husky/pre-push:36, .husky/pre-push:12, .husky/pre-push:4

### #3

- hook: Không có hook commit-msg
- runs: Không có script `.husky/commit-msg` và không có commitlint trong package.json; Husky chỉ sinh stub rỗng.
- blocksOn: Không chặn gì: định dạng `<type>(<scope>): ...` trong docs/15 chỉ là quy ước, không có máy kiểm.
- evidence (evidence): docs/15-commit-guidelines.md:9, docs/15-commit-guidelines.md:75

### #4

- hook: Agent runtime: SessionStart + PreToolUse (.claude/settings.json)
- runs: `scripts/agent/session-start.mjs` in bối cảnh định vị repo; `scripts/agent/pre-tool-guard.mjs` chạy trước tool Write|Edit|Bash của agent.
- blocksOn: pre-tool-guard thoát 2 (BLOCKED) khi vi phạm guard-core: tạo apps/* ngoài baseline, bật synchronize của TypeORM, mutate ledger_entries, sửa/xoá migration đã commit, rm/mv migration, passWithNoTests trong E2E, và rule frontend (env, fetch/axios, import common-dtos, import core-api).
- evidence (evidence): .claude/settings.json:9, .claude/settings.json:16, scripts/agent/pre-tool-guard.mjs:59, scripts/agent/guard-core.mjs:75

### watchdog

- what: Mỗi stage chạy qua scripts/ci/run-stage.mjs: soft timeout chỉ cảnh báo SLOW_STAGE; hard timeout gửi SIGTERM cho cả process group rồi SIGKILL sau 5 giây, thoát 124 và in marker TIMED_OUT. Ngưỡng theo nhãn stage (hard = 8 x normal, giới hạn 15-60 phút; soft = 3 x normal), ghi đè bằng LOCAL_CI_STAGE_TIMEOUT_MS.

### evidence

### #1

- file: scripts/ci/run-stage.mjs
- line: 87
- quote: `[stage-runner] SLOW_STAGE after ${softTimeoutMs}ms: ${label}; ` +

### #2

- file: scripts/ci/run-stage.mjs
- line: 117
- quote: process.exitCode = 124;

### #3

- file: scripts/ci/stage-policy.mjs
- line: 84
- quote: const derivedSoftTimeoutMs = normalMs * 3;

### #4

- file: scripts/ci/stage-policy.mjs
- line: 86
- quote: Math.max(normalMs * 8, MIN_HARD_TIMEOUT_MS),

### #5

- file: scripts/ci/local.mjs
- line: 35
- quote: process.env['LOCAL_CI_STAGE_TIMEOUT_MS']

### bypassRule

- what: Bypass (`--bypass`, LITMATCH_CI_BYPASS, CI_BYPASS) chỉ hợp lệ cho hook cục bộ: local.mjs thoát 0 ngay; khi CI/GITHUB_ACTIONS bật thì từ chối và thoát 1. workflow-policy cấm mọi từ khóa bypass trong ci.yml.

### evidence

### #1

- file: scripts/ci/local.mjs
- line: 87
- quote: Refusing bypass for profile

### #2

- file: scripts/ci/workflow-policy.mjs
- line: 118
- quote: 'CI workflow không được có bypass làm check `CI required` xanh mà bỏ qua gate.',

### cacheRule

- what: Nx cache và workspace data của runner nằm ở `.nx/local-ci` (NX_CACHE_DIRECTORY, NX_WORKSPACE_DATA_DIRECTORY). Test/E2E luôn `--skip-nx-cache` vì chạm Postgres/Redis thật; build và lint dùng cache theo input.

### evidence

### #1

- file: scripts/ci/local.mjs
- line: 154
- quote: join(localCiNxRoot, 'cache'),

### #2

- file: nx.json
- line: 40
- quote: "cache": false

### #3

- file: docs/runbooks/quality-gates.md
- line: 59
- quote: Test của `core-api` và `signaling-gateway` không cache vì có integration suite dùng PostgreSQL/Redis;

### evidence

### #1

- file: scripts/ci/local.mjs
- line: 11
- quote: const profile = process.argv[2] ?? 'ci';

### #2

- file: scripts/ci/local.mjs
- line: 1091
- quote: if (profile === 'security') {

### #3

- file: package.json
- line: 41
- quote: "ci:local:quick": "node scripts/ci/local.mjs quick"

### #4

- file: docs/runbooks/quality-gates.md
- line: 26
- quote: ## Local CI profiles

- cleanContainerRule: Profile `clean` (và nhánh clean của `all`) chạy quality gate check-only trong container `node:22-bookworm` ghim digest, mount repo tại /workspace và 3 named volume (node_modules, pnpm store, .nx) để lần sau không bootstrap lại. LOCAL_CI_STRICT_CLEAN=true dùng volume tạm, `pnpm install --force`, nx reset và docker build `--no-cache`.

### cleanContainerRuleEvidence

### #1

- file: scripts/ci/local.mjs
- line: 27
- quote: 'node:22-bookworm@sha256:

### #2

- file: scripts/ci/local.mjs
- line: 510
- quote: 'litmatch-local-ci-node-modules'

### #3

- file: scripts/ci/local.mjs
- line: 807
- quote: ...(strictCleanRequested ? ['--no-cache'] : []),

### #4

- file: docs/runbooks/quality-gates.md
- line: 92
- quote: Quick/clean/prepush/preflight không tự sửa source; format sai phải được sửa riêng bằng `pnpm format`.

### environmentInjected

- summary: local.mjs ghi đè env cho mọi stage để gate không phụ thuộc .env của máy dev: cờ CI/Nx không tương tác, secret CI-only cố định, storage S3 giả lập local, và DATABASE_URL/INTEGRATION_DB_URL/REDIS_URL cô lập.

### names

- CI=true
- HUSKY=0
- NX_DAEMON=false
- NX_TUI=false
- NX_NATIVE_COMMAND_RUNNER=false
- NX_TASKS_RUNNER_DYNAMIC_OUTPUT=false
- NX_INTERACTIVE=false
- TERM=dumb
- NO_COLOR=1
- FORCE_COLOR=0
- NX_CACHE_DIRECTORY
- NX_WORKSPACE_DATA_DIRECTORY
- JWT_SECRET
- AUTH_OTP_PEPPER
- AUTH_GUEST_DEVICE_TOKEN_SECRET
- MATCHING_GUEST_QUOTA_PEPPER
- MEDIA_STORAGE_PROVIDER=s3
- AWS_REGION
- AWS_S3_ENDPOINT
- AWS_S3_BUCKET
- AWS_ACCESS_KEY_ID
- AWS_SECRET_ACCESS_KEY
- AWS_S3_FORCE_PATH_STYLE
- MEDIA_PUBLIC_BASE_URL
- MEDIA_UPLOAD_URL_TTL_SECONDS
- DATABASE_URL (LOCAL_CI_DATABASE_URL)
- REDIS_URL (LOCAL_CI_REDIS_URL, db 15)
- INTEGRATION_DB_URL (LOCAL_CI_INTEGRATION_DB_URL)
- NEXT_PUBLIC_API_URL
- NEXT_PUBLIC_SOCKET_URL
- NEXT_PUBLIC_LIVEKIT_URL

- smokeContainerFlags: Container core-api trong docker smoke chạy NODE_ENV=production, phone OTP bật và provider ngoài tắt (VIDEO_UPLOAD_ENABLED, NOTIFICATION_PUSH_PROVIDER, ECONOMY_IAP_VERIFIER, outbox relay, refund poll); LiveKit trỏ placeholder.

### evidence

### #1

- file: scripts/ci/local.mjs
- line: 104
- quote: CI: 'true',

### #2

- file: scripts/ci/local.mjs
- line: 105
- quote: HUSKY: '0',

### #3

- file: scripts/ci/local.mjs
- line: 124
- quote: MEDIA_STORAGE_PROVIDER: 's3',

### #4

- file: scripts/ci/local.mjs
- line: 875
- quote: 'VIDEO_UPLOAD_ENABLED=false',

### #5

- file: scripts/ci/local.mjs
- line: 877
- quote: 'NOTIFICATION_PUSH_PROVIDER=disabled',

### #6

- file: scripts/ci/local.mjs
- line: 885
- quote: 'ECONOMY_OUTBOX_RELAY_ENABLED=false',

### #7

- file: scripts/ci/local.mjs
- line: 121
- quote: Production-mode smoke must boot a real storage adapter. The endpoint and
