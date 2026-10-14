# Roadmap · Litmatch System Atlas

id: roadmap
site: atlas
updated: 2026-10-14; repo ducmanh001/litmatch @ d3cb28c

"Roadmap" ở đây gồm bốn phần: **A. lộ trình đọc** (muốn làm việc X thì mở tệp nào), **B. lộ trình dự án** (việc tiếp theo của chính sản phẩm, lấy từ docs/07-roadmap.md và docs/feature-registry.json), **C. việc còn mở** (vấn đề trong sổ lỗi và quan sát của service map chưa xong), **D. lộ trình của atlas** (đã phủ gì, còn thiếu gì).
Mọi thứ công khai, không ẩn mục nào. Tệp máy đọc: [llms.txt](llms.txt) · [sitemap.xml](sitemap.xml) · [search-index.json](search-index.json) · [index.json](index.json).

## A. Lộ trình đọc (theo việc cần làm)

| Việc cần làm | Đọc trước | Đọc tiếp |
|---|---|---|
| Hiểu hệ thống từ đầu | pack/README.md | pack/modules/index.md, pack/service-map/index.md, pack/flows/index.md |
| Theo một tính năng từ màn hình tới database | pack/slices/index.md rồi pack/slices/<feature>.md | pack/fe/index.md, pack/flows/index.md, pack/db/index.md, pack/api/index.md |
| Thêm hoặc sửa endpoint | pack/api/<module>.md | pack/api/authz.md, pack/api/no-client.md, pack/errors/index.md, pack/recipes.md |
| Sửa bảng hoặc migration | pack/db/<module>.md | pack/modules/<module>.md (ai khác đọc/ghi), pack/recipes.md, pack/json/db-usage.json |
| Thêm realtime event, job, outbox | pack/events/realtime.md | pack/system/async/index.md, pack/flows/index.md |
| Cache, Redis, lock, idempotency, state client | pack/system/state/index.md | pack/system/state/redisKeys.md, pack/system/state/idempotency.md, pack/fe/state-and-cache.md |
| Luật nghiệp vụ, thuật ngữ, xử lý lỗi | pack/system/domain/index.md | pack/system/domain/businessRules.md, pack/system/domain/glossary.md, pack/errors/index.md |
| Thêm biến cấu hình | pack/config/index.md | pack/recipes.md, pack/system/state/index.md |
| CI/CD, cổng chất lượng, release | pack/system/cicd/index.md | pack/recipes.md |
| Vì sao service A gọi service B | pack/service-map/<A>.md | pack/flows/index.md, pack/lookup/entrypoint-to-flow.md |
| Đã từng hỏng gì ở khu vực này | pack/issues/index.md | https://ducmanh001.github.io/litmatch-error-history/roadmap.md |
| Tra theo tên (endpoint, bảng, biến, mã lỗi, event, P0xx) | pack/lookup/index.md | search.html, search-index.json |
| Dự án sẽ làm gì tiếp theo | mục B và C của trang này | pack/project/roadmap-source.md, pack/project/feature-registry.md |

Quy ước id (khoá nối giữa mọi tệp): module:<tên>, db:<module> hoặc tên bảng, "METHOD /path", flow:<id>, tên event realtime (match.confirmed), mã lỗi DOMAIN_SUBJECT_REASON, tên biến môi trường, P0xx, slice:<feature>. Code luôn thắng tài liệu: mở file:line được dẫn trước khi sửa.

## B. Lộ trình dự án

### B1. Trạng thái theo giai đoạn

Nguồn: `docs/07-roadmap.md` (repo ducmanh001/litmatch @ d3cb28c). `[x]` = repo có bằng chứng, không có nghĩa đã chạy production. Bản đầy đủ: [pack/project/roadmap-source.md](pack/project/roadmap-source.md).

