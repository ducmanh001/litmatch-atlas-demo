# Module gift

id: module:gift
kind: core-api module
code: apps/core-api/src/modules/gift

Sở hữu catalog quà (bảng gifts, giá là DATA) và GiftEvent (gift_events) cho 3 ngữ cảnh tặng: phòng Party, tác giả video, mở chat từ profile; tính tỉ lệ PTS và điều phối side effect quanh giao dịch. KHÔNG sở hữu sổ cái/ví (Economy ghi LedgerEntry), không sở hữu Conversation/membership phòng/video/notification (mỗi module đó tự ghi).

## Facts

- tables owned: gift_events, gifts
- API: 4 endpoints, 0 public, 0 admin-only, 3 require Idempotency-Key, 3 with own throttle
- publishes realtime: gift.sent
- env vars read: GIFT_POINTS_RATE_PERCENT, REDIS_URL
- error codes: 5 in GiftErrors
- tests: 0 unit spec files, 1 integration spec files
- history: 16 commits (0 fixes), last 2026-08-19
- docs: docs/services/gift-service.md (last changed 2026-08-19)
- features: gift

## Where to read next

- tables: ../db/gift.md
- API contract: ../api/gift.md
- why it talks to other services: ../service-map/gift.md and ../service-map/gift-techniques.md
- error codes: ../errors/gifterrors.md
- flows involving it: [admin-gift-catalog](../flows/admin-gift-catalog.md), [gift-in-party-room](../flows/gift-in-party-room.md), [gift-on-video](../flows/gift-on-video.md), [gift-to-profile-chat-gate](../flows/gift-to-profile-chat-gate.md)
