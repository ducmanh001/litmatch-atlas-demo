# Feature registry

id: project:feature-registry
source: docs/feature-registry.json @ d3cb28c (the code-backed status of every feature; roadmap says what comes next)

| id | status | owner | title |
|---|---|---|---|
| auth-session | implemented | core-api/auth | Guest, OTP, social login, identity upgrade and rotating browser sessions |
| runtime-capabilities | implemented | core-api/app with admin/web consumers | Server-derived runtime availability contract for frontend providers |
| economy-ledger | implemented | core-api/economy | Wallet, IAP catalog, VIP and double-entry ledger |
| matching | implemented | core-api/matching | Queue ticket, pairing, guest quota and paid speed-up |
| soul-match | implemented | core-api/soul-match | Anonymous text match, rating and friendship unlock |
| calling | implemented | core-api/calling | Voice-call lifecycle and settlement |
| party-room | implemented | core-api/party-room | Party-room lifecycle, LiveKit token and member roles |
| gift | implemented | core-api/gift | Party-room gifts through Economy |
| friend-chat-streak | implemented | core-api/friend | Friendship, direct chat and streak |
| feed-stories | implemented | core-api/feed | Feed, reactions, comments and expiring stories |
| safety | implemented | core-api/safety | Report, block and trust-safety decisions |
| avatar-discovery-mood | implemented | core-api/avatar, core-api/discovery, core-api/mood | Avatar, discovery/nearby and mood |
| notification | implemented | core-api/notification | In-process notification dispatch with isolated push and analytics adapters |
| support-tickets | implemented | core-api/support with core-api/admin | Idempotent support tickets with customer tracking and staff workflow |
| mini-game | implemented | core-api/mini-game | Two-player rock-paper-scissors mini game |
| movie-match | implemented | core-api/movie-match | Shared video playback and anonymous movie match |
| palm-match | implemented | core-api/palm-match | Deterministic palm reading and anonymous palm match |
| short-video | implemented | core-api/short-video | Short-video lifecycle, moderation and derived ranking |
| user-profile-open-entry | implemented | core-api/user | Optional profile birth date without an age-based access gate |
| privacy-analytics-consent | implemented | web/shared-analytics | Hosted analytics with quota controls and an explicit privacy warning |
| realtime-fanout | implemented | signaling-gateway with core-api producers | Authenticated Socket.IO fanout and distributed connection quota through Redis |

## Features

### auth-session

- title: Guest, OTP, social login, identity upgrade and rotating browser sessions
- status: implemented
- owner: core-api/auth
- contracts: openapi/core-api.json, docs/06-domain-rules.md, docs/adr/0007-httponly-cookie-refresh-token.md
- evidence: apps/core-api/src/modules/auth/auth.controller.ts, apps/core-api/src/modules/auth/controllers/auth-upgrade.controller.ts
- verification: {"kind":"automated-test-source","evidence":[{"path":"apps/core-api/src/modules/auth/auth.service.spec.ts","contains":"social upgrade giữ userId"}]}

### runtime-capabilities

- title: Server-derived runtime availability contract for frontend providers
- status: implemented
- owner: core-api/app with admin/web consumers
- contracts: openapi/core-api.json, docs/12-frontend-architecture.md, .env.example
- evidence: apps/core-api/src/app/capabilities.controller.ts, apps/core-api/src/app/capabilities.service.ts
- verification: {"kind":"automated-test-source","evidence":[{"path":"apps/core-api/src/app/capabilities.service.spec.ts","contains":"derives configured providers"},{"path":"apps/web/src/shared/capabilities/api.spec.tsx","contains":"capabilities"}]}

### economy-ledger

