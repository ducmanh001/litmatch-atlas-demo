# Async flows: realtime, outbox, jobs, queues: limits

id: system:async:limits

Written by an AI agent from the code; every file:line quote was checked automatically (exists within ±3 lines) but NO independent verification pass has run yet. Treat claims as strong leads; open the cited file before editing.

- Chỉ đọc tĩnh mã/config trong repo: không chạy hệ thống nên mọi hành vi vận hành (độ trễ, backlog outbox, tần suất mất event, số pod thực tế) ghi theo mã, chưa đo.
- Cấu hình Kafka production (partition, retention, cách tạo topic `litmatch.economy.events` và `.DLQ`, SASL) nằm ngoài repo; chỉ docker-compose dev bật KAFKA_AUTO_CREATE_TOPICS_ENABLE và relay đang tắt ở mọi profile trong repo.
- Thời gian relay giữ transaction khi Kafka down được suy từ cấu hình (publish tuần tự × EVENT_BUS_KAFKA_REQUEST_TIMEOUT_MS × (EVENT_BUS_KAFKA_RETRIES+1)), chưa kiểm chứng bằng chạy thật.
- LiveKit có gửi lại webhook hay không, và theo lịch nào, là hành vi của LiveKit (ngoài repo); mã chỉ giả định handler idempotent và có backstop (RoomService, sweeper).
- Số replica thực tế của core-api/signaling-gateway và chính sách HPA nằm ở môi trường triển khai; mã chỉ cho thấy cơ chế an toàn nhiều pod (advisory lock, SKIP LOCKED, ZPOPMIN, Lua quota, server.local).
- Adapter push (NotificationService.sendPush, Web Push) chỉ được nêu như bước best-effort sau commit; chưa phân tích provider, retry hay hàng đợi của chúng.
- Mobile native (gửi IAP receipt) và hạ tầng ngoài repo (Upstash Redis, Northflank, LiveKit Cloud) không được xem; apps/admin không dùng realtime (đã grep).
- loadtest/*.js (signaling-ws, matching-queue, calling-flow) chưa được đọc nên chưa có số liệu giới hạn của Socket.IO hay hàng đợi ghép.
- Thứ tự giữa các event khác nhau của cùng một thao tác (vd call.ended rồi notification) và khả năng publish trùng khi client retry (friend.message) chưa được phân tích từng cặp ngoài những trường hợp đã ghi ở realtimeEvents.
- Yêu cầu gốc nhắc "ticker billing" cho call: mã và docs/services/calling-service.md cho thấy ticker không tính phí theo phút (chỉ enforce timeout/free window 420s); không có luồng billing để mô tả.
