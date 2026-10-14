# CI/CD: pipelines, quality gates, release, images: nxWorkspace

id: system:cicd:nxWorkspace

Written by an AI agent from the code; every file:line quote was checked automatically (exists within ±3 lines) but NO independent verification pass has run yet. Treat claims as strong leads; open the cited file before editing.

- summary: Nx 23 monorepo: target lint/test/build/e2e được plugin suy ra từ config; project.json chỉ khai thêm target đặc thù (migration-*, openapi-emit, cloudflare-*). CI chỉ gọi lint, test, build, e2e và vài target tên cụ thể; không gọi typecheck hay serve.

- defaultBase: main

- cloudAndTui: Nx Cloud không được kết nối (nx.json không có nxCloudId), analytics tắt, TUI tắt.

### plugins

### #1

- plugin: @nx/eslint/plugin
- infers: lint
- evidence (evidence): nx.json:53

### #2

- plugin: @nx/webpack/plugin
- infers: build, serve, preview, serve-static, build-deps, watch-deps (core-api và signaling-gateway tự khai build bằng `webpack-cli build`)
- evidence (evidence): nx.json:59, apps/core-api/project.json:13

### #3

- plugin: @nx/jest/plugin
- infers: test cho mọi project Jest trừ *-e2e; target e2e cho core-api-e2e và signaling-gateway-e2e
- evidence (evidence): nx.json:71, nx.json:113

### #4

- plugin: @nx/vite/plugin
- infers: build, serve, dev, preview, typecheck... cho admin và lib Vite
- evidence (evidence): nx.json:77, nx.json:84

### #5

- plugin: @nx/vitest
- infers: test (chế độ watch, config đặt watch:false nên chạy một lần) và test-ci
- evidence (evidence): nx.json:90, nx.json:94

### #6

- plugin: @nx/next/plugin
- infers: build (next build), dev, start cho apps/web
- evidence (evidence): nx.json:98

### #7

- plugin: @nx/playwright/plugin
- infers: e2e cho apps/web (Playwright)
- evidence (evidence): nx.json:116

### targetDefaults

### cache: true, dependsOn ^build, inputs production

- target: build
- evidence (evidence): nx.json:20

### cache: true

- target: lint
- evidence (evidence): nx.json:23

### cache: true; inputs gồm biến môi trường INTEGRATION_DB_URL và REDIS_URL; core-api và signaling-gate…

- target: test
- evidence (evidence): nx.json:32, apps/core-api/project.json:8

### cache: false

- target: e2e / e2e-ci
- evidence (evidence): nx.json:39, nx.json:42

### explicitTargetsOfNote

### TypeORM CLI qua ts-node với data-source của core-api (`--transaction each` cho run/revert); là nền …

- target: core-api:migration-run | migration-show | migration-revert
- evidence (evidence): apps/core-api/project.json:33, apps/core-api/project.json:48

### Sinh openapi/core-api.json từ code (nền của `pnpm openapi:emit`).

- target: core-api:openapi-emit
- evidence (evidence): apps/core-api/project.json:27

### OpenNext build rồi deploy Worker; dependsOn build.

- target: web:cloudflare-build | cloudflare-deploy
- evidence (evidence): apps/web/project.json:26, apps/web/project.json:36

### `wrangler pages deploy dist/apps/admin` lên project litmatch-admin.

- target: admin:cloudflare-deploy
- evidence (evidence): apps/admin/project.json:16

### E2E phụ thuộc `core-api:migration-run` (và `core-api:build` cho core-api-e2e); web tự khởi core-api…

- target: core-api-e2e:e2e / web:e2e
- evidence (evidence): apps/core-api-e2e/project.json:13, apps/web/project.json:48

### projects

### admin

- path: apps/admin
- tags: type:app; scope:frontend; platform:browser
- explicitTargets: build; cloudflare-deploy
- evidence (evidence): apps/admin/project.json:6

### core-api

- path: apps/core-api
- tags: type:app; scope:core; platform:server
- explicitTargets: test; build; openapi-emit; migration-run; migration-show; migration-revert; serve
- evidence (evidence): apps/core-api/project.json:70

