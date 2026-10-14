# Lookup: issues

id: lookup:issues

P0xx to the file that documents it.

| id | file | note |
|---|---|---|
| P001 | ../issues/economy-payment.md | Guard ledger chặn nhầm bút toán đảo (refund/chargeback) |
| P002 | ../issues/ci-cd.md | CI main đỏ ngay từ commit đầu: "Multiple versions of pnpm specified" |
| P003 | ../issues/ai-agent-process.md | Commit "harden phase 2A" quá lớn phải revert khỏi main |
| P004 | ../issues/ai-agent-process.md | Adapter CLAUDE.md / skills lệch bản canonical (symlink tay) |
| P005 | ../issues/auth-security.md | CodeQL: loop-bound-injection (high) ở palm-match.hash.ts |
| P006 | ../issues/frontend-ux.md | Swagger thiếu cursor pagination → client sinh kiểu never |
| P007 | ../issues/ci-cd.md | CI hosted lệch CI local (runner sạch) |
| P008 | ../issues/auth-security.md | CodeQL: log rõ thông tin nhạy cảm ở runner CI local |
| P009 | ../issues/auth-security.md | Cookie refresh token: 2 lỗi chỉ lộ khi test trình duyệt thật |
| P010 | ../issues/matching.md | Bộ lọc vùng Discovery cho độ chính xác giả (LRN-001) |
| P011 | ../issues/database-concurrency.md | Transaction Postgres bị aborted sau unique violation (LRN-002) |
| P012 | ../issues/auth-security.md | CodeQL: loop-bound-injection (high) ở common/hash/fnv1a.ts còn mở |
| P013 | ../issues/database-concurrency.md | Job định kỳ chồng lấp khi tick chậm/reject (LRN-003) |
| P014 | ../issues/deploy-config.md | Cấu hình theo máy (tunnel/IP/credential) nằm trong repo (LRN-004) |
| P015 | ../issues/realtime-gateway.md | Socket reconnect không khôi phục state bền (LRN-005) |
| P016 | ../issues/ai-agent-process.md | PASS cục bộ bị hiểu thành PASS toàn repo (LRN-006) |
| P017 | ../issues/observability.md | OTLP exporter nhận endpoint Prometheus remote_write (LRN-007) |
| P018 | ../issues/auth-security.md | JWT access token trùng nhau khi cấp trong cùng giây (LRN-010) |
| P019 | ../issues/economy-payment.md | Chữ ký DisabledIapVerifier không khớp port |
| P020 | ../issues/other.md | Vòng phụ thuộc Soul DTO ↔ service |
| P021 | ../issues/deploy-config.md | Redis TLS rediss:// có nguy cơ bị Joi từ chối khi boot |
| P022 | ../issues/ci-cd.md | CVE ở runtime image mà pre-push local bỏ sót |
| P023 | ../issues/ci-cd.md | Integration test race do schema/Redis dùng chung |
| P024 | ../issues/ci-cd.md | Build web OpenNext/ESM hỏng, phải sửa 3 lần |
| P025 | ../issues/dependencies.md | Audit dependency báo lỗi brace-expansion |
| P026 | ../issues/deploy-config.md | Production thiếu biến môi trường đăng nhập |
| P027 | ../issues/ci-cd.md | CI quality-result đỏ do comment/param env trong workflow |
| P028 | ../issues/auth-security.md | Đăng nhập SMS "accepted" của admin còn bật ở production |
| P029 | ../issues/ci-cd.md | Nhiều đường bypass gate CI (hook local / GitHub / hosted lệch nhau) |
| P030 | ../issues/docs-tooling.md | Reviewer độc lập tìm ra 3 vấn đề khi tổ chức lại tài liệu |
| P031 | ../issues/observability.md | Wrapper OTel cho OpenNext Cloudflare handler lỗi sau khi thêm Loki |
| P032 | ../issues/matching.md | Lỗi boot production khi chạy profile local (Matching capability) |
| P033 | ../issues/database-concurrency.md | Khe hở đồng thời ở feed / party-room / safety |
| P034 | ../issues/matching.md | Matching trả 400 (useDiamond) + nút CTA sai trạng thái |
| P035 | ../issues/ci-cd.md | Generated client lệch OpenAPI làm CI đỏ (#188) |
| P036 | ../issues/windows-toolchain.md | Windows: hook/runner không chạy được pnpm (.cmd: 127 / EINVAL) |
| P037 | ../issues/windows-toolchain.md | Windows/Docker: CRLF + symlink thành file thường làm format/agent:check đỏ |
| P038 | ../issues/windows-toolchain.md | Windows: Jest không chạy (thiếu VC++ runtime, resolver native) |
| P039 | ../issues/windows-toolchain.md | Pre-push trên Windows đỏ giả do giả định POSIX / công cụ Linux-only |
| P040 | ../issues/ci-cd.md | Watchdog CI quá thô cắt gate giữa chừng (LRN-008) |
| P041 | ../issues/ci-cd.md | Timeout cứng quá chặt / chạy song song làm test flaky |
| P042 | ../issues/ci-cd.md | Test gia hạn lease bị đói timer khi chạy cạnh pool lớn (LRN-009) |
| P043 | ../issues/ci-cd.md | Nx cache bật cho test có state → có thể trả kết quả cũ |
| P044 | ../issues/ci-cd.md | E2E local: server cũ còn sống, reset DB chồng, thiếu baseUrl |
| P045 | ../issues/ci-cd.md | Lint: config E2E import vi phạm ranh giới Nx |
| P046 | ../issues/matching.md | Guest bấm Matching → 401 vì thiếu X-Guest-Device-Token |
| P047 | ../issues/deploy-config.md | Ảnh avatar trỏ cdn.litmatch.example không phân giải DNS |
| P048 | ../issues/frontend-ux.md | Guest thấy nút đăng bài/like/comment nhưng backend luôn 403 |
| P049 | ../issues/frontend-ux.md | Thông báo Facebook gõ sai "ckhả dụng" |
| P050 | ../issues/ai-agent-process.md | Không push được vì môi trường (thiếu gh, Docker/WSL2, hook) |
| P051 | ../issues/ai-agent-process.md | ci:preflight treo làm mất thay đổi chưa commit |
| P052 | ../issues/database-concurrency.md | Sửa nhầm migration đã commit (guard bất biến chặn) |
| P053 | ../issues/ci-cd.md | Conflict nhánh matching với main |
| P054 | ../issues/ci-cd.md | Hook pre-push chỉ chạy affected nên lỗi lọt lên CI |
| P055 | ../issues/ci-cd.md | Chưa bắt buộc required checks: PR vẫn merge được khi CI đỏ |
| P056 | ../issues/ci-cd.md | Báo động giả: push nhánh không thấy CI chạy |
| P057 | ../issues/frontend-ux.md | Màn matching PC: CTA tĩnh, không có trạng thái chọn |
| P058 | ../issues/frontend-ux.md | Nhiều nút action thiếu cursor pointer |
| P059 | ../issues/frontend-ux.md | Test Feed đỏ do thiếu setup session |
| P060 | ../issues/windows-toolchain.md | tsconfig baseUrl deprecated → mất alias lúc runtime |
| P061 | ../issues/observability.md | Lỗi TS ở tsconfig.spec không làm build fail |
| P062 | ../issues/ci-cd.md | Prettier lỗi bảng markdown → CI #189 đỏ |
| P063 | ../issues/ci-cd.md | Test frontend (admin/web) timeout lặp lại |
| P064 | ../issues/ci-cd.md | Container smoke Linux-only chạy trên Windows chặn push |
| P065 | ../issues/deploy-config.md | Production chạy bản cũ: route profile 404, thông báo Facebook cũ |
| P066 | ../issues/economy-payment.md | Hiểu sai luật cổng quà (follower/ngày vs người bắt chuyện lần đầu) |
| P067 | ../issues/frontend-ux.md | Toast đỏ sai ngữ cảnh khi hồ sơ yêu cầu tặng quà |
| P068 | ../issues/database-concurrency.md | Migration gộp gây phụ thuộc chéo + gate kiến trúc đỏ |
| P069 | ../issues/ci-cd.md | Dry-run test rò cờ Nx cache làm pre-push fail |
| P070 | ../issues/frontend-ux.md | Inbox không hiện hội thoại trực tiếp |
| P071 | ../issues/auth-security.md | Cổng gọi điện có thể bypass sau khi block (bắt được khi tự review) |
| P072 | ../issues/windows-toolchain.md | Windows: Nx build lỗi 1314 + runner timeout (exit 124) |
| P073 | ../issues/ci-cd.md | Báo động giả: clean container fail ở agent-check không có assertion |
| P074 | ../issues/deploy-config.md | Core API production crash lúc boot (media storage dev / thiếu biến R2) |
| P075 | ../issues/auth-security.md | OTP trả mã ngay trong response khi production bật OTP |
| P076 | ../issues/deploy-config.md | Rate limit trong bộ nhớ từng pod + trần kết nối Postgres |
| P077 | ../issues/windows-toolchain.md | Test hard-kill của run-stage lỗi trên Windows (exit 124) |
| P078 | ../issues/dependencies.md | PR Dependabot đỏ vì chính sách minimumReleaseAge của pnpm |
| P079 | ../issues/ci-cd.md | Push bị GitHub từ chối 403 (sai tài khoản) |
| P080 | ../issues/ai-agent-process.md | Chạm session limit vì workflow nhiều agent (đốt ~2,9M token) |
| P081 | ../issues/observability.md | Báo động giả: /metrics trả 404 (đúng thiết kế OTLP) |
| P082 | ../issues/auth-security.md | Báo động giả: Nearby 403 / Feed chặn guest là đúng chính sách |
| P083 | ../issues/realtime-gateway.md | Báo động giả: Party media lỗi khi headless không cấp mic |
| P084 | ../issues/auth-security.md | Báo động giả: throttle đăng nhập guest 5 lần/phút chặn retest |
| P085 | ../issues/frontend-ux.md | Guest reload mất device token → Matching 401 |
| P086 | ../issues/realtime-gateway.md | Relay realtime gửi trùng event theo số pod (LRN-011) |
| P087 | ../issues/auth-security.md | Bỏ qua kiểm bundleId Apple khi env rỗng |
| P088 | ../issues/short-video.md | View count video cộng đôi khi đồng thời |
| P089 | ../issues/short-video.md | Sweeper video lỗi bị đói (lặp 200 hàng cũ nhất) |
| P090 | ../issues/matching.md | Huỷ ván mini-game ghi đè ván đã resolved |
| P091 | ../issues/auth-security.md | Xoay JWT_SECRET đăng xuất mọi người |
| P092 | ../issues/windows-toolchain.md | Windows thiếu zip/unzip nên docs:check không chạy |
| P093 | ../issues/docs-tooling.md | Báo cáo bằng chứng generated bị stale |
| P094 | ../issues/matching.md | Giới hạn đã biết: advisory lock Palm/Movie Match, outbox chưa có consumer/retention |
| P095 | ../issues/ai-agent-process.md | Bước prune xoá nhầm 15 kết luận hợp lệ (pipeline service-map) |
| P096 | ../issues/ai-agent-process.md | Heuristic độ phủ khớp đuôi đường dẫn ngắn → dương tính giả |
| P097 | ../issues/ai-agent-process.md | Sửa script qua shell lồng quote làm hỏng regex; Workflow scriptPath bị từ chối |
| P098 | ../issues/ai-agent-process.md | Báo cáo đầu chỉ là smoke test, bỏ sót lỗi profile |
| P099 | ../issues/other.md | Ranked sort loại hàng có rankScore IS NULL |
| P100 | ../issues/other.md | getHiddenUserIds không có cửa sổ thời gian (cần quyết định sản phẩm) |
| P101 | ../issues/other.md | Lời mời hết hạn kiểu lazy-expire không được persist |
| P102 | ../issues/other.md | Comment k8s lỗi thời |
| P103 | ../issues/other.md | Phân trang cursor video-queue admin (pending_review tăng dần nhưng cursor dùng less-than; suy luận) |
| P104 | ../issues/other.md | soul-match end phát lại soul.ended khi replay |
| P105 | ../issues/ai-agent-process.md | Spec integration liệt kê migration tường minh: migration mới phải đăng ký ở 2 spec |
| P106 | ../issues/ai-agent-process.md | git apply --unidiff-zero đặt sai vị trí hunk chèn thuần |
| P107 | ../issues/ai-agent-process.md | File CRLF trong working tree: sửa bằng script phải giữ EOL |
| P108 | ../issues/short-video.md | Replay upload-intent cấp lại URL sau khi video đã published/failed |