- title: Wallet, IAP catalog, VIP and double-entry ledger
- status: implemented
- owner: core-api/economy
- contracts: openapi/core-api.json, docs/services/economy-service.md, docs/06-domain-rules.md
- evidence: apps/core-api/src/modules/economy/economy.controller.ts, apps/core-api/src/modules/economy/services/ledger.service.ts
- verification: {"kind":"automated-test-source","evidence":[{"path":"apps/core-api/src/modules/economy/economy.integration.spec.ts","contains":"Economy"}]}

### matching

- title: Queue ticket, pairing, guest quota and paid speed-up
- status: implemented
- owner: core-api/matching
- contracts: openapi/core-api.json, docs/services/matching-service.md, docs/06-domain-rules.md
- evidence: apps/core-api/src/modules/matching/matching.controller.ts, apps/core-api/src/modules/matching/jobs/matcher-worker.service.ts, apps/core-api/src/modules/matching/services/guest-match-quota.service.ts
- verification: {"kind":"automated-test-source","evidence":[{"path":"apps/core-api/src/modules/matching/matching.integration.spec.ts","contains":"guest quota: concurrent guest mới cùng IP"}]}

### soul-match

- title: Anonymous text match, rating and friendship unlock
- status: implemented
- owner: core-api/soul-match
- contracts: openapi/core-api.json, docs/services/soul-match-service.md, docs/services/realtime-gateway.md
- evidence: apps/core-api/src/modules/soul-match/soul-match.controller.ts
- verification: {"kind":"automated-test-source","evidence":[{"path":"apps/core-api/src/modules/soul-match/soul-match.integration.spec.ts","contains":"Soul"}]}

### calling

- title: Voice-call lifecycle and settlement
- status: implemented
- owner: core-api/calling
- contracts: openapi/core-api.json, docs/services/calling-service.md, docs/06-domain-rules.md
- evidence: apps/core-api/src/modules/calling/calling.controller.ts
- verification: {"kind":"automated-test-source","evidence":[{"path":"apps/core-api/src/modules/calling/calling.integration.spec.ts","contains":"Calling"}]}

### party-room

- title: Party-room lifecycle, LiveKit token and member roles
- status: implemented
- owner: core-api/party-room
- contracts: openapi/core-api.json, docs/services/party-room-service.md, docs/services/realtime-gateway.md
- evidence: apps/core-api/src/modules/party-room/party-room.controller.ts
- verification: {"kind":"automated-test-source","evidence":[{"path":"apps/core-api/src/modules/party-room/party-room.integration.spec.ts","contains":"Party"}]}

### gift

- title: Party-room gifts through Economy
- status: implemented
- owner: core-api/gift
- contracts: openapi/core-api.json, docs/services/gift-service.md, docs/services/economy-service.md
- evidence: apps/core-api/src/modules/gift/gift.controller.ts
- verification: {"kind":"automated-test-source","evidence":[{"path":"apps/core-api/src/modules/gift/gift.integration.spec.ts","contains":"Gift"}]}

### friend-chat-streak

- title: Friendship, direct chat and streak
- status: implemented
- owner: core-api/friend
- contracts: openapi/core-api.json, docs/services/friend-service.md, docs/services/streak-service.md
- evidence: apps/core-api/src/modules/friend/friend.controller.ts, apps/core-api/src/modules/friend/services/streak.service.ts
- verification: {"kind":"automated-test-source","evidence":[{"path":"apps/core-api/src/modules/friend/friend.integration.spec.ts","contains":"Friend"}]}

### feed-stories

- title: Feed, reactions, comments and expiring stories
- status: implemented
- owner: core-api/feed
- contracts: openapi/core-api.json, docs/services/feed-service.md
- evidence: apps/core-api/src/modules/feed/feed.controller.ts, apps/core-api/src/modules/feed/controllers/story.controller.ts
- verification: {"kind":"automated-test-source","evidence":[{"path":"apps/core-api/src/modules/feed/feed.integration.spec.ts","contains":"Feed"}]}

### safety

