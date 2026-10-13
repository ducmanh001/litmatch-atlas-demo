# Issues in area observability

id: issues:observability

### P017: OTLP exporter nhận endpoint Prometheus remote_write (LRN-007)

status: fixed; tier: A; date: 2026-07-18; confidence: high

- problem: OTLP metrics exporter nhận endpoint Prometheus remote_write nên gửi OTLP tới endpoint không tương thích và export hỏng.
- root cause: Tên biến môi trường làm mờ giao thức của endpoint.
- resolution: Resolver trong libs/observability/src/lib/tracing.ts từ chối /api/prom/push, chấp nhận dạng endpoint OTLP; có tracing.spec.ts.
- evidence: doc docs/reference/lessons-registry.md, commit 07c90f9, doc docs/reference/project-handoff.md

### P031: Wrapper OTel cho OpenNext Cloudflare handler lỗi sau khi thêm Loki

status: fixed; tier: A; date: 2026-08-02; confidence: low

- problem: Wrapper OTel cho OpenNext Cloudflare handler lỗi 'do not object cloudflare' sau khi thêm Loki export.
- root cause: Chưa ro: diff 10 dòng ở apps/web/open-next.config.ts.
- resolution: Sửa open-next.config.ts (#58) để wrapper @microlabs/otel-cf-workers tương thích Cloudflare.
- evidence: commit 59356ec

### P061: Lỗi TS ở tsconfig.spec không làm build fail

status: mitigated; tier: B; date: 2026-08-14; confidence: high

- problem: Lỗi/cảnh báo TypeScript ở tsconfig.spec.json của signaling-gateway vẫn cho build qua; user muốn tắt tạm cảnh báo.
- root cause: Target build chỉ chạy webpack-cli với tsconfig.app.json, không có target typecheck; VS Code có thể dùng TypeScript khác bản workspace 5.9.3.
- resolution: Tạm thời ép VS Code dùng TS workspace qua .vscode/settings.json (typescript.tsdk); ignoreDeprecations 6.0 bị gỡ vì không hợp TS 5.9. Đề xuất thêm target typecheck cho build phụ thuộc, chưa làm.
- evidence: codex-thread 019fffde, file .vscode/settings.json

### P081: Báo động giả: /metrics trả 404 (đúng thiết kế OTLP)

status: false-alarm; tier: C; date: 2026-10-09; confidence: high

- problem: Prometheus /metrics public trả 404.
- root cause: Đúng thiết kế: core-api/signaling-gateway đẩy metrics OTLP lên Grafana, Alloy chỉ scrape LiveKit; không expose /metrics.
- resolution: Kết luận không phải lỗi, không mở /metrics public; chỉ cần xác nhận biến GRAFANA_CLOUD_* và log '[metrics] gửi thành công'.
- evidence: codex-thread 01a11dea, file observability/alloy/config.alloy
