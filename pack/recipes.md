# Change recipes mined from git history

id: recipes

Which file kinds past commits of each kind touched together. >=80% usually required; 30-80% case by case.

## Thêm một endpoint REST

Detection: Commit làm openapi/core-api.json thêm một operationId mới. Usable commits 9.

| file kind | share | examples |
|---|---|---|
| controller | 100% (9/9) | apps/core-api/src/modules/friend/controllers/profile-social.controller.ts, apps/core-api/src/modules/friend/friend.controller.ts |
| unit/integration test | 100% (9/9) | apps/core-api/src/modules/friend/friend.integration.spec.ts, apps/web/src/features/notification/notification-copy.spec.ts |
| service / domain code | 100% (9/9) | apps/core-api/src/modules/friend/services/profile-social.service.spec.ts, apps/core-api/src/modules/friend/services/profile-social.service.ts |
| generated api-client | 100% (9/9) | libs/api-client/src/generated/core-api.ts |
| openapi/core-api.json | 100% (9/9) | openapi/core-api.json |
| dto | 89% (8/9) | apps/core-api/src/modules/friend/dto/profile-social.dtos.ts |
| module wiring (*.module.ts) | 78% (7/9) | apps/core-api/src/modules/friend/friend.module.ts, apps/core-api/src/modules/gift/gift.module.ts |
| migration | 67% (6/9) | apps/core-api/src/database/migrations/1757900000000-profile-follow-following-index.ts |
| entity | 67% (6/9) | apps/core-api/src/modules/friend/entities/profile-follow.entity.ts |
| module public API (index.ts) | 67% (6/9) | apps/core-api/src/modules/friend/index.ts |
| agent instructions (AGENTS.md) | 56% (5/9) | apps/web/AGENTS.md |
| web feature code | 56% (5/9) | apps/web/src/app/(app)/app-chrome.tsx, apps/web/src/app/(app)/friends/page.tsx |
| docs/services | 56% (5/9) | docs/services/friend-service.md |
| env.validation.ts (core-api) | 44% (4/9) | apps/core-api/src/config/env.validation.ts |
| .env.example | 33% (3/9) | .env.example |
| docs (other) | 33% (3/9) | docs/02-domain-model.md, docs/06-domain-rules.md |
| core-api other source (jobs, ports, guards, common) | 33% (3/9) | apps/core-api/src/modules/party-room/jobs/party-room-sweeper.service.ts |
| realtime contract (common-dtos) | 33% (3/9) | libs/common-dtos/src/lib/realtime-events.ts |
| admin feature code | 33% (3/9) | apps/admin/src/app/router.tsx, apps/admin/src/features/economy/api.ts |
| CI / scripts | 22% (2/9) | scripts/ci/local.mjs, scripts/ci/local.test.mjs |

## Thêm một biến môi trường cho core-api

Detection: Commit thêm một dòng KEY: Joi… trong env.validation.ts. Usable commits 18.

| file kind | share | examples |
|---|---|---|
| env.validation.ts (core-api) | 100% (18/18) | apps/core-api/src/config/env.validation.ts |
| unit/integration test | 94% (17/18) | apps/core-api/src/config/env.validation.spec.ts |
| .env.example | 89% (16/18) | .env.example |
| docs/services | 78% (14/18) | docs/services/economy-service.md |
| core-api other source (jobs, ports, guards, common) | 78% (14/18) | apps/core-api/src/common/guards/jwt-auth.guard.ts, apps/core-api/src/common/guards/jwt-rotation.ts |
| service / domain code | 78% (14/18) | apps/core-api/src/modules/friend/friend.errors.ts, apps/core-api/src/modules/friend/services/profile-social.service.spec.ts |
| migration | 72% (13/18) | apps/core-api/src/database/migrations/1757400000000-profile-follow.ts, apps/core-api/src/database/migrations/1757500000000-profile-gift-context.ts |
| dto | 67% (12/18) | apps/core-api/src/modules/friend/dto/profile-social.dtos.ts, apps/core-api/src/modules/gift/dto/gift.dtos.ts |
| entity | 67% (12/18) | apps/core-api/src/modules/friend/entities/profile-chat-contact.entity.ts, apps/core-api/src/modules/friend/entities/profile-follow.entity.ts |
| module wiring (*.module.ts) | 67% (12/18) | apps/core-api/src/modules/friend/friend.module.ts, apps/core-api/src/modules/gift/gift.module.ts |
| docs (other) | 67% (12/18) | docs/02-domain-model.md, docs/06-domain-rules.md |
| controller | 61% (11/18) | apps/core-api/src/modules/friend/controllers/profile-social.controller.ts, apps/core-api/src/modules/gift/gift.controller.ts |
| module public API (index.ts) | 56% (10/18) | apps/core-api/src/modules/friend/index.ts |
| core-api app wiring (app.module / main) | 44% (8/18) | apps/core-api/src/app/app.module.ts |
| k8s manifests | 39% (7/18) | k8s/README.md, k8s/base/core-api/configmap.yaml |
| other | 39% (7/18) | .husky/pre-push |
| generated api-client | 28% (5/18) | libs/api-client/src/generated/core-api.ts |
| openapi/core-api.json | 28% (5/18) | openapi/core-api.json |
| web feature code | 22% (4/18) | apps/web/src/features/discovery/components/discovery-detail-sheet.tsx, apps/web/src/features/profile/api.ts |
| realtime contract (common-dtos) | 22% (4/18) | libs/common-dtos/src/lib/realtime-events.ts |
| CI / scripts | 17% (3/18) | scripts/ci/local.mjs, scripts/ci/local.test.mjs |