- title: Report, block and trust-safety decisions
- status: implemented
- owner: core-api/safety
- contracts: openapi/core-api.json, docs/services/safety-service.md, docs/06-domain-rules.md
- evidence: apps/core-api/src/modules/safety/safety.controller.ts
- verification: {"kind":"automated-test-source","evidence":[{"path":"apps/core-api/src/modules/safety/safety.service.spec.ts","contains":"SafetyService"}]}

### avatar-discovery-mood

- title: Avatar, discovery/nearby and mood
- status: implemented
- owner: core-api/avatar, core-api/discovery, core-api/mood
- contracts: openapi/core-api.json, docs/services/avatar-service.md, docs/services/discovery-service.md, docs/services/mood-service.md
- evidence: apps/core-api/src/modules/avatar/avatar.controller.ts, apps/core-api/src/modules/discovery/discovery.controller.ts, apps/core-api/src/modules/mood/mood.controller.ts
- verification: {"kind":"automated-test-source","evidence":[{"path":"apps/core-api/src/modules/avatar/avatar.integration.spec.ts","contains":"Avatar"},{"path":"apps/core-api/src/modules/discovery/discovery.integration.spec.ts","contains":"Discovery"},{"path":"apps/core-api/src/modules/mood/mood.integration.spec.ts","contains":"Mood"}]}

### notification

- title: In-process notification dispatch with isolated push and analytics adapters
- status: implemented
- owner: core-api/notification
- contracts: openapi/core-api.json, docs/services/notification-service.md
- evidence: apps/core-api/src/modules/notification/notification.controller.ts
- verification: {"kind":"automated-test-source","evidence":[{"path":"apps/core-api/src/modules/notification/notification.service.spec.ts","contains":"NotificationService"},{"path":"apps/core-api/src/common/platform/platform.adapters.spec.ts","contains":"disabled feature/provider selects no-op adapters"}]}

### support-tickets

- title: Idempotent support tickets with customer tracking and staff workflow
- status: implemented
- owner: core-api/support with core-api/admin
- contracts: openapi/core-api.json, docs/plans/2026-07-15-ui-contract-completion.md
- evidence: apps/core-api/src/modules/support/support.controller.ts, apps/core-api/src/modules/support/entities/support-ticket.entity.ts
- verification: {"kind":"automated-test-source","evidence":[{"path":"apps/core-api/src/modules/support/support.service.spec.ts","contains":"listMine khóa ownership"}]}

### mini-game

- title: Two-player rock-paper-scissors mini game
- status: implemented
- owner: core-api/mini-game
- contracts: openapi/core-api.json, docs/services/mini-game-service.md, docs/services/realtime-gateway.md
- evidence: apps/core-api/src/modules/mini-game/mini-game.controller.ts
- verification: {"kind":"automated-test-source","evidence":[{"path":"apps/core-api/src/modules/mini-game/mini-game.integration.spec.ts","contains":"Mini"}]}

### movie-match

- title: Shared video playback and anonymous movie match
- status: implemented
- owner: core-api/movie-match
- contracts: openapi/core-api.json, docs/services/movie-match-service.md, docs/services/realtime-gateway.md
- evidence: apps/core-api/src/modules/movie-match/movie-match.controller.ts
- verification: {"kind":"automated-test-source","evidence":[{"path":"apps/core-api/src/modules/movie-match/movie-match.integration.spec.ts","contains":"Movie"}]}

### palm-match

- title: Deterministic palm reading and anonymous palm match
- status: implemented
- owner: core-api/palm-match
- contracts: openapi/core-api.json, docs/services/palm-match-service.md
- evidence: apps/core-api/src/modules/palm-match/palm-match.controller.ts
- verification: {"kind":"automated-test-source","evidence":[{"path":"apps/core-api/src/modules/palm-match/palm-match.integration.spec.ts","contains":"Palm"}]}

### short-video

