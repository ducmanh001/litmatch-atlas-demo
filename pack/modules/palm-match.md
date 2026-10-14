# Module palm-match

id: module:palm-match
kind: core-api module
code: apps/core-api/src/modules/palm-match

Sở hữu hàng đợi ghép cặp ẩn danh Palm Match, phiên 2 người (lật bài, độ hợp, đánh giá like/skip) và kho nội dung bói palm_reading_templates. Không sở hữu quan hệ bạn bè/Conversation (Friend tạo), không sở hữu block/report (Safety quyết định), không chạm Economy và không phát realtime.

## Facts

- tables owned: palm_match_active_participants, palm_match_queue_entries, palm_match_sessions, palm_reading_templates
- API: 6 endpoints, 0 public, 0 admin-only, 0 require Idempotency-Key, 0 with own throttle
- env vars read: PALM_MATCH_QUEUE_MAX_WAIT_SECONDS, PALM_MATCH_SESSION_DURATION_SECONDS, PALM_MATCH_TARGET_NAME_MAX_LENGTH
- error codes: 6 in PalmMatchErrors
- tests: 1 unit spec files, 1 integration spec files
- history: 3 commits (0 fixes), last 2026-08-13
- docs: docs/services/palm-match-service.md (last changed 2026-10-09)
- features: palm-match

## Where to read next

- tables: ../db/palm-match.md
- API contract: ../api/palm-match.md
- why it talks to other services: ../service-map/palm-match.md and ../service-map/palm-match-techniques.md
- error codes: ../errors/palmmatcherrors.md
- flows involving it: [palm-match-queue-and-rating](../flows/palm-match-queue-and-rating.md)

## Linked issues (heuristic)

- P094 [open] Giới hạn đã biết: advisory lock Palm/Movie Match, outbox chưa có consumer/retention
- P005 [fixed] CodeQL: loop-bound-injection (high) ở palm-match.hash.ts

See ../issues/index.md.
