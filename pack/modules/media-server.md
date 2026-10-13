# Module media-server

id: module:media-server
kind: deployable (config only)
code: apps/media-server

Không phải code NestJS: chỉ là cấu hình/deploy image livekit/livekit-server (livekit.yaml, compose, k8s/base/media-server) làm SFU chuyển tiếp audio/video giữa client; giữ room runtime và trạng thái cụm trong Redis. KHÔNG chứa business logic, KHÔNG truy cập DB: quyền tham gia, membership, timer, billing và giới hạn speaker đều ở core-api (calling / party-room).

## Facts

- tests: 0 unit spec files, 0 integration spec files
- history: 14 commits (3 fixes), last 2026-07-30

## Where to read next

- why it talks to other services: ../service-map/media-server.md and ../service-map/media-server-techniques.md
- flows involving it: [admin-room-management](../flows/admin-room-management.md), [matching-to-call](../flows/matching-to-call.md), [party-room-leave-and-role-change](../flows/party-room-leave-and-role-change.md), [party-room-lifecycle](../flows/party-room-lifecycle.md), [party-room-speaker-invite](../flows/party-room-speaker-invite.md)

## Linked issues (heuristic, public subset)

- P004 [fixed] Adapter CLAUDE.md / skills lệch bản canonical (symlink tay)

See ../issues/index.md.
