# CI/CD: pipelines, quality gates, release, images: qualityGates

id: system:cicd:qualityGates

Written by an AI agent from the code; every file:line quote was checked automatically (exists within ±3 lines) but NO independent verification pass has run yet. Treat claims as strong leads; open the cited file before editing.

### install-frozen-lockfile

- what: Cài dependency đúng pnpm-lock.yaml; lockfile lệch package.json thì cài thất bại.
- where: ci.yml `quality`/`test`/`docker` (qua local.mjs) và `deploy` của hosted-release.yml; hook pre-commit/pre-push gián tiếp
- blocking: true
- status: enforced
- evidence (evidence): scripts/ci/local.mjs:250, .github/workflows/hosted-release.yml:91, scripts/ci/workflow-policy.mjs:169

### format

- what: Prettier --check toàn repo; chỉ kiểm tra, không sửa. Hook pre-commit mới là nơi chạy `prettier --write` trên file staged.
- where: ci.yml `quality` (stage format-check) và lặp lại trong `test`; pnpm format:check
- blocking: true
- status: enforced
- evidence (evidence): package.json:34, scripts/ci/local.mjs:314

### lint

- what: ESLint mọi Nx project (flat config), gồm no-explicit-any = error và @nx/enforce-module-boundaries.
- where: ci.yml `quality` (stage lint) và lặp lại trong `test`; agent:verify <scope> chạy lint theo project
- blocking: true
- status: enforced
- evidence (evidence): package.json:31, eslint.config.mjs:61, scripts/ci/local.mjs:315

### typecheck

- what: Không có lệnh `tsc --noEmit`/`nx typecheck` nào trong CI hay hook. Type error chỉ bị bắt gián tiếp khi build: core-api và signaling-gateway build webpack `compiler: tsc`, web build bằng `next build`; admin (Vite) chỉ transpile.
- where: Không có stage riêng; nx.json suy ra target `typecheck` cho dự án Vite nhưng không workflow/script nào gọi `-t typecheck`
- blocking: false
- status: partial
- notes: Kết luận theo mã, chưa xác minh bằng chạy thử; admin có thể lọt lỗi kiểu nếu không test nào import đúng đoạn lỗi.
- evidence (evidence): nx.json:84, nx.json:98, apps/core-api/webpack.config.js:15

### arch-nx-boundaries

- what: Ranh giới tầng project: app chỉ phụ thuộc lib, lib không phụ thuộc app; tag platform (browser/server) và scope (frontend, core, signaling, e2e) giới hạn lib được import.
- where: ESLint rule `@nx/enforce-module-boundaries` trong eslint.config.mjs, chạy ở stage lint
- blocking: true
- status: enforced
- evidence (evidence): eslint.config.mjs:23, eslint.config.mjs:29

### arch-core-specs

