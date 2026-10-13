# Issues in area ci-cd

id: issues:ci-cd

### P002: CI main đỏ ngay từ commit đầu: "Multiple versions of pnpm specified"

status: fixed; tier: A; date: 2026-07-11; confidence: medium

- problem: Ba run CI đầu tiên trên main (11-12/07) fail ở job verify với lỗi "Multiple versions of pnpm specified".
- root cause: Phiên bản pnpm bị khai báo ở hai nơi (packageManager trong package.json và bước pnpm/action-setup) nên action từ chối chạy.
- resolution: Workflow CI được sửa trong các commit CI ngày 12-13/07 (không xác định đúng commit); lỗi không lặp lại sau 12/07.
- evidence: ci-run 29169060725, ci-run 29169210862, ci-run 29179402029

### P007: CI hosted lệch CI local (runner sạch)

status: fixed; tier: A; date: 2026-07-13; confidence: high

- problem: CI hosted lệch local: @eslint/js không resolve trên runner sạch, Next build thiếu 3 NEXT_PUBLIC_*, gitleaks chặn secret giả lịch sử, Docker job bị skipped, actionlint bắt ShellCheck mà local bỏ sót, action dùng Node 20.
- root cause: @eslint/js chỉ có qua dependency bắc cầu; local có cache/env nên pass; local gate không cài ShellCheck.
- resolution: Thêm pnpm ci:preflight và hook pre-push chạy Node 22 container sạch; .gitleaks-baseline theo fingerprint; local tự cài ShellCheck pin SHA-256; nâng pnpm/action-setup và upload-artifact lên bản Node 24; workflow-policy.mjs có test.
- evidence: pr #16, pr #19, commit d8676be, commit f7186b6

### P022: CVE ở runtime image mà pre-push local bỏ sót

status: fixed; tier: A; date: 2026-07-22; confidence: medium

- problem: CVE ở runtime image (Web/Edge, Go cũ, toolchain thừa) bị GitHub phát hiện trong khi pre-push local vẫn xanh vì bỏ sót image.
- root cause: Pre-push local không build/scan đủ bốn runtime image; Edge build bằng Go cũ, Web image giữ package manager.
- resolution: Pre-push gọi pnpm ci:preflight (build+scan đủ 4 image); Web xoá package manager; Edge build Caddy bằng Go đã vá, chạy nobody; pin bản vá trong pnpm-workspace.yaml; pnpm audit high PASS.
- evidence: doc docs/plans/2026-07-22-hosted-free-release-review.md

### P023: Integration test race do schema/Redis dùng chung

status: fixed; tier: A; date: 2026-07-22; confidence: medium

- problem: Integration test core-api race trên schema/Redis dùng chung khi nhiều Jest worker reset tài nguyên cùng lúc.
- root cause: Nhiều worker Jest chạy song song cùng schema Postgres và Redis.
- resolution: jest.config.cts đặt maxWorkers: 1; hai lần chạy liên tiếp PASS 65 suite/716 test.
- evidence: doc docs/plans/2026-07-22-hosted-free-release-review.md

### P024: Build web OpenNext/ESM hỏng, phải sửa 3 lần

status: fixed; tier: A; date: 2026-07-22; confidence: medium

