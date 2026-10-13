# Issues in area short-video

id: issues:short-video

### P088: View count video cộng đôi khi đồng thời

status: fixed; tier: A; date: 2026-10-09; confidence: high

- problem: recordView có thể cộng đôi view count khi đồng thời; đường save() bắt unique violation trong transaction bị Postgres huỷ.
- root cause: Check-then-act với save() + bắt unique violation trong transaction đã aborted.
- resolution: INSERT ... ON CONFLICT DO NOTHING rồi SELECT ... FOR UPDATE, bỏ đường save()+catch (liên hệ LRN-2026-002).
- evidence: commit d506ca2, memory project-litmatch-service-map.md, commit d506ca2

### P089: Sweeper video lỗi bị đói (lặp 200 hàng cũ nhất)

status: fixed; tier: A; date: 2026-10-09; confidence: high

- problem: Sweeper video lặp lại 200 hàng cũ nhất nên video failed mới bị bỏ đói, không được dọn storage.
- root cause: Không có cột đánh dấu đã dọn; hàng lỗi luôn nằm đầu hàng đợi.
- resolution: Thêm cột videos.storage_cleaned_at (migration mới) để hàng đã dọn rời hàng đợi, hàng lỗi bị đẩy về cuối.
- evidence: commit d506ca2, memory project-litmatch-service-map.md, commit d506ca2

### P108: Replay upload-intent cấp lại URL sau khi video đã published/failed

status: fixed; tier: A; date: 2026-10-09; confidence: high

- problem: Replay cùng Idempotency-Key sau khi video đã published hoặc failed vẫn cấp URL upload mới: tác giả thay được file đã duyệt, và object upload muộn của video failed (đã đánh dấu dọn) thành rác vĩnh viễn.
- root cause: Đường replay idempotent của upload-intent không kiểm trạng thái video trước khi cấp lại URL.
- resolution: Chỉ cấp lại URL khi video còn uploading, ngược lại trả 409. Test FOR UPDATE viết lại tất định (giữ khoá hàng ở giao dịch A; xác nhận đỏ khi bỏ khoá) thay cho test bắn nhiều request vốn vẫn xanh khi thiếu khoá.
- evidence: commit dc32ca0
