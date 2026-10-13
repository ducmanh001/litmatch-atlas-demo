# Issues in area docs-tooling

id: issues:docs-tooling

### P030: Reviewer độc lập tìm ra 3 vấn đề khi tổ chức lại tài liệu

status: fixed; tier: B; date: 2026-07-30; confidence: low

- problem: Counterexample reviewer tìm ra ba vấn đề actionable khi tổ chức lại tài liệu; baseline có các finding cần sửa.
- root cause: chua ro (trích đoạn không nêu chi tiết)
- resolution: Sửa theo bảng Baseline findings/Correction; dùng progressive-disclosure indexes và evidence ladder.
- evidence: doc docs/plans/2026-07-30-documentation-reorganization-review.md

### P093: Báo cáo bằng chứng generated bị stale

status: fixed; tier: A; date: 2026-10-09; confidence: high

- problem: Báo cáo bằng chứng generated bị stale vì vị trí source dịch chuyển.
- root cause: Source dịch chuyển nhưng báo cáo sinh tự động chưa chạy lại.
- resolution: Chạy pnpm docs:generate để sinh lại; docs:check PASS; không đổi status capability.
- evidence: commit 50f7dc9
