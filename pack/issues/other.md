# Issues in area other

id: issues:other

### P020: Vòng phụ thuộc Soul DTO ↔ service

status: fixed; tier: B; date: 2026-07-18; confidence: high

- problem: Dependency cycle Soul DTO -> Soul service (DTO import enum từ service implementation).
- root cause: Enum đặt trong service nên DTO phải import ngược boundary.
- resolution: Chuyển kiểu sang soul-match.types.ts; Madge quét 801 file TS/TSX còn 0 cycle.
- evidence: doc docs/plans/2026-07-18-whole-source-refactor-review.md

### P099: Ranked sort loại hàng có rankScore IS NULL

status: open; tier: B; date: 2026-10-09; confidence: medium

- problem: Quan sát từ audit tĩnh service-map: Ranked sort loại hàng có rankScore IS NULL. Chưa xác nhận ở runtime, chưa có commit sửa.
- root cause: Suy ra từ đọc code (audit tĩnh), chưa tái hiện.
- resolution: Chưa xử lý; được ghi trong memory như quan sát cho dev team.
- evidence: memory project-litmatch-service-map.md

### P100: getHiddenUserIds không có cửa sổ thời gian (cần quyết định sản phẩm)

status: open; tier: B; date: 2026-10-09; confidence: medium

- problem: Quan sát từ audit tĩnh service-map: getHiddenUserIds không có cửa sổ thời gian (cần quyết định sản phẩm). Chưa xác nhận ở runtime, chưa có commit sửa.
- root cause: Suy ra từ đọc code (audit tĩnh), chưa tái hiện.
- resolution: Chưa xử lý; được ghi trong memory như quan sát cho dev team.
- evidence: memory project-litmatch-service-map.md

### P101: Lời mời hết hạn kiểu lazy-expire không được persist

status: open; tier: B; date: 2026-10-09; confidence: medium

- problem: Quan sát từ audit tĩnh service-map: Lời mời hết hạn kiểu lazy-expire không được persist. Chưa xác nhận ở runtime, chưa có commit sửa.
- root cause: Suy ra từ đọc code (audit tĩnh), chưa tái hiện.
- resolution: Chưa xử lý; được ghi trong memory như quan sát cho dev team.
- evidence: memory project-litmatch-service-map.md

### P102: Comment k8s lỗi thời

status: open; tier: B; date: 2026-10-09; confidence: medium

- problem: Quan sát từ audit tĩnh service-map: Comment k8s lỗi thời. Chưa xác nhận ở runtime, chưa có commit sửa.
- root cause: Suy ra từ đọc code (audit tĩnh), chưa tái hiện.
- resolution: Chưa xử lý; được ghi trong memory như quan sát cho dev team.
- evidence: memory project-litmatch-service-map.md

### P103: Phân trang cursor video-queue admin (pending_review tăng dần nhưng cursor dùng less-than; suy luận)

status: open; tier: B; date: 2026-10-09; confidence: medium

- problem: Quan sát từ audit tĩnh service-map: Phân trang cursor video-queue admin (pending_review tăng dần nhưng cursor dùng less-than; suy luận). Chưa xác nhận ở runtime, chưa có commit sửa.
- root cause: Suy ra từ đọc code (audit tĩnh), chưa tái hiện.
- resolution: Chưa xử lý; được ghi trong memory như quan sát cho dev team.
- evidence: memory project-litmatch-service-map.md

### P104: soul-match end phát lại soul.ended khi replay

status: open; tier: B; date: 2026-10-09; confidence: medium

- problem: Quan sát từ audit tĩnh service-map: soul-match end phát lại soul.ended khi replay. Chưa xác nhận ở runtime, chưa có commit sửa.
- root cause: Suy ra từ đọc code (audit tĩnh), chưa tái hiện.
- resolution: Chưa xử lý; được ghi trong memory như quan sát cho dev team.
- evidence: memory project-litmatch-service-map.md
