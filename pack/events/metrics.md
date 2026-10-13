# Metrics emitted

id: events:metrics

| name | instrument | unit | description | file |
|---|---|---|---|---|
| event_bus_publish_total | Counter |  | Events sent to the broker by outcome and topic. | apps/core-api/src/common/events/kafka-event-bus.adapter.ts:85 |
| event_bus_publish_failure_total | Counter |  | Event publish failures handed back to the outbox relay. | apps/core-api/src/common/events/kafka-event-bus.adapter.ts:88 |
| event_bus_consumer_failure_total | Counter |  | Consumer handler failures before retry or dead-letter handling. | apps/core-api/src/common/events/kafka-event-bus.adapter.ts:94 |
| event_bus_dead_letter_total | Counter |  | Events moved to a broker dead-letter topic. | apps/core-api/src/common/events/kafka-event-bus.adapter.ts:101 |
| runtime_active | ObservableGauge | 1 | Whether recent business/API traffic keeps the runtime active | apps/core-api/src/common/runtime/runtime-activity.ts:64 |
| runtime_last_meaningful_request_age_seconds | ObservableGauge | s | Age of the last non-probe request | apps/core-api/src/common/runtime/runtime-activity.ts:72 |
| runtime_meaningful_requests | ObservableGauge | 1 | Business/API requests that keep best-effort jobs warm | apps/core-api/src/common/runtime/runtime-activity.ts:84 |
| runtime_background_skipped | ObservableGauge | 1 | Best-effort background ticks skipped while runtime is idle | apps/core-api/src/common/runtime/runtime-activity.ts:97 |
| call_ended_total | Counter |  | Tổng số call kết thúc, theo lý do (CallEndReason) | apps/core-api/src/modules/calling/calling.metrics.ts:19 |
| economy_transaction_total | Counter |  | Tổng giao dịch ledger, theo type và kết quả (success\|replayed\|failed) | apps/core-api/src/modules/economy/economy.metrics.ts:36 |
| economy_reconciliation_mismatch_total | Counter |  | Số lệch đối soát phát hiện được, theo check và currency. Alert: increase(...) > 0. | apps/core-api/src/modules/economy/economy.metrics.ts:40 |
| economy_reconciliation_last_run_status | ObservableGauge | 1 | Trạng thái run đối soát gần nhất theo tier: 1 = cân, 0 = lệch hoặc lỗi | apps/core-api/src/modules/economy/economy.metrics.ts:47 |
| economy_reconciliation_run_duration_seconds | Histogram | s | Thời gian 1 run đối soát (giây) theo tier — theo dõi chi phí khi ledger lớn dần | apps/core-api/src/modules/economy/economy.metrics.ts:60 |
| matching_ticket_wait_seconds | Histogram | s | Thời gian ticket chờ từ enqueue tới matched (giây), theo matchType | apps/core-api/src/modules/matching/matching.metrics.ts:17 |
| http_request_duration_seconds | Histogram | s | Thời gian xử lý HTTP request tính bằng giây, theo method/route/status_code | libs/observability/src/lib/http-metrics.ts:17 |
| litmatch_up | ObservableGauge | 1 | Process đang chạy và có thể export metrics | libs/observability/src/lib/metrics-registry.ts:17 |
| process_uptime_seconds | ObservableGauge | s | Thời gian process đã chạy, tính bằng giây | libs/observability/src/lib/metrics-registry.ts:23 |
| process_resident_memory_bytes | ObservableGauge | By | Resident memory của process, tính bằng bytes | libs/observability/src/lib/metrics-registry.ts:31 |
