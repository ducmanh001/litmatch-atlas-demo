# Issues in area ai-agent-process

id: issues:ai-agent-process

### P003: Commit "harden phase 2A" quá lớn phải revert khỏi main

status: fixed; tier: B; date: 2026-07-11; confidence: low

- problem: Commit lớn 'feat(codex): refactor specs and harden phase 2A' (migration matching, token.service, env validation, economy) vào main rồi bị revert sau 1 ngày.
- root cause: Chưa ro: bằng chứng chỉ có subject revert, không có lý do trong body.
- resolution: Revert toàn bộ phase 2A hardening khỏi main (d09be35).
- evidence: commit 0f51134, commit d09be35

### P004: Adapter CLAUDE.md / skills lệch bản canonical (symlink tay)

status: fixed; tier: B; date: 2026-07-12; confidence: high

- problem: CLAUDE.md và .claude/skills là file văn bản trỏ tay, nội dung lệch bản canonical (AGENTS.md, .agents/skills); mô tả review-module đã trôi thật và phải Read thêm để lấy luật.
- root cause: Adapter là bản sao thủ công nên dễ drift khi canonical đổi.
- resolution: Chuyển thành symlink thật; thêm validateSymlinks() vào agent:check; tách vendor-wording.mjs có test và escape-hatch; ưu tiên $CLAUDE_PROJECT_DIR trong settings. Sau đó 46623e4 hỗ trợ symlink trên Windows.
- evidence: commit b40267b, commit 46623e4

### P016: PASS cục bộ bị hiểu thành PASS toàn repo (LRN-006)

status: mitigated; tier: B; date: 2026-07-18; confidence: medium

- problem: Test trong phạm vi task pass nhưng gate format toàn repo fail vì file có sẵn ngoài phạm vi, nên không thể khẳng định kết quả toàn repo.
- root cause: Gate repo kiểm toàn worktree, không theo quyền sở hữu file của task.
- resolution: Quy tắc: giữ nguyên sửa đổi của người khác, báo va chạm, tách bằng chứng theo phạm vi và toàn repo.
- evidence: doc docs/reference/lessons-registry.md, doc docs/reference/project-handoff.md, codex-thread 019fff93

### P050: Không push được vì môi trường (thiếu gh, Docker/WSL2, hook)

status: mitigated; tier: B; date: 2026-08-14; confidence: medium

- problem: Không push được ban đầu: thiếu gh, chưa login ducmanh001, pre-push security-tools fail, Docker/WSL2 chưa chạy do virtualization tắt; process pre-push/container mồ côi chạy chồng.
- root cause: Máy thiếu công cụ (gh, WSL2/virtualization), terminal giữ PATH cũ; wrapper timeout bỏ lại tiến trình CI stale.
- resolution: Cài gh/Docker, đăng nhập đúng tài khoản chỉ cấu hình local repo, bật Windows feature WSL2; dọn process/container stale; một lần push --no-verify có kiểm soát, sau đó pre-push đầy đủ PASS.
- evidence: codex-thread 019ffec1, commit a3f0f61, codex-thread 01a11bf9, codex-thread 01a11dea

### P051: ci:preflight treo làm mất thay đổi chưa commit

status: mitigated; tier: B; date: 2026-08-14; confidence: medium

- problem: Tiến trình pnpm ci:preflight chạy song song bị treo và làm mất các thay đổi chưa commit (fix button + payload) trong working tree.
- root cause: Preflight chạy nền làm worktree quay về sạch giữa lúc agent làm việc; chạy gate song song gây race cache.
- resolution: Dừng đúng hai PID preflight, khôi phục lại 8 file bằng patch, chỉ chạy test targeted sau đó.
- evidence: codex-thread 01a0015f

### P080: Chạm session limit vì workflow nhiều agent (đốt ~2,9M token)

status: fixed; tier: B; date: 2026-10-08; confidence: high

- problem: Workflow 10 agent song song đốt ~2.9M token trong 16 phút, chạm session limit hai lần (1:50am, 05:57), giết toàn bộ agent không có kết quả; từng mở 3 sub-agent vượt giới hạn 2.
- root cause: Chạy nhiều agent đồng thời, không ghi kết quả từng phần, vượt trần 2 agent của AGENTS.md.
- resolution: Quy tắc mới: trích xuất tất định bằng AST/validator trước, tối đa 2 agent, ghi partial output, dừng khi lỗi limit, sao lưu, publish sớm; lưu memory feedback-keep-work-cheap-and-fast.
- evidence: memory feedback-keep-work-cheap-and-fast.md, claude-session 9353f1cf, claude-session 1f780174