## Thêm một migration (bảng/cột/index mới)

Detection: Commit thêm file mới trong database/migrations/. Usable commits 18.

| file kind | share | examples |
|---|---|---|
| migration | 100% (18/18) | apps/core-api/src/database/migrations/1757900000000-profile-follow-following-index.ts |
| unit/integration test | 100% (18/18) | apps/core-api/src/modules/friend/friend.integration.spec.ts, apps/web/src/features/notification/notification-copy.spec.ts |
| service / domain code | 94% (17/18) | apps/core-api/src/modules/friend/services/profile-social.service.spec.ts, apps/core-api/src/modules/friend/services/profile-social.service.ts |
| entity | 89% (16/18) | apps/core-api/src/modules/friend/entities/profile-follow.entity.ts |
| dto | 83% (15/18) | apps/core-api/src/modules/friend/dto/profile-social.dtos.ts |
| docs/services | 78% (14/18) | docs/services/friend-service.md |
| controller | 72% (13/18) | apps/core-api/src/modules/friend/controllers/profile-social.controller.ts, apps/core-api/src/modules/friend/friend.controller.ts |
| core-api other source (jobs, ports, guards, common) | 72% (13/18) | apps/core-api/src/modules/short-video/jobs/video-sweeper.service.ts |
| env.validation.ts (core-api) | 72% (13/18) | apps/core-api/src/config/env.validation.ts |
| module wiring (*.module.ts) | 72% (13/18) | apps/core-api/src/modules/friend/friend.module.ts, apps/core-api/src/modules/gift/gift.module.ts |
| .env.example | 61% (11/18) | .env.example |
| module public API (index.ts) | 61% (11/18) | apps/core-api/src/modules/friend/index.ts |
| docs (other) | 61% (11/18) | docs/02-domain-model.md, docs/06-domain-rules.md |
| generated api-client | 39% (7/18) | libs/api-client/src/generated/core-api.ts |
| openapi/core-api.json | 39% (7/18) | openapi/core-api.json |
| core-api app wiring (app.module / main) | 39% (7/18) | apps/core-api/src/app/capabilities.service.ts |
| other | 39% (7/18) | apps/web/public/avatar/accessory-crown.svg, apps/web/public/avatar/accessory-none.svg |
| web feature code | 33% (6/18) | apps/web/src/app/(app)/app-chrome.tsx, apps/web/src/app/(app)/friends/page.tsx |
| agent instructions (AGENTS.md) | 22% (4/18) | apps/web/AGENTS.md |
| realtime contract (common-dtos) | 22% (4/18) | libs/common-dtos/src/lib/realtime-events.ts |
| CI / scripts | 17% (3/18) | scripts/ci/local.mjs, scripts/ci/local.test.mjs |

## Thêm một sự kiện realtime

Detection: Commit thêm một khoá vào RealtimeEvents. Usable commits 6.

