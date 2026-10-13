# Issues in area windows-toolchain

id: issues:windows-toolchain

### P036: Windows: hook/runner không chạy được pnpm (.cmd: 127 / EINVAL)

status: fixed; tier: A; date: 2026-08-14; confidence: high

- problem: git push bị hook pre-push chặn: 'pnpm: command not found' (code 127), sau đó spawn EINVAL khi chạy pnpm.cmd.
- root cause: Git Bash/Husky nhận PATH cũ thiếu %APPDATA%/npm; run-stage.mjs spawn trực tiếp pnpm.cmd không qua shell trên Windows.
- resolution: Hook pre-commit/pre-push tự thêm %APPDATA%/npm và Program Files/nodejs vào PATH; scripts/ci/run-stage.mjs dùng shell cho .cmd/.bat (test 5/5). Commit 3f231ca, eba5763.
- evidence: codex-thread 019ffee6, commit 3f231ca, commit eba5763, codex-thread 019ffec1

### P037: Windows/Docker: CRLF + symlink thành file thường làm format/agent:check đỏ

status: fixed; tier: A; date: 2026-08-14; confidence: high

- problem: Trên Windows pnpm format:check báo ~1.428 file và 9 alias symlink (CLAUDE.md, SKILL.md) bị checkout thành file thường, trong khi CI Linux chỉ báo 2 file.
- root cause: Checkout Windows lẫn CRLF/LF, chưa có rule eol; symlink bị materialize thành file làm Prettier đòi newline và agent:check fail.
- resolution: Thêm .gitattributes ép LF, .prettierignore loại alias symlink, bật core.symlinks; adapter-parity chỉ chấp nhận file materialized khi nội dung khớp target trong index. Commit f717299, 86e1a45.
- evidence: codex-thread 019ffee6, commit f717299, commit 86e1a45, codex-thread 019ffec1

### P038: Windows: Jest không chạy (thiếu VC++ runtime, resolver native)

status: fixed; tier: A; date: 2026-08-14; confidence: medium

- problem: Jest trên Windows/pnpm không tìm được preset/ts-jest/resolver; core-api và signaling jest không chạy; thiếu vcruntime140.dll.
- root cause: preset resolve tương đối từ app root, <rootDir> của resolver đổi khi spread; unrs-resolver binding native ERR_DLOPEN_FAILED do thiếu VC++ runtime.
- resolution: Nạp preset bằng require, override resolver về scripts/jest/resolver.cjs (fallback Node/TS), cài VC++ Redistributable; Jest E2E config chuyển sang ESM import + fileURLToPath; signaling 8 suite PASS.
- evidence: codex-thread 019ffec1, file scripts/jest/resolver.cjs, commit c261afe, codex-thread 019fff81

### P039: Pre-push trên Windows đỏ giả do giả định POSIX / công cụ Linux-only

status: fixed; tier: A; date: 2026-08-14; confidence: high

- problem: Local gate trên Windows đỏ giả: security-tools 16 test, markdown-links, pre-tool-guard, module-boundaries 14 test fail do path/platform.
- root cause: Test giả định path POSIX/$HOME, URL.pathname thành /C:/..., snapshot boundary dùng dấu \ thay vì /.
- resolution: Sửa test dùng path separator/homedir đa nền tảng, chuẩn hóa POSIX trong module-boundaries.spec.ts; dry-run không đòi Linux/Docker. Trước đó push --no-verify có kiểm soát.
- evidence: codex-thread 019ffec1, commit 0ffd61f, file apps/core-api/src/arch/module-boundaries.spec.ts, codex-thread 01a0015f

### P060: tsconfig baseUrl deprecated → mất alias lúc runtime

status: fixed; tier: A; date: 2026-08-14; confidence: medium

- problem: VS Code đỏ vì cảnh báo baseUrl/moduleResolution 'node' deprecated; sau khi bỏ baseUrl, openapi:check không resolve @litmatch/logger.
- root cause: tsconfig.base.json dùng baseUrl deprecated; tsconfig-paths/register cũ không rebase paths từ file extends nên runtime mất alias sau khi bỏ baseUrl.
- resolution: Xoá baseUrl khỏi tsconfig.base.json, moduleResolution thành node10 ở core-api/signaling-gateway; thêm scripts/register-tsconfig-paths.cjs và sửa apps/core-api/project.json, openapi-check.mjs. OpenAPI check PASS.
- evidence: codex-thread 019fff81, file scripts/register-tsconfig-paths.cjs, file tsconfig.base.json

### P072: Windows: Nx build lỗi 1314 + runner timeout (exit 124)

status: mitigated; tier: B; date: 2026-08-20; confidence: low

- problem: Trên Windows: quality gate/nx build/test bị runner timeout không log (exit 124), Nx build báo os error 1314, next build ngoài Nx thiếu NEXT_PUBLIC_*, next-env.d.ts bị sinh đổi, lệnh cài package chạy nhầm thư mục cha.
- root cause: Giới hạn watchdog tool, dev server giữ .next, build direct thiếu env, workdir lệch; không phải lỗi logic.
- resolution: Chạy tách target, tắt dev server rồi build lại, cấp env NEXT_PUBLIC_* local, hoàn tác next-env.d.ts, gỡ package cài nhầm ở thư mục cha.
- evidence: codex-thread 01a01990, codex-thread 01a01990

### P077: Test hard-kill của run-stage lỗi trên Windows (exit 124)

status: open; tier: A; date: 2026-09-03; confidence: high

- problem: pnpm agent:verify media FAIL trên Windows: test hard-kill trong scripts/ci/run-stage.test.mjs kỳ vọng exit 124 nhưng nhận rỗng (2 test lần đầu, 3 test khi retry).
- root cause: Test harness chung không portable trên Windows (cơ chế kill/timeout); không phải lỗi LiveKit.
- resolution: Chỉ chẩn đoán là finding độ ổn định CI portability; chưa thấy sửa trong bằng chứng.
- evidence: codex-thread 01a06739, file scripts/ci/run-stage.test.mjs

### P092: Windows thiếu zip/unzip nên docs:check không chạy

status: mitigated; tier: A; date: 2026-10-09; confidence: high

- problem: Máy Windows thiếu zip/unzip nên pnpm docs:check/generate DOCX không chạy; unzip Git không đọc entry có bracket.
- root cause: Generator dựa vào zip/unzip không có trên Windows.
- resolution: Dùng shim tạm (.NET ZipArchive tạo, 7-Zip kiểm tra) trong temp/PATH của lệnh, không sửa tooling repo; pnpm docs:check PASS.
- evidence: commit 50f7dc9, doc docs/plans/2026-10-08-capacity-and-scaling-plan.md