| Giai đoạn | Xong | Còn mở (điều kiện và việc cần làm) |
|---|---|---|
| Giai đoạn 0 — Nền móng | 3/3 | - |
| Giai đoạn 1 — Economy | 2/3 | Chạy Apple/Google sandbox/production credential flow và lưu operational evidence trước khi gọi native IAP là available. |
| Giai đoạn 2 — Matching lõi | 3/5 | Bổ sung UX nâng cấp guest ở client; endpoint tồn tại không tự tạo một user journey hoàn chỉnh. • Bung matcher/shard mới chỉ khi queue depth/latency theo region có production evidence. |
| Giai đoạn 3 — Party Room và Gift | 2/4 | Benchmark workload Party Room thật trước khi nới member/speaker cap; thêm LiveKit node không chia một room qua nhiều node. • Chốt refund policy cho gift nếu product yêu cầu; correction vẫn phải đi qua reversal. |
| Giai đoạn 4 — Social, discovery và safety | 3/5 | Push provider production và user-warning API cần contract/backend riêng trước khi thêm UI. • Re-enable visible short-video report action chỉ sau product decision + behavior test; backend report contract tồn tại không đồng nghĩa UI capability đang bật. |
| Giai đoạn 5 — Content phụ trợ | 2/3 | Cấu hình storage/transcode/moderation provider production trước khi bật video upload ngoài development. |
| Giai đoạn 6 — Scale và observability | 3/4 | Chạy load/chaos/reliability profile trong môi trường đại diện production và lưu artifact/SHA. Script/alert tồn tại chỉ là readiness của repository. |
| Frontend track | 3/5 | Guest-upgrade UX và các product decision còn mở ở Giai đoạn 2/4/5. • Browser/device E2E cho provider thật chỉ chạy khi môi trường/credential tồn tại; unit/mock PASS không được nâng thành production claim. |
| Public web, SEO và trust | 2/5 | Legal/product owner review nội dung terms, privacy, cookies và community guidelines trước khi công bố như chính sách pháp lý chính thức; bản source hiện là baseline theo behavior hiện có. • Sau khi có domain production: khai báo Search Console, gửi sitemap, kiểm tra rich result, canonical, Core Web Vitals và cập nhật `NEXT_PUBLIC_SITE_URL` đúng public web URL. • Đo organic acquisition và AI-search citations bằng dữ liệu thật trước khi tuyên bố SEO đã đạt mục tiêu; JSON-LD/SSR giúp máy hiểu nội dung nhưng không đảm bảo thứ hạng hay việc được trích dẫn. |

### B2. Ảnh chụp theo track

| Track                                          | Repository outcome                                                                                 | Khoảng trống không được suy diễn                                                       |
| ---------------------------------------------- | -------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------- |
| Foundation, Economy, Matching, Social, Content | Source/contract/test evidence đã có cho các capability được registry đánh dấu `implemented`.       | Registry không chứng minh production traffic, provider credential hoặc store approval. |
| Frontend                                       | Admin và Web có route/hợp đồng chính, generated API client và runtime capability gating.           | Một số provider/UX entry point vẫn deferred; mockup không phải backend contract.       |
| Scale/operations                               | K8s/Compose/hosted profiles, observability, load-test scaffold và reliability evidence gate đã có. | Multi-region thật, production capacity benchmark và SLA chưa được chứng minh bởi repo. |

### B3. Ưu tiên P0-P2 và điều kiện mở khoá (Giai đoạn 7)

| Priority | Outcome cần đạt                                           | Trigger mở khóa / evidence bắt buộc                                                                |
| -------- | --------------------------------------------------------- | -------------------------------------------------------------------------------------------------- |
| P0       | Provider readiness cho top-up, push, video và social auth | Credential/sandbox smoke, runtime `/capabilities`, owner và rollback rõ                            |
| P0       | Production SLO baseline và alert ownership                | `reliability:production-gate`, dashboard/alert links, observation window đủ dài theo runbook       |
| P1       | LiveKit capacity/headroom thật                            | Chạy profile đúng codec/bitrate/publisher/subscriber trên cluster mục tiêu; không dùng placeholder |
| P1       | Multi-region thứ hai                                      | Budget/cluster/domain thật, region code/URL thật, DNS/LB smoke và failover evidence                |
| P1       | Scale matcher/shard                                       | Queue depth, wait latency và contention theo region vượt SLO đã chốt                               |
| P2       | Tách module thành deployable                              | Ít nhất một tiêu chí § 3.4 + ADR + migration/rollback + observability; không chỉ vì module lớn     |
| P2       | Feed CQRS/read replica/cache mới                          | **số liệu traffic production thật** về read/write, latency, hit rate và freshness budget           |
| P2       | Thuật toán ranking/matching phức tạp hơn                  | Offline/online eval chứng minh uplift đủ bù fairness, explainability và vận hành                   |

### B4. Thang quy mô S0-S8

**Vị trí hiện tại theo evidence trong checkout:** có nền tảng và scaffold scale của Giai đoạn 6, đang cần hoàn tất các gate vận hành ở Giai đoạn 7. Bước kiểm chứng quy mô tiếp theo là pilot S0 → S1; chưa có capacity report được dẫn tại đây để chứng nhận một mốc CCU/RPS production. Số user thực tế và deployment đang chạy cần telemetry, không suy ra từ source/manifest.