- problem: Build web OpenNext/ESM cho deploy edge (Cloudflare) hỏng, phải sửa lặp lại ba lần liên tiếp (#32, #36, #37).
- root cause: Chưa rõ chi tiết (body commit rỗng): chỉ thấy đổi package.json/pnpm-lock và cấu hình build edge; commit 82dd0f7 còn lỡ commit cache .wrangler.
- resolution: Ghim/nâng dependency (package.json, pnpm-lock) và chỉnh Dockerfile.edge / apps/web/project.json qua ba lần sửa liên tiếp.
- evidence: commit 81864a3, commit 4b29328, commit 82dd0f7

### P027: CI quality-result đỏ do comment/param env trong workflow

status: fixed; tier: A; date: 2026-07-24; confidence: medium

- problem: Job quality-result của CI fail vì comment/param env trong workflow.
- root cause: Cú pháp/giá trị env trong ci.yml làm bước tổng hợp kết quả quality fail.
- resolution: Gỡ comment param env (#40) rồi chỉnh bước tổng hợp kết quả quality trong ci.yml (#41).
- evidence: commit 8b28c91, commit 4e53ca1

### P029: Nhiều đường bypass gate CI (hook local / GitHub / hosted lệch nhau)

status: fixed; tier: B; date: 2026-07-30; confidence: high

- problem: Hook local, GitHub Actions và hosted release dùng gate khác nhau, còn đường bypass; release không bắt buộc ci.yml push run thành công trên main.
- root cause: Contract CI không thống nhất, gate chạy được ở chế độ không fail-closed.
- resolution: Hợp nhất 1 contract fail-closed, bỏ bypass, release yêu cầu run ci.yml thành công đúng commit, gate check-only deterministic (NX_TUI=false, frozen lockfile), cô lập Nx state; thêm test workflow-policy.
- evidence: commit ab57f9b, pr #50

### P035: Generated client lệch OpenAPI làm CI đỏ (#188)

status: fixed; tier: A; date: 2026-08-14; confidence: high

- problem: CI PR #66 job Test, build and E2E fail (kéo CI required fail): openapi:check phát hiện generated client lệch OpenAPI.
- root cause: libs/api-client/src/generated/core-api.ts khai useDiamond/genderPreference optional trong khi OpenAPI yêu cầu bắt buộc.
- resolution: Regenerate từ openapi/core-api.json (đổi 2 dòng), commit 0ffd61f; nâng ci:preflight/pre-push lên profile all để bắt lỗi này trước GitHub.
- evidence: codex-thread 019ffec1, commit 0ffd61f, pr #66, codex-thread 019ffee6

### P040: Watchdog CI quá thô cắt gate giữa chừng (LRN-008)

status: fixed; tier: B; date: 2026-08-14; confidence: high

- problem: Wrapper 45 giây chung cắt gate CI tổng hợp trước khi thấy lỗi thật, chẩn đoán mơ hồ và tiến trình con còn chạy.
- root cause: Chính sách timeout cho probe ngắn bị áp cho lệnh install/test/build nhiều stage.
- resolution: stage-policy.mjs đặt timeout theo task, run-stage.mjs kill cả process group, in marker TIMED_OUT; có test và runbook quality-gates.
- evidence: commit 8ba9ef9, doc docs/reference/lessons-registry.md, commit 1ba5b54, file scripts/ci/stage-policy.mjs

### P041: Timeout cứng quá chặt / chạy song song làm test flaky

status: fixed; tier: A; date: 2026-08-14; confidence: high

- problem: Pre-push fail lặp do timeout cứng: web friend-picker.spec 5s trong Docker, 2 test run-stage deadline 2s (127 pass/2 fail), frontend test 5s khi nhiều Vitest worker.
- root cause: Deadline test quá sát trên Windows bind mount/Docker chậm; hai container preflight stale chạy đồng thời trên cùng workspace.
- resolution: friend-picker timeout 15s (bacd2f3); run-stage test deadline 10s (5beb6f8); Vitest timeout 15s khi CI=true ở admin/web vite config; dọn container stale.
- evidence: commit bacd2f3, commit 5beb6f8, codex-thread 019ffec1, commit a91d73d

### P042: Test gia hạn lease bị đói timer khi chạy cạnh pool lớn (LRN-009)

status: fixed; tier: A; date: 2026-08-14; confidence: high

- problem: Test gia hạn lease của signaling pass riêng nhưng fail khi chạy cạnh pool coverage của Core API vì timer starvation giống replica chết.
- root cause: Scheduler CI xếp test Redis TTL nhạy thời gian chung với Jest pool lớn.
- resolution: Local/GitHub CI chạy signaling-gateway:test ở stage Nx cô lập trước phần test song song; TTL tối thiểu production và hành vi fail-closed giữ nguyên.
- evidence: doc docs/reference/lessons-registry.md, file scripts/ci/local.mjs, doc docs/reference/project-handoff.md, commit 60269c6

### P043: Nx cache bật cho test có state → có thể trả kết quả cũ

status: fixed; tier: B; date: 2026-08-14; confidence: medium

- problem: Nx cache đang bật cho test tích hợp có state (core-api, signaling Redis) và E2E, có thể trả kết quả cũ dù DB/Redis đổi.
- root cause: Nx hash không bao gồm trạng thái DB/Redis runtime; target e2e/test stateful vẫn cache mặc định.
- resolution: Tắt cache cho core-api/signaling-gateway test, toàn bộ E2E và target web atomized; thêm LOCAL_CI_STRICT_CLEAN và Docker Buildx GHA cache. Commit 79bea23.
- evidence: commit 79bea23, file nx.json

### P044: E2E local: server cũ còn sống, reset DB chồng, thiếu baseUrl

status: fixed; tier: A; date: 2026-08-14; confidence: high

- problem: E2E local fail lẫn lộn: server core-api/signaling cũ còn sống, Core E2E reset DB khi Web E2E đang đăng nhập; web E2E thiếu baseUrl alias và lệnh PORT không chạy trên Windows.
- root cause: Teardown dùng process.kill(-pid) kiểu Unix; --parallel=2 chung DB; tsconfig.base mất baseUrl; PORT=... không tương thích Windows.
- resolution: isolated-node-server.ts dùng taskkill /T /F; E2E --parallel=1; khôi phục baseUrl trong tsconfig.base.json; sửa playwright.config.mts. Signaling 2/2, core 12/12, web 1/1 PASS.
- evidence: codex-thread 019ffec1, commit c261afe, file libs/e2e-support/src/lib/isolated-node-server.ts

### P045: Lint: config E2E import vi phạm ranh giới Nx

status: fixed; tier: A; date: 2026-08-14; confidence: high

- problem: Lint fail 2 lỗi ở core-api-e2e/jest.config.ts và signaling-gateway-e2e/jest.config.ts.
- root cause: Nx cấm import relative/absolute từ config E2E.
- resolution: Sửa hai config theo pattern preset canonical, thêm ngoại lệ eslint đúng dòng chỉ cho config Jest; lint từng project PASS.
- evidence: codex-thread 019ffec1, file apps/core-api-e2e/jest.config.ts

### P053: Conflict nhánh matching với main

status: fixed; tier: B; date: 2026-08-14; confidence: high

- problem: Branch feat/matching-voice-friend-monetization conflict với main tại scripts/ci/local.test.mjs.
- root cause: Cả hai nhánh cùng thêm test 'Economy E2E fixtures'; feature đổi thêm contract prepush.
- resolution: Merge origin/main, giữ một bản test Economy và toàn bộ test prepush/database của feature; merge commit 2d1fa02, chưa push.
- evidence: codex-thread 019ffeb6, commit 2d1fa02

### P054: Hook pre-push chỉ chạy affected nên lỗi lọt lên CI

status: fixed; tier: B; date: 2026-08-14; confidence: high

- problem: Local hook pre-push chỉ chạy affected (prepush) nên lỗi vẫn lọt lên CI GitHub, user thấy CI đỏ nhiều.
- root cause: ci:preflight dùng profile affected, không phủ quality toàn repo/test/build/E2E như CI.
- resolution: Đổi ci:preflight sang profile ci trong package.json, cập nhật scripts/ci/local.test.mjs, README và docs quality-gates; giữ Docker smoke ở GitHub vì Windows không ổn định.
- evidence: codex-thread 019ffebd, file package.json

### P055: Chưa bắt buộc required checks: PR vẫn merge được khi CI đỏ

status: open; tier: B; date: 2026-08-14; confidence: medium

- problem: PR #66 vẫn có thể merge khi CI đỏ; user hỏi cách bắt buộc phải pass hết mới merge.
- root cause: Chưa có bằng chứng branch protection được bật; job con skipped có thể bị coi hợp lệ nếu require trực tiếp.
- resolution: Khuyến nghị bật branch protection main yêu cầu check 'CI / CI required', up-to-date, không bypass; chưa thấy bằng chứng đã cấu hình.
- evidence: codex-thread 019ffee6, file .github/workflows/ci.yml

### P056: Báo động giả: push nhánh không thấy CI chạy

status: false-alarm; tier: C; date: 2026-08-14; confidence: high

- problem: User thắc mắc git push lên branch fix/matching-payload-400 không thấy CI chạy.
- root cause: Không phải lỗi: ci.yml chỉ chạy push trên main, branch tính năng chạy qua pull_request; PR #66 đã có CI run #188.
- resolution: Giải thích; không đổi workflow, giữ mở PR sớm.
- evidence: codex-thread 019ffebd, file .github/workflows/ci.yml

### P062: Prettier lỗi bảng markdown → CI #189 đỏ

status: fixed; tier: A; date: 2026-08-14; confidence: high

- problem: CI #189 đỏ ở Format and lint: Prettier báo README.md và docs/runbooks/quality-gates.md; test/docker bị skipped, CI required fail.
- root cause: Hai bảng Markdown vừa sửa không đúng format Prettier; commit trước không chạy hook vì máy chưa pnpm install nên Husky chưa đăng ký.
- resolution: Format lại hai file, commit d794cc0; cài Node 22 + pnpm, pnpm install để Husky bật core.hooksPath=.husky/_ cho pre-commit lint-staged.
- evidence: codex-thread 019ffee6, commit d794cc0

### P063: Test frontend (admin/web) timeout lặp lại

status: open; tier: A; date: 2026-08-14; confidence: medium

- problem: Full frontend gate fail: admin:test 4 test và web help/page.spec.tsx 1 timeout; nhiều gate bị runner timeout 124.
- root cause: Nghi flaky/race khi Nx run-many chạy song song (fast gate chạy lại toàn bộ test PASS 82 files/302 tests); chưa chứng minh chắc.
- resolution: Chưa sửa test; thử lại NX_PARALLEL=1 bị ngắt, không kết luận được. Ghi nhận không liên quan thay đổi.
- evidence: codex-thread 01a0015f, codex-thread 01a06739

### P064: Container smoke Linux-only chạy trên Windows chặn push

status: fixed; tier: A; date: 2026-08-19; confidence: high

- problem: git push bị husky pre-push chặn: ci-local all fail ở container smoke dù test/E2E đã PASS.
- root cause: Container smoke dùng Docker host networking, chỉ hỗ trợ Linux/WSL, nên fail trên Windows native.
- resolution: scripts/ci/local.mjs: profile all/pre-push trên Windows native skip riêng container smoke (có log); ci:local:docker chạy trực tiếp vẫn fail rõ. Thêm regression test local.test.mjs, cập nhật quality-gates.md; ci:local:all PASS.
- evidence: codex-thread 01a01908, file scripts/ci/local.mjs, file docs/runbooks/quality-gates.md, commit cf17e22

### P069: Dry-run test rò cờ Nx cache làm pre-push fail

status: fixed; tier: A; date: 2026-08-20; confidence: high

- problem: Pre-push fail: local.test.mjs dry-run không khớp /Reuse persistent Nx cache/ dù đã dọn cache Docker.
- root cause: Test thiếu isolation: LOCAL_CI_STRICT_CLEAN/LOCAL_CI_RESET_NX còn bật trong terminal làm dry-run đi nhánh 'Reset Nx'.
- resolution: Loại hai biến khỏi env của helper dry-run trong scripts/ci/local.test.mjs; kiểm chứng với hai biến bật (22/22 PASS), commit hotfix 60269c6 và push thành công.
- evidence: commit 60269c6, file scripts/ci/local.test.mjs, codex-thread 01a01990, commit 60269c6

### P073: Báo động giả: clean container fail ở agent-check không có assertion

status: false-alarm; tier: C; date: 2026-08-20; confidence: medium

- problem: Pre-push clean Linux container fail ở repository-check/agent check, log không có assertion cụ thể.
- root cause: Không tái hiện: replay PASS (~48s); nghi run bị gián đoạn/chậm do bind mount Docker từ Windows hoặc cache, không phải code web-push.
- resolution: Không sửa code; chạy lại profile clean PASS (repository check, 137 agent tests, format, lint 14 project). Lỗi thật xuất hiện sau đó là env leak của test.
- evidence: codex-thread 01a01990

### P079: Push bị GitHub từ chối 403 (sai tài khoản)

status: fixed; tier: A; date: 2026-10-08; confidence: high

- problem: Push bị GitHub từ chối 403: remote xác thực bằng tài khoản manhpv-3661 không có quyền ghi repo ducmanh001/litmatch.
- root cause: gh đang active sai tài khoản; tài khoản owner ducmanh001 đang inactive.
- resolution: Chuyển tài khoản active của gh sang ducmanh001 rồi push thành công, mở PR #75.
- evidence: codex-thread 01a11bf9, pr #75