### P095: Bước prune xoá nhầm 15 kết luận hợp lệ (pipeline service-map)

status: fixed; tier: B; date: 2026-10-09; confidence: high

- problem: Bước dọn dữ liệu (validate-localconf --prune) xoá nhầm 15 kết luận hợp lệ sau khi flow-packets3 chia lại packet.
- root cause: Prune chạy trên manifest đã bị flow-packets3.cjs.re-partition, lệch với agent output.
- resolution: Khôi phục từ bản sao lưu; validators dùng packets/localconf-manifest.run1.json và build-all không còn prune localconf.
- evidence: memory project-litmatch-service-map.md, claude-session 1f780174

### P096: Heuristic độ phủ khớp đuôi đường dẫn ngắn → dương tính giả

status: fixed; tier: B; date: 2026-10-09; confidence: high

- problem: Heuristic kiểm độ phủ khớp đuôi đường dẫn ngắn (vd 'end') gây dương tính giả, khai báo phủ sai (có 2 khai báo sai ở sơ đồ cũ).
- root cause: check-covers khớp suffix đường dẫn quá lỏng, không kèm METHOD.
- resolution: check-covers.cjs yêu cầu METHOD + path suffix, có covers-override.json cho 3 ngoại lệ; sửa 2 khai báo sai.
- evidence: memory project-litmatch-service-map.md, claude-session 1f780174

### P097: Sửa script qua shell lồng quote làm hỏng regex; Workflow scriptPath bị từ chối

status: fixed; tier: B; date: 2026-10-09; confidence: medium

- problem: Sửa script build qua shell lồng quote làm hỏng regex; swap-limits cắt luôn hàm renderFindings; Workflow scriptPath bị từ chối; sơ đồ cắt nhãn >3 dòng (64/1007 bước).
- root cause: Dùng sed/node -e với quoting lồng nhau; scriptPath phải là file do tool trả về; renderer giới hạn 3 dòng.
- resolution: Dùng Write/Edit thay shell, chèn lại renderFindings, truyền args.tasks cho Workflow, nâng giới hạn nhãn lên 7 dòng.
- evidence: memory project-litmatch-service-map.md, claude-session 1f780174

### P098: Báo cáo đầu chỉ là smoke test, bỏ sót lỗi profile

status: fixed; tier: B; date: 2026-10-09; confidence: medium

- problem: Báo cáo đầu của agent chỉ là smoke test route nhưng bị hiểu là kiểm thử đầy đủ, nên bỏ sót lỗi profile follow/nhắn tin.
- root cause: Phạm vi test mới là route + vài flow chính, không test từng nút/action.
- resolution: Agent thừa nhận, chạy lại functional sweep 2 tài khoản guest và tìm ra lỗi 404 profile; kết luận lại 'chưa PASS toàn bộ'.
- evidence: codex-thread 01a11dea

### P105: Spec integration liệt kê migration tường minh: migration mới phải đăng ký ở 2 spec

status: fixed; tier: B; date: 2026-10-09; confidence: medium

- problem: Thêm migration mới (videos.storage_cleaned_at) mà không đăng ký vào short-video.integration.spec.ts và demo-short-videos.integration.spec.ts thì integration test không thấy cột mới; undoLastMigration của spec sau phải undo 2 lần.
- root cause: Spec integration của repo liệt kê migration thủ công thay vì tự quét thư mục.
- resolution: Đăng ký migration 1757800000000 vào cả hai spec và sửa số lần undo.
- evidence: memory project-litmatch-multi-pod-readiness-branch.md

### P106: git apply --unidiff-zero đặt sai vị trí hunk chèn thuần

status: fixed; tier: B; date: 2026-10-09; confidence: medium

- problem: Tách commit bằng git apply --unidiff-zero làm hunk chỉ-chèn bị đặt lệch 1 dòng.
- root cause: Hành vi của git apply với hunk -U0.
- resolution: Tự áp hunk -U0 lên nội dung nền để tách commit.
- evidence: memory project-litmatch-multi-pod-readiness-branch.md

### P107: File CRLF trong working tree: sửa bằng script phải giữ EOL

status: fixed; tier: B; date: 2026-10-09; confidence: medium

- problem: Một số file (deploy/production/.env.example) là CRLF trong working tree; script sửa dễ làm đổi EOL và sinh diff nhiễu.
- root cause: Repo trộn LF/CRLF trên Windows (.gitattributes chỉ áp một phần).
- resolution: Script sửa file giữ nguyên EOL của từng file.
- evidence: memory project-litmatch-multi-pod-readiness-branch.md