### core-api-e2e

- path: apps/core-api-e2e
- tags: type:app; scope:e2e; platform:server
- explicitTargets: e2e
- evidence (evidence): apps/core-api-e2e/project.json:5

### signaling-gateway

- path: apps/signaling-gateway
- tags: type:app; scope:signaling; platform:server
- explicitTargets: test; build; serve
- evidence (evidence): apps/signaling-gateway/project.json:46

### signaling-gateway-e2e

- path: apps/signaling-gateway-e2e
- tags: type:app; scope:e2e; platform:server
- explicitTargets: e2e
- evidence (evidence): apps/signaling-gateway-e2e/project.json:5

### web

- path: apps/web
- tags: type:app; scope:frontend; platform:browser
- explicitTargets: build; dev; start; cloudflare-build; cloudflare-deploy; e2e; e2e-ci; e2e-ci--e2e/login-and-join-matching-queue.spec.ts; e2e-ci--merge-reports
- evidence (evidence): apps/web/project.json:6

### api-client

- path: libs/api-client
- tags: type:lib; scope:shared; platform:browser
- explicitTargets: build
- evidence (evidence): libs/api-client/project.json:18

### browser-auth

- path: libs/browser-auth
- tags: type:lib; scope:shared; platform:browser
- explicitTargets: build
- evidence (evidence): libs/browser-auth/project.json:17

### common-dtos

- path: libs/common-dtos
- tags: type:lib; scope:shared; platform:browser; platform:server
- explicitTargets: build
- evidence (evidence): libs/common-dtos/project.json:18

### common-exceptions

- path: libs/common-exceptions
- tags: type:lib; scope:shared; platform:server
- explicitTargets: build
- evidence (evidence): libs/common-exceptions/project.json:18

### config-validator

- path: libs/config-validator
- tags: type:lib; scope:shared; platform:server
- explicitTargets: build
- evidence (evidence): libs/config-validator/project.json:18

### e2e-support

- path: libs/e2e-support
- tags: type:lib; scope:shared; platform:server
- explicitTargets: build
- evidence (evidence): libs/e2e-support/project.json:17

### logger

- path: libs/logger
- tags: type:lib; scope:shared; platform:server
- explicitTargets: build
- evidence (evidence): libs/logger/project.json:18

### observability

- path: libs/observability
- tags: type:lib; scope:shared; platform:server
- explicitTargets: build
- evidence (evidence): libs/observability/project.json:18

- boundaryRules: Mỗi project phải có đúng một type:*, một scope:*, >=1 platform:*; ESLint chặn app → app, lib → app, frontend → lib không có platform:browser, backend → lib không có platform:server.

### boundaryEvidence

### #1

- file: eslint.config.mjs
- line: 29
- quote: { sourceTag: 'type:app', onlyDependOnLibsWithTags: ['type:lib'] },

### #2

- file: eslint.config.mjs
- line: 43
- quote: sourceTag: 'scope:frontend',

### #3

- file: scripts/agent/project-tags.mjs
- line: 16
- quote: errors.push(`${projectPath}: cần đúng một tag type:*.`);

- ciUsage: CI và hook chỉ gọi các target lint, test, build, e2e (qua run-many hoặc affected), cộng `nx run core-api:migration-run`, `nx run core-api:openapi-emit` (qua openapi:check/sync) và `cloudflare-deploy`; target typecheck không được gọi.

### ciUsageEvidence

### #1

- file: package.json
- line: 29
- quote: "build": "nx run-many -t build --outputStyle=static"

### #2

- file: package.json
- line: 60
- quote: "db:migrate": "nx run core-api:migration-run"

### #3

- file: .github/workflows/hosted-release.yml
- line: 97
- quote: run: pnpm nx run-many -t cloudflare-deploy -p web,admin --parallel=2 --skip-nx-cache --outputStyle=static

### evidence

### #1

- file: nx.json
- line: 3
- quote: "defaultBase": "main",

### #2

- file: nx.json
- line: 122
- quote: "analytics": false,

### #3

- file: nx.json
- line: 123
- quote: "tui": {
