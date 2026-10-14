# Async flows: realtime, outbox, jobs, queues: idleGating

id: system:async:idleGating

Written by an AI agent from the code; every file:line quote was checked automatically (exists within ±3 lines) but NO independent verification pass has run yet. Treat claims as strong leads; open the cited file before editing.

- what: Cổng "idle" cục bộ theo process core-api: job đặt `skipWhenIdle: true` bỏ tick khi 5 phút gần nhất không có request nghiệp vụ nào.

- mechanism: ResponseEnvelopeInterceptor (APP_INTERCEPTOR toàn cục) gọi markRuntimeActivity(url) cho mỗi request tới handler, trừ /health, /metrics, /swagger; isRuntimeActive() chỉ true nếu có request trong 5 phút (vừa khởi động là idle). Tick bị bỏ được đếm bằng recordRuntimeBackgroundSkip(jobName) và export thành metric runtime_background_skipped{job}.

- effect: 6 job backstop bỏ tick khi idle: matching-matcher, matching-ticket-sweeper, matching-invite-sweeper, story-sweeper, short-video-ranking, short-video-sweeper; calling-ticker, party-room-*, economy-* và friend-streak-warning luôn chạy, còn matching vẫn được đánh thức bởi enqueue. Gate theo từng process: request bị guard/throttle từ chối không tính, webhook công khai tới được handler thì có tính.

### evidence

### #1

- file: apps/core-api/src/common/runtime/runtime-activity.ts
- line: 10
- quote: export const RUNTIME_ACTIVITY_IDLE_AFTER_MS = 5 * 60 * 1_000;

### #2

- file: apps/core-api/src/common/runtime/runtime-activity.ts
- line: 18
- quote: if (isProbePath(path)) return;

### #3

- file: apps/core-api/src/common/runtime/runtime-activity.ts
- line: 113
- quote: lastMeaningfulRequestAt !== undefined &&

### #4

- file: apps/core-api/src/common/interceptors/response-envelope.interceptor.ts
- line: 26
- quote: markRuntimeActivity(request.originalUrl ?? request.url ?? '');

### #5

- file: apps/core-api/src/app/app.module.ts
- line: 157
- quote: { provide: APP_INTERCEPTOR, useClass: ResponseEnvelopeInterceptor },

### #6

- file: apps/core-api/src/common/scheduling/managed-interval.ts
- line: 85
- quote: if (options.skipWhenIdle && !isRuntimeActive()) {

### #7

- file: apps/core-api/src/common/scheduling/managed-interval.ts
- line: 86
- quote: recordRuntimeBackgroundSkip(options.jobName);

### #8

- file: apps/core-api/src/common/runtime/runtime-activity.ts
- line: 97
- quote: const skipGauge = meter.createObservableGauge('runtime_background_skipped', {

### #9

- file: docs/runbooks/hosted-free-release.md
- line: 60
- quote: Core API coi process là **idle** sau 5 phút không có request nghiệp vụ;

### #10

- file: docs/runbooks/hosted-free-release.md
- line: 63
- quote: call ticker, reconciliation và outbox không bị gate
