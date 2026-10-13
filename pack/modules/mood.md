# Module mood

id: module:mood
kind: core-api module
code: apps/core-api/src/modules/mood

Sở hữu catalog mood_presets và log append-only mood_status_events (set/clear), suy ra mood hiện tại lúc đọc dựa trên dòng mới nhất và expiresAt. Không sở hữu block/report (Safety), không có free-text/moderation, không phát realtime và không có job dọn.

## Facts

- tables owned: mood_presets, mood_status_events
- API: 5 endpoints, 0 public, 0 admin-only, 2 require Idempotency-Key, 0 with own throttle
- env vars read: MOOD_STATUS_TTL_HOURS
- error codes: 1 in MoodErrors
- tests: 1 unit spec files, 1 integration spec files
- history: 1 commits (0 fixes), last 2026-07-17
- docs: docs/services/mood-service.md (last changed 2026-07-17)
- features: avatar-discovery-mood

## Where to read next

- tables: ../db/mood.md
- API contract: ../api/mood.md
- why it talks to other services: ../service-map/mood.md and ../service-map/mood-techniques.md
- error codes: ../errors/mooderrors.md
- flows involving it: [mood-status-visibility](../flows/mood-status-visibility.md)
