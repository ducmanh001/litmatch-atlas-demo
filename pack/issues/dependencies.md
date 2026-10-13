# Issues in area dependencies

id: issues:dependencies

### P025: Audit dependency báo lỗi brace-expansion

status: fixed; tier: A; date: 2026-07-22; confidence: medium

- problem: pnpm audit báo cảnh báo dependency liên quan brace-expansion nên CI/audit gate đỏ.
- root cause: Phiên bản dependency bị audit cảnh báo trong cây phụ thuộc (chi tiết CVE không có trong commit).
- resolution: Một commit lớn đổi package.json, pnpm-workspace.yaml và pnpm-lock.yaml (1176 dòng) để loại phiên bản bị cảnh báo (#35).
- evidence: commit 0d657c9

### P078: PR Dependabot đỏ vì chính sách minimumReleaseAge của pnpm

status: open; tier: A; date: 2026-09-09; confidence: high

- problem: Các PR bump dependency của Dependabot (next, vitest, joi, axios...) fail ở bước quality preflight với ERR_PNPM_MINIMUM_RELEASE_AGE_VIOLATION nên không merge được.
- root cause: Chính sách chuỗi cung ứng của pnpm 11 từ chối các bản phát hành quá mới, trong khi Dependabot đề xuất đúng các bản mới nhất.
- resolution: Đúng thiết kế nhưng chưa xử lý: chờ bản đủ tuổi hoặc cấu hình ngoại lệ có chủ đích cho gói cần cập nhật gấp.
- evidence: ci-run 37845423227, ci-run 37405486552