| Mốc | User tích lũy tham khảo | Peak CCU minh họa | Mô hình cần đánh giá                                                         | Điều kiện mở công việc tiếp theo                                           |
| --- | ----------------------- | ----------------- | ---------------------------------------------------------------------------- | -------------------------------------------------------------------------- |
| S0  | Vài–100                 | Vài–10            | Development/pilot, một topology nhỏ với ba backend hiện hành                 | Luồng thật và recovery cơ bản được kiểm chứng; có baseline tải             |
| S1  | 100–1.000               | 1–10              | Alpha hosted-free hoặc single-node theo ADR hiện hành                        | Quota, availability hoặc latency của profile cản trở pilot; cần HA         |
| S2  | 1.000–10.000            | 10–100            | Một region, nhiều replica API/signaling khi cần, DB/Redis được bảo vệ        | Load qua LB, reconnect và failover đạt mục tiêu; xác định bottleneck       |
| S3  | 10.000–100.000          | 100–1.000         | HA theo các miền lỗi trong một region; scale riêng API/signaling/media       | Số liệu DB, queue, socket và media đòi hỏi tối ưu riêng từng lớp           |
| S4  | 100.000–1 triệu         | 1.000–10.000      | Tối ưu đường đọc và lifecycle dữ liệu; replica/cache/partition có chọn lọc   | Primary DB, storage hoặc fanout đạt giới hạn đã benchmark                  |
| S5  | 1–10 triệu              | 10.000–100.000    | Capacity theo region; DR/region thứ hai hoặc DB sharding nếu số liệu yêu cầu | Latency địa lý, recovery hoặc write capacity cần boundary mới + ADR        |
| S6  | 10–100 triệu            | 100.000–1 triệu   | Đề xuất các cụm độc lập (cell), placement và ownership dữ liệu rõ            | Một cụm không đáp ứng capacity/phạm vi lỗi; migration cell được kiểm chứng |
| S7  | 100 triệu–1 tỷ          | 1–10 triệu        | Đề xuất nhiều region/cell, vận hành và phân phối nội dung toàn cầu           | Capacity, chi phí và recovery chứng minh khả năng mở thêm từng cell        |
| S8  | 1–vài tỷ                | 10–vài chục triệu | Kịch bản quy mô toàn cầu; mở rộng bằng các đơn vị đã benchmark               | Chỉ cam kết tải cụ thể sau báo cáo capacity, dữ liệu và ngân sách thực tế  |

