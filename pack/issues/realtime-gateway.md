# Issues in area realtime-gateway

id: issues:realtime-gateway

### P015: Socket reconnect không khôi phục state bền (LRN-005)

status: mitigated; tier: A; date: 2026-07-18; confidence: medium

- problem: Chỉ reconnect socket realtime có thể để state bền phía server cũ sau khi lỡ sự kiện.
- root cause: Đường reconnect coi phục hồi transport là phục hồi state.
- resolution: Handler reconnect ở apps/web layout refetch state REST. Test hành vi cho missed-event recovery vẫn còn thiếu.
- evidence: doc docs/reference/lessons-registry.md, doc docs/reference/project-handoff.md

### P083: Báo động giả: Party media lỗi khi headless không cấp mic

status: false-alarm; tier: C; date: 2026-10-09; confidence: high

- problem: Party media báo lỗi khi chạy browser headless.
- root cause: Không cấp quyền microphone cho browser test; cấp quyền thì PASS.
- resolution: Chạy lại với quyền microphone, kết nối LiveKit thành công.
- evidence: codex-thread 01a11dea

### P086: Relay realtime gửi trùng event theo số pod (LRN-011)

status: fixed; tier: A; date: 2026-10-09; confidence: high

- problem: Relay realtime của signaling-gateway gửi trùng event: mỗi socket nhận N bản (N = số pod).
- root cause: Mọi pod PSUBSCRIBE realtime:user:* và relay bằng server.to(room).emit; Redis cluster adapter lại chuyển tiếp broadcast sang các pod khác.
- resolution: Dùng server.local khi emit. Test hai pod chạy mã thật @socket.io/redis-adapter trên bus giả + 1 case Redis thật (đã xác nhận đỏ khi bỏ fix). Ghi LRN-2026-011. Đánh đổi của relay cục bộ khi pod mất subscription đã được ghi vào docs/services/realtime-gateway.md (3888a32).
- evidence: commit d639899, memory project-litmatch-service-map.md, claude-session 1f780174, file apps/signaling-gateway/src/app/signaling.gateway.ts
