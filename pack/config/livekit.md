# Environment variables LIVEKIT_*

id: config:LIVEKIT
secret values are never printed

| var | app | class | type/valid | default | required | set in | meaning |
|---|---|---|---|---|---|---|---|
| LIVEKIT_URL | core-api | endpoint | string | ws://localhost:7880 |  | .env.example:264, deploy/hosted/core-api.env.example:75, deploy/production/compose.yml:106, docker-compose.dev.yml:29, k8s/base/core-api/configmap.yaml:134 | Calling — Giai đoạn 2 (docs/services/calling-service.md § 6); key/secret khớp livekit.yaml |
| LIVEKIT_REGION_URLS | core-api | other | string |  |  | .env.example:269, k8s/base/core-api/configmap.yaml:138, k8s/overlays/production-region-b/core-api-configmap-patch.yaml:30 | GĐ7 multi-region (ADR 0005): JSON map region (User.region) → ws/wss URL edge LiveKit. '' (default) = single-region, mọi client dùng LIVEKIT_URL — deploy hôm nay không đổi gì. Regi… |
| LIVEKIT_API_URL | core-api | endpoint | string |  |  | .env.example:274, deploy/hosted/core-api.env.example:76, deploy/production/compose.yml:107, docker-compose.dev.yml:30 | Endpoint server-to-server (RoomServiceClient: createRoom/deleteRoom/updateParticipant...) — KHÁC LIVEKIT_URL (endpoint client/browser dùng để join media). '' (default) = derive từ… |
| LIVEKIT_API_KEY | core-api | secret | string |  |  | .env.example:275, deploy/production/.env.example:25, deploy/hosted/core-api.env.example:77, deploy/production/compose.yml:108, k8s/base/core-api/secret.yaml:37 |  |
| LIVEKIT_API_SECRET | core-api | secret | string |  |  | .env.example:276, deploy/production/.env.example:26, deploy/hosted/core-api.env.example:78, deploy/production/compose.yml:109, k8s/base/core-api/secret.yaml:38 |  |
