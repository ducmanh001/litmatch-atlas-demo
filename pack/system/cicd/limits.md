# CI/CD: pipelines, quality gates, release, images: limits

id: system:cicd:limits

Written by an AI agent from the code; every file:line quote was checked automatically (exists within ±3 lines) but NO independent verification pass has run yet. Treat claims as strong leads; open the cited file before editing.

- Chỉ đọc tĩnh: không chạy CI, test, build hay deploy nào; thời gian, độ ổn định (flaky) và tỉ lệ cache hit của các run thật không được biết.
- Branch protection/ruleset, merge queue, số reviewer, bắt buộc code-owner review và quy tắc của GitHub Environment `staging` là cài đặt GitHub, không nằm trong repo.
- Giá trị secrets/variables (HOSTED_RELEASE_ENABLED, NORTHFLANK_*, PUBLIC_*, STAGING_*) và việc hosted release hiện đang bật hay tắt không thấy được.
- Trạng thái console Northflank (CD bật/tắt, đường dẫn Dockerfile), Cloudflare, Upstash, LiveKit Cloud chỉ được mô tả trong runbook, không kiểm chứng được.
- Không biết cluster K8s, registry ghcr, công cụ quản lý secret (sealed-secrets/external-secrets) có tồn tại hay không; repo không có pipeline cho chúng.
- Có thể GitHub-native code scanning, secret scanning, push protection hoặc Dependabot security updates đang bật trong cài đặt repo; kết luận "không có quét bảo mật" chỉ đúng với những gì repo định nghĩa.
- Chưa xác minh `gh api .../actions/workflows/ci.yml/runs` có chạy được với `permissions` đã khai (cần một lần dispatch thật).
- Chưa chạy `pnpm release:deploy`; nhận định release.mjs lỗi vì thiếu service kafka trong compose là suy luận từ mã.
- Hook Husky chỉ có hiệu lực trên máy đã chạy `pnpm install` (script prepare); không biết từng contributor có bypass hay không.
- timeoutMs của từng stage được tính bằng cách gọi hàm resolveStagePolicy của repo trên nhãn stage; lịch sử git (git log) được dùng để ghi chú workflow Security đã xóa và kafka đã bỏ khỏi compose.