- title: Short-video lifecycle, moderation and derived ranking
- status: implemented
- owner: core-api/short-video
- contracts: openapi/core-api.json, docs/services/short-video-service.md, docs/services/safety-service.md
- evidence: apps/core-api/src/modules/short-video/short-video.controller.ts, apps/core-api/src/modules/short-video/jobs/video-ranking.service.ts
- verification: {"kind":"automated-test-source","evidence":[{"path":"apps/core-api/src/modules/short-video/short-video.integration.spec.ts","contains":"Short"}]}

### user-profile-open-entry

- title: Optional profile birth date without an age-based access gate
- status: implemented
- owner: core-api/user
- contracts: openapi/core-api.json, docs/01-product-features.md, docs/06-domain-rules.md
- evidence: apps/core-api/src/modules/user/user.service.ts, apps/core-api/src/modules/user/dto/update-profile.dto.ts
- verification: {"kind":"automated-test-source","evidence":[{"path":"apps/core-api/src/modules/user/user.service.spec.ts","contains":"birthDate không phải access gate"}]}

### privacy-analytics-consent

- title: Hosted analytics with quota controls and an explicit privacy warning
- status: implemented
- owner: web/shared-analytics
- contracts: docs/runbooks/posthog-cloud.md
- evidence: apps/web/src/shared/analytics/product-analytics.ts, apps/web/src/shared/analytics/product-analytics.ts, apps/web/src/shared/analytics/product-analytics.ts, apps/web/src/shared/analytics/product-analytics-components.tsx
- verification: {"kind":"automated-test-source","evidence":[{"path":"apps/web/src/shared/analytics/product-analytics.spec.ts","contains":"khởi tạo SDK ngay cả khi chưa có cookie consent"},{"path":"apps/web/src/shared/analytics/product-analytics.spec.ts","contains":"khởi tạo trực tiếp với cấu hình tối ưu quota và debug UI"},{"path":"apps/web/src/shared/analytics/product-analytics-components.spec.tsx","contains":"hiển thị analytics đang bật và không có thao tác opt-in riêng"}]}

### realtime-fanout

- title: Authenticated Socket.IO fanout and distributed connection quota through Redis
- status: implemented
- owner: signaling-gateway with core-api producers
- contracts: libs/common-dtos/src/lib/realtime-events.ts, docs/services/realtime-gateway.md, specs/realtime.asyncapi.yaml
- evidence: apps/signaling-gateway/src/app/signaling.gateway.ts, libs/common-dtos/src/lib/realtime-events.ts, apps/signaling-gateway/src/app/connection-quota.service.ts
- verification: {"kind":"automated-test-source","evidence":[{"path":"apps/signaling-gateway/src/app/signaling.integration.spec.ts","contains":"event chỉ tới ĐÚNG user"},{"path":"apps/signaling-gateway/src/app/signaling.horizontal-scale.integration.spec.ts","contains":"quota per-user atomic xuyên 2 instance"}]}

## Deferred decisions

### production-cache-policy

- status: deferred
- owner: architecture/platform
- reason: Redis and derived ranking are already documented as projections or transport support. No production read/write, hit-rate, latency, or eviction evidence is checked into this repository to justify a new cache layer or TTL policy.
- evidence: docs/11-engineering-principles.md

### algorithm-upgrades

- status: deferred
- owner: product/domain owners
- reason: Matching shard expansion, feed CQRS and richer video ranking need observed traffic and SLO evidence. The current code keeps the documented bounded algorithms rather than adding speculative infrastructure.
- evidence: docs/07-roadmap.md

### short-video-reporting-ui

- status: deferred
- owner: web/short-video with core-api/safety
- reason: The backend report contract and web API hook remain, but the visible report action was deliberately removed from the current reel UI. Do not claim the client feature is implemented until a product decision re-enables it and a behavior test verifies the user action.
- evidence: apps/web/src/features/short-video/components/video-reel-feed.spec.tsx, apps/web/src/features/short-video/components/video-reel-feed.spec.tsx

## Out of scope


