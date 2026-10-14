# Module notification

id: module:notification
kind: core-api module
code: apps/core-api/src/modules/notification

Sở hữu bảng notifications (in-app, cursor theo seq) và web_push_subscriptions; là điểm duy nhất các module nghiệp vụ gọi để ghi thông báo (createWithManager/create) rồi đẩy push best-effort (sendPush). Không sở hữu nội dung hiển thị (payload tối thiểu, client tự render), không có realtime event riêng và không publish Redis.

## Facts

- tables owned: notifications, web_push_subscriptions
- API: 5 endpoints, 0 public, 0 admin-only, 0 require Idempotency-Key, 0 with own throttle
- error codes: 2 in NotificationErrors
- tests: 2 unit spec files, 0 integration spec files
- history: 7 commits (1 fixes), last 2026-08-20
- docs: docs/services/notification-service.md (last changed 2026-08-20)
- features: notification

## Where to read next

- tables: ../db/notification.md
- API contract: ../api/notification.md
- why it talks to other services: ../service-map/notification.md and ../service-map/notification-techniques.md
- error codes: ../errors/notificationerrors.md
- flows involving it: [admin-broadcast-notification](../flows/admin-broadcast-notification.md), [feed-comment-with-notification](../flows/feed-comment-with-notification.md), [feed-reaction-with-notification](../flows/feed-reaction-with-notification.md), [friend-streak-warning-job](../flows/friend-streak-warning-job.md), [gift-in-party-room](../flows/gift-in-party-room.md), [gift-on-video](../flows/gift-on-video.md), [gift-to-profile-chat-gate](../flows/gift-to-profile-chat-gate.md), [matching-invite-create](../flows/matching-invite-create.md), [message-push-notification](../flows/message-push-notification.md)

## Linked issues (heuristic)

- P006 [fixed] Swagger thiếu cursor pagination → client sinh kiểu never

See ../issues/index.md.