Ưu tiên ngay: chốt workload pilot và owner → xác minh provider theo feature bật → đo API, Socket.IO và media thật → diễn tập restore/reconnect → lưu capacity report cùng SHA. Production promotion tiếp tục theo [reliability gate](https://github.com/ducmanh001/litmatch/blob/main/docs/runbooks/reliability-slo-and-evidence.md), không được thay bằng bảng mốc user này.

Chi tiết: [pack/project/capacity-plan.md](pack/project/capacity-plan.md).

### B5. Quyết định đã hoãn (docs/feature-registry.json)

| id | trạng thái | owner | lý do |
|---|---|---|---|
| production-cache-policy | deferred | architecture/platform | Redis and derived ranking are already documented as projections or transport support. No production read/write, hit-rate, latency, or eviction evidence is checked into this repository to justify a new cache layer or TTL policy. |
| algorithm-upgrades | deferred | product/domain owners | Matching shard expansion, feed CQRS and richer video ranking need observed traffic and SLO evidence. The current code keeps the documented bounded algorithms rather than adding speculative infrastructure. |
| short-video-reporting-ui | deferred | web/short-video with core-api/safety | The backend report contract and web API hook remain, but the visible report action was deliberately removed from the current reel UI. Do not claim the client feature is implemented until a product decision re-enables it and a behavior test verifies the user a… |

## C. Việc còn mở

Loại A = lỗi / sự cố thực tế; B = thiết kế, quy trình, quan sát từ đọc code; C = báo động giả.

### C1. Sổ lỗi: đang mở hoặc đang xử lý (16)

| id | trạng thái | loại | khu vực | ngày | vấn đề |
|---|---|---|---|---|---|
| [P085](https://ducmanh001.github.io/litmatch-error-history/issues/P085.md) | open | A | frontend-ux | 2026-10-09 | Guest reload mất device token → Matching 401 |
| [P078](https://ducmanh001.github.io/litmatch-error-history/issues/P078.md) | open | A | dependencies | 2026-09-09 | PR Dependabot đỏ vì chính sách minimumReleaseAge của pnpm |
| [P077](https://ducmanh001.github.io/litmatch-error-history/issues/P077.md) | open | A | windows-toolchain | 2026-09-03 | Test hard-kill của run-stage lỗi trên Windows (exit 124) |
| [P075](https://ducmanh001.github.io/litmatch-error-history/issues/P075.md) | open | A | auth-security | 2026-09-03 | OTP trả mã ngay trong response khi production bật OTP |
| [P074](https://ducmanh001.github.io/litmatch-error-history/issues/P074.md) | in-progress | A | deploy-config | 2026-08-21 | Core API production crash lúc boot (media storage dev / thiếu biến R2) |
| [P065](https://ducmanh001.github.io/litmatch-error-history/issues/P065.md) | open | A | deploy-config | 2026-08-19 | Production chạy bản cũ: route profile 404, thông báo Facebook cũ |
| [P063](https://ducmanh001.github.io/litmatch-error-history/issues/P063.md) | open | A | ci-cd | 2026-08-14 | Test frontend (admin/web) timeout lặp lại |
| [P012](https://ducmanh001.github.io/litmatch-error-history/issues/P012.md) | open | A | auth-security | 2026-07-16 | CodeQL: loop-bound-injection (high) ở common/hash/fnv1a.ts còn mở |
| [P104](https://ducmanh001.github.io/litmatch-error-history/issues/P104.md) | open | B | other | 2026-10-09 | soul-match end phát lại soul.ended khi replay |
| [P103](https://ducmanh001.github.io/litmatch-error-history/issues/P103.md) | open | B | other | 2026-10-09 | Phân trang cursor video-queue admin (pending_review tăng dần nhưng cursor dùng less-than; suy luận) |
| [P102](https://ducmanh001.github.io/litmatch-error-history/issues/P102.md) | open | B | other | 2026-10-09 | Comment k8s lỗi thời |
| [P101](https://ducmanh001.github.io/litmatch-error-history/issues/P101.md) | open | B | other | 2026-10-09 | Lời mời hết hạn kiểu lazy-expire không được persist |
| [P100](https://ducmanh001.github.io/litmatch-error-history/issues/P100.md) | open | B | other | 2026-10-09 | getHiddenUserIds không có cửa sổ thời gian (cần quyết định sản phẩm) |
| [P099](https://ducmanh001.github.io/litmatch-error-history/issues/P099.md) | open | B | other | 2026-10-09 | Ranked sort loại hàng có rankScore IS NULL |
| [P094](https://ducmanh001.github.io/litmatch-error-history/issues/P094.md) | open | B | matching | 2026-10-09 | Giới hạn đã biết: advisory lock Palm/Movie Match, outbox chưa có consumer/retention |
| [P055](https://ducmanh001.github.io/litmatch-error-history/issues/P055.md) | open | B | ci-cd | 2026-08-14 | Chưa bắt buộc required checks: PR vẫn merge được khi CI đỏ |

### C2. Sổ lỗi: đã giảm nhẹ nhưng chưa dứt điểm (8)

| id | trạng thái | loại | khu vực | ngày | vấn đề |
|---|---|---|---|---|---|
| [P092](https://ducmanh001.github.io/litmatch-error-history/issues/P092.md) | mitigated | A | windows-toolchain | 2026-10-09 | Windows thiếu zip/unzip nên docs:check không chạy |
| [P015](https://ducmanh001.github.io/litmatch-error-history/issues/P015.md) | mitigated | A | realtime-gateway | 2026-07-18 | Socket reconnect không khôi phục state bền (LRN-005) |
| [P011](https://ducmanh001.github.io/litmatch-error-history/issues/P011.md) | mitigated | A | database-concurrency | 2026-07-13 | Transaction Postgres bị aborted sau unique violation (LRN-002) |
| [P072](https://ducmanh001.github.io/litmatch-error-history/issues/P072.md) | mitigated | B | windows-toolchain | 2026-08-20 | Windows: Nx build lỗi 1314 + runner timeout (exit 124) |
| [P061](https://ducmanh001.github.io/litmatch-error-history/issues/P061.md) | mitigated | B | observability | 2026-08-14 | Lỗi TS ở tsconfig.spec không làm build fail |
| [P051](https://ducmanh001.github.io/litmatch-error-history/issues/P051.md) | mitigated | B | ai-agent-process | 2026-08-14 | ci:preflight treo làm mất thay đổi chưa commit |
| [P050](https://ducmanh001.github.io/litmatch-error-history/issues/P050.md) | mitigated | B | ai-agent-process | 2026-08-14 | Không push được vì môi trường (thiếu gh, Docker/WSL2, hook) |
| [P016](https://ducmanh001.github.io/litmatch-error-history/issues/P016.md) | mitigated | B | ai-agent-process | 2026-07-18 | PASS cục bộ bị hiểu thành PASS toàn repo (LRN-006) |

### C3. Quan sát của service map chưa xử lý (8/13)

| id | trạng thái | service | quan sát |
|---|---|---|---|
| V8 | confirmed | short-video | sort=ranked loại video chưa có rankScore dù comment nói fallback |
| V12 | confirmed | safety | getHiddenUserIds đọc mọi report vĩnh viễn? |
| V13 | confirmed | matching | Lazy-expire lời mời có thể không được lưu? |
| M1 | confirmed | media-server | Chú thích đầu file k8s của media-server lỗi thời so với cấu hình |
| M2 | confirmed | matching, signaling-gateway | Sự kiện match.matched được khai báo nhưng không có nơi nào phát |
| M3 | confirmed | movie-match, signaling-gateway | Movie Match ẩn danh phát realtime bên trong transaction, trái với tài liệu |
| M4 | confirmed | auth, app-shell | AUTH_MIN_AGE và DEPLOY_ENVIRONMENT có trong .env.example nhưng không có schema hay mã đọc |
| M5 | confirmed | signaling-gateway | Đặc tả AsyncAPI trong repo chỉ liệt kê 8 trong 26 sự kiện realtime |

Chi tiết: [pack/service-map/findings.md](pack/service-map/findings.md).

## D. Lộ trình của atlas

### D1. Đã phủ

| lớp | số lượng | nơi xem |
|---|---|---|
| Module / service | 25 | [pack/modules/index.md](pack/modules/index.md) |
| Bảng database | 70 | [pack/db/index.md](pack/db/index.md) |
| REST handler | 196 | [pack/api/index.md](pack/api/index.md) |
| Biến môi trường | 207 | [pack/config/index.md](pack/config/index.md) |
| Mã lỗi | 182 | [pack/errors/index.md](pack/errors/index.md) |
| Realtime event | 26 | [pack/events/index.md](pack/events/index.md) |
| Route frontend | 46 (web 36, admin 10) | [pack/fe/index.md](pack/fe/index.md) |
| Luồng xuyên service | 72 | [pack/flows/index.md](pack/flows/index.md) |
| Entrypoint phủ | 214 (luồng 188, cục bộ 26) | [pack/lookup/entrypoint-to-flow.md](pack/lookup/entrypoint-to-flow.md) |
| Lát cắt tính năng | 21 | [pack/slices/index.md](pack/slices/index.md) |
| Vấn đề trong sổ lỗi | 108 | [pack/issues/index.md](pack/issues/index.md) |

### D2. Phần do agent viết đọc từ code

| phần | trạng thái | tệp |
|---|---|---|
| async | có; bằng chứng file:line đã kiểm tự động, chưa kiểm chứng độc lập | [pack/system/async/index.md](pack/system/async/index.md) |
| state | có; bằng chứng file:line đã kiểm tự động, chưa kiểm chứng độc lập | [pack/system/state/index.md](pack/system/state/index.md) |
| domain | có; bằng chứng file:line đã kiểm tự động, chưa kiểm chứng độc lập | [pack/system/domain/index.md](pack/system/domain/index.md) |
| cicd | có; bằng chứng file:line đã kiểm tự động, chưa kiểm chứng độc lập | [pack/system/cicd/index.md](pack/system/cicd/index.md) |
| statemachines | chưa viết | - |
| infra | chưa viết | - |
| observability | chưa viết | - |
| integrations | chưa viết | - |

### D3. Việc tiếp theo của atlas

- Kiểm chứng độc lập các phần async, cicd, state, domain bằng một lượt agent thứ hai.
- Viết các phần còn thiếu: statemachines, infra, observability, integrations.
- Đưa bộ sinh vào repo litmatch (đề xuất: scripts/docs/generate.mjs với --check trong CI, .agents/context-map.json) để pack tự lệch-báo khi code đổi.
- Thêm bằng chứng chạy thật mà phân tích tĩnh không thấy: cấu hình production, dashboard, tải thật.

Các trang cùng bộ: [Atlas](https://ducmanh001.github.io/litmatch-atlas-demo/) · [Service Map](https://ducmanh001.github.io/litmatch-service-map/) · [Error History](https://ducmanh001.github.io/litmatch-error-history/). Roadmap dự án và việc còn mở nằm ở [roadmap của Atlas](https://ducmanh001.github.io/litmatch-atlas-demo/roadmap.md).
