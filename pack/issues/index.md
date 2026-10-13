# Error history ledger (public version)

id: issues:index
rows: 103 (open security/production findings are withheld from the public version)

| id | status | tier | area | date | title |
|---|---|---|---|---|---|
| P001 | fixed | A | economy-payment | 2026-07-10 | Guard ledger chặn nhầm bút toán đảo (refund/chargeback) |
| P002 | fixed | A | ci-cd | 2026-07-11 | CI main đỏ ngay từ commit đầu: "Multiple versions of pnpm specified" |
| P003 | fixed | B | ai-agent-process | 2026-07-11 | Commit "harden phase 2A" quá lớn phải revert khỏi main |
| P004 | fixed | B | ai-agent-process | 2026-07-12 | Adapter CLAUDE.md / skills lệch bản canonical (symlink tay) |
| P006 | fixed | A | frontend-ux | 2026-07-13 | Swagger thiếu cursor pagination → client sinh kiểu never |
| P007 | fixed | A | ci-cd | 2026-07-13 | CI hosted lệch CI local (runner sạch) |
| P008 | fixed | A | auth-security | 2026-07-13 | CodeQL: log rõ thông tin nhạy cảm ở runner CI local |
| P009 | fixed | A | auth-security | 2026-07-13 | Cookie refresh token: 2 lỗi chỉ lộ khi test trình duyệt thật |
| P010 | fixed | B | matching | 2026-07-13 | Bộ lọc vùng Discovery cho độ chính xác giả (LRN-001) |
| P011 | mitigated | A | database-concurrency | 2026-07-13 | Transaction Postgres bị aborted sau unique violation (LRN-002) |
| P013 | fixed | A | database-concurrency | 2026-07-18 | Job định kỳ chồng lấp khi tick chậm/reject (LRN-003) |
| P014 | fixed | A | deploy-config | 2026-07-18 | Cấu hình theo máy (tunnel/IP/credential) nằm trong repo (LRN-004) |
| P015 | mitigated | A | realtime-gateway | 2026-07-18 | Socket reconnect không khôi phục state bền (LRN-005) |
| P016 | mitigated | B | ai-agent-process | 2026-07-18 | PASS cục bộ bị hiểu thành PASS toàn repo (LRN-006) |
| P017 | fixed | A | observability | 2026-07-18 | OTLP exporter nhận endpoint Prometheus remote_write (LRN-007) |
| P018 | fixed | A | auth-security | 2026-07-18 | JWT access token trùng nhau khi cấp trong cùng giây (LRN-010) |
| P019 | fixed | A | economy-payment | 2026-07-18 | Chữ ký DisabledIapVerifier không khớp port |
| P020 | fixed | B | other | 2026-07-18 | Vòng phụ thuộc Soul DTO ↔ service |
| P021 | fixed | A | deploy-config | 2026-07-20 | Redis TLS rediss:// có nguy cơ bị Joi từ chối khi boot |
| P022 | fixed | A | ci-cd | 2026-07-22 | CVE ở runtime image mà pre-push local bỏ sót |
| P023 | fixed | A | ci-cd | 2026-07-22 | Integration test race do schema/Redis dùng chung |
| P024 | fixed | A | ci-cd | 2026-07-22 | Build web OpenNext/ESM hỏng, phải sửa 3 lần |
| P025 | fixed | A | dependencies | 2026-07-22 | Audit dependency báo lỗi brace-expansion |
| P026 | fixed | A | deploy-config | 2026-07-24 | Production thiếu biến môi trường đăng nhập |
| P027 | fixed | A | ci-cd | 2026-07-24 | CI quality-result đỏ do comment/param env trong workflow |
| P028 | fixed | A | auth-security | 2026-07-24 | Đăng nhập SMS "accepted" của admin còn bật ở production |
| P029 | fixed | B | ci-cd | 2026-07-30 | Nhiều đường bypass gate CI (hook local / GitHub / hosted lệch nhau) |
| P030 | fixed | B | docs-tooling | 2026-07-30 | Reviewer độc lập tìm ra 3 vấn đề khi tổ chức lại tài liệu |
| P031 | fixed | A | observability | 2026-08-02 | Wrapper OTel cho OpenNext Cloudflare handler lỗi sau khi thêm Loki |
| P032 | fixed | A | matching | 2026-08-12 | Lỗi boot production khi chạy profile local (Matching capability) |
| P033 | fixed | A | database-concurrency | 2026-08-13 | Khe hở đồng thời ở feed / party-room / safety |
| P034 | fixed | A | matching | 2026-08-14 | Matching trả 400 (useDiamond) + nút CTA sai trạng thái |
| P035 | fixed | A | ci-cd | 2026-08-14 | Generated client lệch OpenAPI làm CI đỏ (#188) |
| P036 | fixed | A | windows-toolchain | 2026-08-14 | Windows: hook/runner không chạy được pnpm (.cmd: 127 / EINVAL) |
| P037 | fixed | A | windows-toolchain | 2026-08-14 | Windows/Docker: CRLF + symlink thành file thường làm format/agent:check đỏ |
| P038 | fixed | A | windows-toolchain | 2026-08-14 | Windows: Jest không chạy (thiếu VC++ runtime, resolver native) |
| P039 | fixed | A | windows-toolchain | 2026-08-14 | Pre-push trên Windows đỏ giả do giả định POSIX / công cụ Linux-only |
| P040 | fixed | B | ci-cd | 2026-08-14 | Watchdog CI quá thô cắt gate giữa chừng (LRN-008) |
| P041 | fixed | A | ci-cd | 2026-08-14 | Timeout cứng quá chặt / chạy song song làm test flaky |
| P042 | fixed | A | ci-cd | 2026-08-14 | Test gia hạn lease bị đói timer khi chạy cạnh pool lớn (LRN-009) |
| P043 | fixed | B | ci-cd | 2026-08-14 | Nx cache bật cho test có state → có thể trả kết quả cũ |
| P044 | fixed | A | ci-cd | 2026-08-14 | E2E local: server cũ còn sống, reset DB chồng, thiếu baseUrl |
| P045 | fixed | A | ci-cd | 2026-08-14 | Lint: config E2E import vi phạm ranh giới Nx |
| P046 | fixed | A | matching | 2026-08-14 | Guest bấm Matching → 401 vì thiếu X-Guest-Device-Token |
| P047 | fixed | A | deploy-config | 2026-08-14 | Ảnh avatar trỏ cdn.litmatch.example không phân giải DNS |
| P048 | fixed | A | frontend-ux | 2026-08-14 | Guest thấy nút đăng bài/like/comment nhưng backend luôn 403 |
| P049 | fixed | A | frontend-ux | 2026-08-14 | Thông báo Facebook gõ sai "ckhả dụng" |
| P050 | mitigated | B | ai-agent-process | 2026-08-14 | Không push được vì môi trường (thiếu gh, Docker/WSL2, hook) |
| P051 | mitigated | B | ai-agent-process | 2026-08-14 | ci:preflight treo làm mất thay đổi chưa commit |
| P052 | fixed | B | database-concurrency | 2026-08-14 | Sửa nhầm migration đã commit (guard bất biến chặn) |
| P053 | fixed | B | ci-cd | 2026-08-14 | Conflict nhánh matching với main |
| P054 | fixed | B | ci-cd | 2026-08-14 | Hook pre-push chỉ chạy affected nên lỗi lọt lên CI |
| P055 | open | B | ci-cd | 2026-08-14 | Chưa bắt buộc required checks: PR vẫn merge được khi CI đỏ |
| P056 | false-alarm | C | ci-cd | 2026-08-14 | Báo động giả: push nhánh không thấy CI chạy |
| P057 | fixed | A | frontend-ux | 2026-08-14 | Màn matching PC: CTA tĩnh, không có trạng thái chọn |
| P058 | fixed | A | frontend-ux | 2026-08-14 | Nhiều nút action thiếu cursor pointer |
| P059 | fixed | A | frontend-ux | 2026-08-14 | Test Feed đỏ do thiếu setup session |
| P060 | fixed | A | windows-toolchain | 2026-08-14 | tsconfig baseUrl deprecated → mất alias lúc runtime |
| P061 | mitigated | B | observability | 2026-08-14 | Lỗi TS ở tsconfig.spec không làm build fail |
| P062 | fixed | A | ci-cd | 2026-08-14 | Prettier lỗi bảng markdown → CI #189 đỏ |
| P063 | open | A | ci-cd | 2026-08-14 | Test frontend (admin/web) timeout lặp lại |
| P064 | fixed | A | ci-cd | 2026-08-19 | Container smoke Linux-only chạy trên Windows chặn push |
| P066 | fixed | B | economy-payment | 2026-08-19 | Hiểu sai luật cổng quà (follower/ngày vs người bắt chuyện lần đầu) |
| P067 | fixed | A | frontend-ux | 2026-08-19 | Toast đỏ sai ngữ cảnh khi hồ sơ yêu cầu tặng quà |
| P068 | fixed | B | database-concurrency | 2026-08-19 | Migration gộp gây phụ thuộc chéo + gate kiến trúc đỏ |
| P069 | fixed | A | ci-cd | 2026-08-20 | Dry-run test rò cờ Nx cache làm pre-push fail |
| P070 | fixed | A | frontend-ux | 2026-08-20 | Inbox không hiện hội thoại trực tiếp |
| P071 | fixed | A | auth-security | 2026-08-20 | Cổng gọi điện có thể bypass sau khi block (bắt được khi tự review) |
| P072 | mitigated | B | windows-toolchain | 2026-08-20 | Windows: Nx build lỗi 1314 + runner timeout (exit 124) |
| P073 | false-alarm | C | ci-cd | 2026-08-20 | Báo động giả: clean container fail ở agent-check không có assertion |
| P076 | fixed | B | deploy-config | 2026-09-03 | Rate limit trong bộ nhớ từng pod + trần kết nối Postgres |
| P077 | open | A | windows-toolchain | 2026-09-03 | Test hard-kill của run-stage lỗi trên Windows (exit 124) |
| P078 | open | A | dependencies | 2026-09-09 | PR Dependabot đỏ vì chính sách minimumReleaseAge của pnpm |
| P079 | fixed | A | ci-cd | 2026-10-08 | Push bị GitHub từ chối 403 (sai tài khoản) |
| P080 | fixed | B | ai-agent-process | 2026-10-08 | Chạm session limit vì workflow nhiều agent (đốt ~2,9M token) |
| P081 | false-alarm | C | observability | 2026-10-09 | Báo động giả: /metrics trả 404 (đúng thiết kế OTLP) |
| P082 | false-alarm | C | auth-security | 2026-10-09 | Báo động giả: Nearby 403 / Feed chặn guest là đúng chính sách |
| P083 | false-alarm | C | realtime-gateway | 2026-10-09 | Báo động giả: Party media lỗi khi headless không cấp mic |
| P084 | false-alarm | C | auth-security | 2026-10-09 | Báo động giả: throttle đăng nhập guest 5 lần/phút chặn retest |
| P085 | open | A | frontend-ux | 2026-10-09 | Guest reload mất device token → Matching 401 |
| P086 | fixed | A | realtime-gateway | 2026-10-09 | Relay realtime gửi trùng event theo số pod (LRN-011) |
| P087 | fixed | A | auth-security | 2026-10-09 | Bỏ qua kiểm bundleId Apple khi env rỗng |
| P088 | fixed | A | short-video | 2026-10-09 | View count video cộng đôi khi đồng thời |
| P089 | fixed | A | short-video | 2026-10-09 | Sweeper video lỗi bị đói (lặp 200 hàng cũ nhất) |
| P090 | fixed | A | matching | 2026-10-09 | Huỷ ván mini-game ghi đè ván đã resolved |
| P091 | fixed | B | auth-security | 2026-10-09 | Xoay JWT_SECRET đăng xuất mọi người |
| P092 | mitigated | A | windows-toolchain | 2026-10-09 | Windows thiếu zip/unzip nên docs:check không chạy |
| P093 | fixed | A | docs-tooling | 2026-10-09 | Báo cáo bằng chứng generated bị stale |
| P094 | open | B | matching | 2026-10-09 | Giới hạn đã biết: advisory lock Palm/Movie Match, outbox chưa có consumer/retention |
| P095 | fixed | B | ai-agent-process | 2026-10-09 | Bước prune xoá nhầm 15 kết luận hợp lệ (pipeline service-map) |
| P096 | fixed | B | ai-agent-process | 2026-10-09 | Heuristic độ phủ khớp đuôi đường dẫn ngắn → dương tính giả |
| P097 | fixed | B | ai-agent-process | 2026-10-09 | Sửa script qua shell lồng quote làm hỏng regex; Workflow scriptPath bị từ chối |
| P098 | fixed | B | ai-agent-process | 2026-10-09 | Báo cáo đầu chỉ là smoke test, bỏ sót lỗi profile |
| P099 | open | B | other | 2026-10-09 | Ranked sort loại hàng có rankScore IS NULL |
| P100 | open | B | other | 2026-10-09 | getHiddenUserIds không có cửa sổ thời gian (cần quyết định sản phẩm) |
| P101 | open | B | other | 2026-10-09 | Lời mời hết hạn kiểu lazy-expire không được persist |
| P102 | open | B | other | 2026-10-09 | Comment k8s lỗi thời |
| P103 | open | B | other | 2026-10-09 | Phân trang cursor video-queue admin (pending_review tăng dần nhưng cursor dùng less-than; suy luận) |
| P104 | open | B | other | 2026-10-09 | soul-match end phát lại soul.ended khi replay |
| P105 | fixed | B | ai-agent-process | 2026-10-09 | Spec integration liệt kê migration tường minh: migration mới phải đăng ký ở 2 spec |
| P106 | fixed | B | ai-agent-process | 2026-10-09 | git apply --unidiff-zero đặt sai vị trí hunk chèn thuần |
| P107 | fixed | B | ai-agent-process | 2026-10-09 | File CRLF trong working tree: sửa bằng script phải giữ EOL |
| P108 | fixed | A | short-video | 2026-10-09 | Replay upload-intent cấp lại URL sau khi video đã published/failed |