| file kind | share | examples |
|---|---|---|
| unit/integration test | 100% (6/6) | apps/core-api/src/modules/gift/gift.integration.spec.ts, apps/core-api/src/modules/party-room/party-room.integration.spec.ts |
| service / domain code | 100% (6/6) | apps/core-api/src/modules/party-room/party-room.constants.ts, apps/core-api/src/modules/party-room/party-room.errors.ts |
| docs/services | 100% (6/6) | docs/services/party-room-service.md |
| realtime contract (common-dtos) | 100% (6/6) | libs/common-dtos/src/lib/realtime-events.ts |
| core-api other source (jobs, ports, guards, common) | 83% (5/6) | apps/core-api/src/modules/party-room/jobs/party-room-sweeper.service.ts |
| controller | 83% (5/6) | apps/core-api/src/modules/party-room/party-room.controller.ts |
| module wiring (*.module.ts) | 83% (5/6) | apps/core-api/src/modules/party-room/party-room.module.ts |
| other | 83% (5/6) | .agents/context-map.json |
| .env.example | 67% (4/6) | .env.example |
| dto | 67% (4/6) | apps/core-api/src/modules/party-room/dto/party-room.dtos.ts |
| entity | 67% (4/6) | apps/core-api/src/modules/party-room/entities/party-room-comment.entity.ts, apps/core-api/src/modules/party-room/entities/party-room-member.entity.ts |
| module public API (index.ts) | 67% (4/6) | apps/core-api/src/modules/party-room/index.ts |
| docs (other) | 67% (4/6) | docs/00-overview-and-index.md, docs/08-working-with-agents.md |
| env.validation.ts (core-api) | 50% (3/6) | apps/core-api/src/config/env.validation.ts |
| migration | 50% (3/6) | apps/core-api/src/database/migrations/1756800000000-party-room-presence-chat.ts |
| web feature code | 33% (2/6) | apps/web/src/features/party-room/api.ts, apps/web/src/features/party-room/components/member-list.tsx |
| generated api-client | 33% (2/6) | libs/api-client/src/generated/core-api.ts |
| openapi/core-api.json | 33% (2/6) | openapi/core-api.json |
| dependencies (package.json, lockfile) | 33% (2/6) | package.json, pnpm-lock.yaml |
| CI / scripts | 17% (1/6) | scripts/agent/context.test.mjs |
| core-api app wiring (app.module / main) | 17% (1/6) | apps/core-api/src/app/app.module.ts, apps/core-api/src/main.ts |
| signaling-gateway | 17% (1/6) | apps/signaling-gateway/jest.config.cts, apps/signaling-gateway/src/app/signaling.gateway.ts |

## Thêm một mã lỗi nghiệp vụ

Detection: Commit thêm một khoá vào *.errors.ts. Usable commits 15.

| file kind | share | examples |
|---|---|---|
| unit/integration test | 100% (15/15) | apps/core-api/src/modules/friend/friend.integration.spec.ts, apps/core-api/src/modules/gift/gift.integration.spec.ts |
| controller | 93% (14/15) | apps/core-api/src/modules/friend/controllers/profile-social.controller.ts, apps/core-api/src/modules/gift/gift.controller.ts |
| dto | 93% (14/15) | apps/core-api/src/modules/friend/dto/profile-social.dtos.ts, apps/core-api/src/modules/gift/dto/gift.dtos.ts |
| service / domain code | 93% (14/15) | apps/core-api/src/modules/friend/friend.errors.ts, apps/core-api/src/modules/friend/services/profile-social.service.spec.ts |
| module wiring (*.module.ts) | 93% (14/15) | apps/core-api/src/modules/friend/friend.module.ts, apps/core-api/src/modules/gift/gift.module.ts |
| entity | 87% (13/15) | apps/core-api/src/modules/friend/entities/profile-chat-contact.entity.ts, apps/core-api/src/modules/friend/entities/profile-follow.entity.ts |
| module public API (index.ts) | 87% (13/15) | apps/core-api/src/modules/friend/index.ts |
| migration | 80% (12/15) | apps/core-api/src/database/migrations/1757400000000-profile-follow.ts, apps/core-api/src/database/migrations/1757500000000-profile-gift-context.ts |
| env.validation.ts (core-api) | 73% (11/15) | apps/core-api/src/config/env.validation.ts |
| docs/services | 73% (11/15) | docs/services/discovery-service.md, docs/services/friend-service.md |
| core-api other source (jobs, ports, guards, common) | 73% (11/15) | apps/core-api/src/modules/party-room/jobs/party-room-sweeper.service.ts |
| docs (other) | 67% (10/15) | docs/02-domain-model.md, docs/06-domain-rules.md |
| .env.example | 60% (9/15) | .env.example |
| other | 47% (7/15) | .vscode/settings.json |
| generated api-client | 40% (6/15) | libs/api-client/src/generated/core-api.ts |
| openapi/core-api.json | 40% (6/15) | openapi/core-api.json |
| realtime contract (common-dtos) | 40% (6/15) | libs/common-dtos/src/lib/realtime-events.ts |
| core-api app wiring (app.module / main) | 40% (6/15) | apps/core-api/src/app/app.module.ts, apps/core-api/src/main.ts |
| agent instructions (AGENTS.md) | 33% (5/15) | apps/web/AGENTS.md |
| web feature code | 20% (3/15) | apps/web/src/features/discovery/components/discovery-detail-sheet.tsx, apps/web/src/features/profile/api.ts |
| CI / scripts | 20% (3/15) | scripts/ci/local.mjs, scripts/ci/local.test.mjs |