- what: Test kiến trúc trong core-api: ranh giới module (exact ratchet cho legacy exception), ranh giới tầng (adapter không chạm ORM, không cycle), bố cục module, và LiveKit SDK chỉ có một owner.
- where: apps/core-api/src/arch/*.spec.ts, chạy như test Jest thường trong stage unit-integration-coverage
- blocking: true
- status: enforced
- evidence (evidence): apps/core-api/src/arch/module-boundaries.spec.ts:176, apps/core-api/src/arch/layer-boundaries.spec.ts:179, apps/core-api/src/arch/module-layout.spec.ts:63, apps/core-api/src/arch/livekit-boundary.spec.ts:23

### project-tags

- what: Mọi project.json phải có đúng một tag type:*, đúng một scope:* và ít nhất một platform:*.
- where: scripts/agent/project-tags.test.mjs, chạy trong `pnpm agent:test`
- blocking: true
- status: enforced
- evidence (evidence): scripts/agent/project-tags.test.mjs:29, package.json:40

### guard-diff

- what: Guard trên các file thay đổi so với base: cấm apps/* ngoài baseline, cấm bật synchronize của TypeORM, cấm mutate ledger_entries, migration đã commit bất biến và không được rename, E2E không được pass khi không có test, cùng rule riêng cho frontend (env, fetch/axios, common-dtos, import core-api).
- where: `pnpm agent:check` → validateDiff() trong repository-check.mjs; cùng logic chạy ở hook PreToolUse của agent
- blocking: true
- status: enforced
- notes: Phạm vi diff = AGENT_BASE_SHA...HEAD; với merge_group/workflow_dispatch biến này rỗng nên chỉ xét git diff HEAD.
- evidence (evidence): scripts/agent/repository-check.mjs:192, scripts/agent/guard-core.mjs:101, scripts/agent/guard-core.mjs:90, scripts/agent/repository-check.mjs:205, scripts/agent/guard-core.mjs:110

### neutral-wording

- what: Bề mặt dùng chung (AGENTS.md, docs/, scripts) không được nêu tên nền tảng agent cụ thể; ngoại lệ phải có marker `agent-check:allow-vendor-name`.
- where: `pnpm agent:check` → validateNeutralWording()
- blocking: true
- status: enforced
- evidence (evidence): scripts/agent/repository-check.mjs:247, scripts/agent/vendor-wording.mjs:29

### adapter-parity

- what: Mỗi AGENTS.md phải có CLAUDE.md symlink đúng đích, 3 skill bắt buộc (adaptive-orchestration, new-module, review-module) phải có symlink trong .claude/skills; bản sao `.github/skills` không được lệch `.agents/skills`; symlink gãy bị bắt.
- where: `pnpm agent:check` → validateAdapterParity(), validateGithubSkillMirrors(), validateSymlinks()
- blocking: true
- status: enforced
- evidence (evidence): scripts/agent/repository-check.mjs:294, scripts/agent/repository-check.mjs:325, scripts/agent/adapter-parity.mjs:4

### markdown-links

- what: Mọi link tương đối trong file Markdown phải trỏ tới file tồn tại (anchor và URL ngoài không kiểm).
- where: `pnpm agent:check` → validateMarkdownLinks()
- blocking: true
- status: enforced
- evidence (evidence): scripts/agent/repository-check.mjs:343, scripts/agent/markdown-links.mjs:7

### context-map

- what: Mọi path trong .agents/context-map.json phải tồn tại; mục readWhen phải có { path, when }; skill chỉ được có frontmatter name + description.
- where: `pnpm agent:check` → validateContextMap(), validateSkill()
- blocking: true
- status: enforced
- evidence (evidence): scripts/agent/repository-check.mjs:74, scripts/agent/repository-check.mjs:106

### workflow-policy

- what: Chính sách cho ci.yml và hosted-release.yml: trigger bắt buộc, job graph quality → test/docker → required, check tổng hợp `CI required`, cấm bypass, cấm action chưa pin SHA, hosted release chỉ deploy CI push thành công của main, cài lockfile frozen và mọi lệnh Nx của hosted release có `--outputStyle=static`.
- where: `pnpm agent:check` → workflowPolicyErrors() trong scripts/ci/workflow-policy.mjs
- blocking: true
- status: enforced
- evidence (evidence): scripts/agent/repository-check.mjs:137, scripts/ci/workflow-policy.mjs:55, scripts/ci/workflow-policy.mjs:98, scripts/ci/workflow-policy.mjs:159

### agent-check-umbrella

- what: `pnpm agent:check` là một lệnh gộp các gate trên (context-map, skill, ESLint toolchain dep, workflow policy, guard diff, wording, adapter parity, symlink, link Markdown); `--staged` đọc file từ git index cho hook pre-commit.
- where: ci.yml `quality` và `test` (qua local.mjs), `agent:verify <scope>`, hook pre-commit (--staged)
- blocking: true
- status: enforced
- evidence (evidence): package.json:39, scripts/ci/local.mjs:311, .husky/pre-commit:27

### agent-test

- what: `node --test` cho scripts/agent, scripts/ci, scripts/release: guard, runner local CI, workflow policy, release config/contract và production profile.
- where: ci.yml `quality` và `test` (stage agent-test)
- blocking: true
- status: enforced
- notes: Không gồm scripts/docs/*.test.mjs và scripts/reliability/*.test.mjs (chạy bằng docs:check và reliability:test).
- evidence (evidence): package.json:40, scripts/ci/local.mjs:312

### actionlint-shellcheck

- what: actionlint 1.7.12 kèm shellcheck 0.11.0 (tải về kiểm sha256) lint mọi .github/workflows/*.yml; trên host không phải Linux bước này nằm trong container clean.
- where: ci.yml `quality` (stage workflow-lint)
- blocking: true
- status: enforced
- evidence (evidence): scripts/ci/local.mjs:1056, scripts/ci/security-tools.mjs:20, scripts/ci/security-tools.mjs:34

### actions-sha-pinning

- what: Mọi `uses:` từ xa trong ci.yml và hosted-release.yml phải pin bằng commit SHA 40 ký tự; staging-reliability.yml cũng đang pin nhưng không nằm trong policy này.
- where: `pnpm agent:check` → unpinnedRemoteActions()
- blocking: true
- status: enforced
- evidence (evidence): scripts/ci/workflow-policy.mjs:21, scripts/ci/workflow-policy.mjs:80

### ci-required-aggregate

- what: Check tổng hợp `CI required` (job `required`, `if: always()`) chỉ xanh khi `quality`, `test`, `docker` đều success; tên job được policy khóa cứng.
- where: ci.yml job `required`; policy trong workflow-policy.mjs
- blocking: true
- status: enforced
- notes: Việc check này có thực sự chặn merge hay không phụ thuộc branch protection/ruleset của GitHub, không thấy trong repo.
- evidence (evidence): .github/workflows/ci.yml:167, .github/workflows/ci.yml:168, scripts/ci/workflow-policy.mjs:90

### ci-bypass-ban

- what: Không có đường bypass trong GitHub CI: local.mjs từ chối `--bypass`, LITMATCH_CI_BYPASS, CI_BYPASS khi CI/GITHUB_ACTIONS bật; policy cấm các từ khóa này trong ci.yml.
- where: scripts/ci/local.mjs và workflow-policy.mjs
- blocking: true
- status: enforced
- evidence (evidence): scripts/ci/local.mjs:87, scripts/ci/workflow-policy.mjs:118, scripts/ci/local.test.mjs:167

### test-frontend

- what: Vitest cho admin và web (jsdom) và api-client (node); chạy trong agent:verify frontend (lint + test + build theo project) với `--skip-nx-cache`.
- where: ci.yml `test` → stage frontend-verify
- blocking: true
- status: enforced
- evidence (evidence): scripts/agent/verify.mjs:33, apps/web/vitest.config.mts:15, libs/api-client/vitest.config.mts:13, scripts/ci/local.mjs:542

### test-backend-unit-integration

- what: Jest unit + integration cho core-api và các lib có PostgreSQL/Redis thật (`INTEGRATION_DB_URL`, `maxWorkers: 1` vì các suite reset chung schema); test không dùng cache Nx.
- where: ci.yml `test` → stage unit-integration-coverage
- blocking: true
- status: enforced
- evidence (evidence): scripts/ci/local.mjs:559, apps/core-api/jest.config.cts:24, nx.json:32

### test-signaling-redis-isolated

- what: Test signaling-gateway chạy riêng, có Redis thật, vì suite lease dùng TTL tối thiểu và tranh CPU sẽ giả lập replica chết.
- where: ci.yml `test` → stage signaling-test
- blocking: true
- status: enforced
- evidence (evidence): scripts/ci/local.mjs:551, docs/runbooks/quality-gates.md:86

### e2e

- what: E2E: core-api-e2e (Jest, cần migration + build core-api), signaling-gateway-e2e (Jest) và web (Playwright, tự dựng core-api riêng trên cổng 3011); chạy tuần tự `--parallel=1`, không cache; E2E không có test thì fail.
- where: ci.yml `test` → stage e2e
- blocking: true
- status: enforced
- evidence (evidence): scripts/ci/local.mjs:578, scripts/agent/guard-core.mjs:110, apps/core-api-e2e/project.json:13, apps/web/playwright.config.mts:10

### coverage-core-api

- what: Ngưỡng coverage kiểu ratchet cho service layer của core-api (statements 84, branches 78, functions 74, lines 85) trên `src/modules/**/*.service.ts`; chỉ được enforce khi chạy `--coverage`.
- where: ci.yml `test` → stage unit-integration-coverage (có `--coverage`)
- blocking: true
- status: enforced
- evidence (evidence): apps/core-api/jest.config.cts:33, apps/core-api/jest.config.cts:31, docs/05-coding-standards.md:120

### coverage-frontend

- what: admin (79/68/75/80), web (69/57/59/72) và api-client (67/52/63/73) có ngưỡng coverage Vitest, nhưng stage test có `--coverage` loại trừ đúng ba project này và `agent:verify frontend` chạy `test` không kèm `--coverage` nên ngưỡng không được enforce trong CI.
- where: apps/admin/vite.config.mts, apps/web/vitest.config.mts, libs/api-client/vitest.config.mts; local.mjs `--exclude=admin,web,api-client,signaling-gateway`
- blocking: false
- status: configured-not-run
- notes: Kết luận theo mã (không thấy cờ --coverage cho ba project này); signaling-gateway có thu coverage nhưng jest.config.cts không khai ngưỡng.
- evidence (evidence): apps/admin/vite.config.mts:48, apps/web/vitest.config.mts:33, libs/api-client/vitest.config.mts:22, scripts/ci/local.mjs:566

### openapi-check

- what: Emit OpenAPI từ core-api vào thư mục tạm, sinh lại client rồi so từng byte với openapi/core-api.json và libs/api-client/src/generated/core-api.ts; lệch thì báo `OpenAPI contract drift` và fail.
- where: ci.yml `test` → `agent:verify frontend` (scope có openapi: true); profile prepush; `pnpm openapi:check`
- blocking: true
- status: enforced
- evidence (evidence): package.json:54, scripts/agent/openapi-check.mjs:59, scripts/agent/verify.mjs:35

### browser-bundle-audit

- what: Sau build frontend: bundle admin/web không được chứa `class-validator` hay `@nestjs/`, CSS không còn directive Tailwind chưa compile, entry script của admin gzip tối đa 180 KiB.
- where: agent:verify frontend (tier full) trong ci.yml `test`
- blocking: true
- status: enforced
- evidence (evidence): scripts/agent/verify.mjs:266, scripts/agent/verify.mjs:299

### docker-image-build

- what: Build 4 image (core-api, signaling-gateway, web, edge) từ Dockerfile production; lỗi build là fail job.
- where: ci.yml `docker` → stages docker-image-*
- blocking: true
- status: enforced
- evidence (evidence): scripts/ci/local.mjs:815, scripts/ci/local.mjs:827

### container-runtime-smoke

- what: Chạy core-api, signaling-gateway, web trong container NODE_ENV=production và đòi /health/ready (core, signaling) và `/` (web) trả 2xx trong 30 giây.
- where: ci.yml `docker` → stage docker-smoke-start + health-wait
- blocking: true
- status: enforced
- evidence (evidence): scripts/ci/local.mjs:697, scripts/ci/local.mjs:740, scripts/ci/local.mjs:939

### caddy-config-validate

- what: `caddy validate` Caddyfile ngay trong image edge vừa build (không chạy edge thật, chưa xin TLS).
- where: ci.yml `docker` → stage edge-validate
- blocking: true
- status: enforced
- evidence (evidence): scripts/ci/local.mjs:924, scripts/ci/local.mjs:934

### production-profile-contract

- what: Hợp đồng profile production: compose có đủ 8 service, biến bắt buộc dạng `:?required`, OBSERVABILITY_REQUIRED=true, 4 Dockerfile runtime có HEALTHCHECK đúng endpoint, K8s probe /health/live + /health/ready, >=2 replica cho core-api và signaling-gateway, media-server 1 replica Recreate hostNetwork.
- where: `pnpm release:profile-check`; chạy gián tiếp trong CI qua `agent:test` (production-profile.test.mjs), trong `release:preflight` và `agent:verify infra`
- blocking: true
- status: enforced
- evidence (evidence): scripts/release/production-profile.test.mjs:7, scripts/release/production-profile.mjs:54, scripts/release/production-profile.mjs:145

### docs-check

- what: `pnpm docs:check`: chạy test scripts/docs và `generate.mjs --check` để chắc báo cáo sinh từ feature-registry.json (md + docx) không cũ. Không nằm trong workflow hay hook nào; chỉ được yêu cầu bởi scope `docs` và `agents` của context-map.
- where: package.json; .agents/context-map.json (scope docs, agents)
- blocking: false
- status: manual
- evidence (evidence): package.json:36, .agents/context-map.json:57

### gitleaks-secret-scan

- what: Gitleaks 8.30.1 đã được ghim (sha256) trong security-tools.mjs, có .gitleaks.toml và baseline, nhưng không có workflow hay profile nào chạy nó.
- where: Không chạy ở đâu: profile `security` bị vô hiệu
- blocking: false
- status: configured-not-run
- evidence (evidence): scripts/ci/security-tools.mjs:26, .gitleaks.toml:1, scripts/ci/local.mjs:1093, docs/14-rule-enforcement-matrix.md:26

### trivy-scan

- what: Trivy 0.72.0 được ghim và có .trivyignore.yaml (KSV-0109, KSV-0009, DS-0002) nhưng không có lần quét filesystem/image nào trong CI hay profile local.
- where: Không chạy ở đâu
- blocking: false
- status: configured-not-run
- evidence (evidence): scripts/ci/security-tools.mjs:42, .trivyignore.yaml:1, docs/14-rule-enforcement-matrix.md:26

### codeql

- what: Không có workflow CodeQL; policy chỉ kiểm tra workflow bảo mật nếu file security.yml tồn tại và hiện test xác nhận repo hợp lệ khi thiếu nó.
- where: Không có (workflow Security từng tồn tại đã bị xóa khỏi lịch sử gần đây, theo git log)
- blocking: false
- status: absent
- notes: Có thể bật code scanning "default setup" trong cài đặt GitHub mà không có file workflow; không thấy được từ repo.
- evidence (evidence): docs/14-rule-enforcement-matrix.md:26, scripts/ci/workflow-policy.test.mjs:69

### sbom

- what: Không có bước sinh SBOM hay attestation image trong workflow, script hay Dockerfile.
- where: Không có
- blocking: false
- status: absent
- notes: Chỉ chứng minh được bằng tìm kiếm toàn repo (không có kết quả cho sbom/syft/cyclonedx), không có dòng nào để trích.
- evidence (evidence): docs/14-rule-enforcement-matrix.md:26

### dependency-review

- what: Policy chỉ yêu cầu action dependency-review pinned SHA và `fail-on-severity: high` khi tồn tại security.yml; file đó không có nên PR thêm dependency có lỗ hổng không bị chặn bởi workflow nào.
- where: workflow-policy.mjs (nhánh có điều kiện)
- blocking: false
- status: absent
- evidence (evidence): scripts/ci/workflow-policy.mjs:198, scripts/ci/workflow-policy.mjs:205

### dependency-audit

- what: `pnpm audit` không được gọi trong workflow, hook hay script; ma trận enforcement ghi audit cần review tay.
- where: Không có (thủ công)
- blocking: false
- status: manual
- evidence (evidence): docs/14-rule-enforcement-matrix.md:26

### dependabot

- what: Dependabot khai 7 mục (npm, github-actions, devcontainers, docker-compose, docker x3 thư mục) lịch hàng tuần nhưng mọi mục đặt `open-pull-requests-limit: 0`, tức không mở PR cập nhật phiên bản.
- where: .github/dependabot.yml
- blocking: false
- status: configured-not-run
- notes: Dependabot security updates là tùy chọn riêng trong cài đặt repo, không thấy từ file này.
- evidence (evidence): .github/dependabot.yml:10, .github/dependabot.yml:3

### commit-message-format

- what: Quy ước `<type>(<scope>): ...` ghi là bắt buộc trong docs/15 nhưng không có hook commit-msg hay commitlint.
- where: docs/15-commit-guidelines.md (quy ước, không có máy kiểm)
- blocking: false
- status: manual
- evidence (evidence): docs/15-commit-guidelines.md:9

### review-module-verify

- what: Thay đổi business nhạy cảm (Economy/Matching/Calling/Gift/Party/Feed/Safety) phải chạy `review-module verify`, dán bảng giả định vào PR; là gate thủ công do reviewer ký nhận.
- where: AGENTS.md invariant 3; .github/PULL_REQUEST_TEMPLATE.md
- blocking: false
- status: manual
- evidence (evidence): AGENTS.md:14, .github/PULL_REQUEST_TEMPLATE.md:22

### codeowners-review

- what: CODEOWNERS giao `*` và các đường nhạy cảm (workflows, migrations, economy, signaling, observability, reliability) cho một owner; việc bắt buộc code-owner review phụ thuộc cài đặt GitHub.
- where: .github/CODEOWNERS
- blocking: unknown
- status: configured-enforcement-unknown
- evidence (evidence): .github/CODEOWNERS:2, .github/CODEOWNERS:6

### hook-pre-commit

- what: Prettier ghi đè trên file staged rồi `agent:check --staged`; bypass bằng LITMATCH_CI_BYPASS/CI_BYPASS.
- where: .husky/pre-commit
- blocking: true
- status: enforced-local-only
- notes: Chỉ chặn trên máy dev đã chạy `pnpm install` (script prepare của Husky) và không đặt bypass.
- evidence (evidence): .husky/pre-commit:26, .husky/pre-commit:27

### hook-pre-push

- what: `pnpm ci:preflight` (clean quality, test/build/E2E, docker smoke trên Linux) trước mỗi push; bypass bằng LITMATCH_CI_BYPASS/CI_BYPASS.
- where: .husky/pre-push
- blocking: true
- status: enforced-local-only
- evidence (evidence): .husky/pre-push:36, package.json:70

### hosted-release-gate

- what: Chỉ deploy khi `HOSTED_RELEASE_ENABLED == true` và run CI push của main kết thúc success; dispatch phải chứng minh SHA đầy đủ có run `ci.yml` push thành công trên main.
- where: hosted-release.yml job `deploy`; policy trong workflow-policy.mjs
- blocking: true
- status: enforced
- evidence (evidence): .github/workflows/hosted-release.yml:26, .github/workflows/hosted-release.yml:90, scripts/ci/workflow-policy.mjs:191

### public-smoke

- what: Sau deploy: 4 URL công khai (api và signaling `/health/ready`, web `/`, admin `/login`) phải 2xx trong 30 lần thử cách 10 giây; fail thì job `deploy` đỏ (sau khi đã deploy).
- where: hosted-release.yml bước cuối, scripts/release/public-smoke.mjs
- blocking: true
- status: enforced
- evidence (evidence): .github/workflows/hosted-release.yml:104, scripts/release/public-smoke.mjs:6, scripts/release/public-smoke.mjs:7

### reliability-production-gate

- what: `pnpm reliability:production-gate` đòi 3 file evidence (loadtest <= 30 ngày, game-day <= 90 ngày, slo-dashboard <= 30 ngày) có status pass, gitSha, runUrl, owner và đủ danh sách check. Không workflow nào gọi lệnh này.
- where: scripts/reliability/verify-evidence.mjs; docs/runbooks/reliability-slo-and-evidence.md
- blocking: false
- status: manual
- evidence (evidence): package.json:27, scripts/reliability/verify-evidence.mjs:19, docs/runbooks/reliability-slo-and-evidence.md:5

### staging-load-test

- what: Workflow thủ công chạy k6 thật trên GitHub Environment `staging` (ticket + SHA bắt buộc, URL phải mang tên staging) và upload evidence 90 ngày; không nằm trong chuỗi release.
- where: .github/workflows/staging-reliability.yml
- blocking: false
- status: manual
- evidence (evidence): .github/workflows/staging-reliability.yml:37, .github/workflows/staging-reliability.yml:139
