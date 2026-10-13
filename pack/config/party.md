# Environment variables PARTY_*

id: config:PARTY
secret values are never printed

| var | app | class | type/valid | default | required | set in | meaning |
|---|---|---|---|---|---|---|---|
| PARTY_MAX_SPEAKERS | core-api | tuning | number | 8 |  | .env.example:288, k8s/base/core-api/configmap.yaml:148 | Party Room — Giai đoạn 3 (docs/services/party-room-service.md § 7) Cap speaker là giới hạn CỨNG theo docs/03 § 3.8.A (consumer tăng N×(N-1)) — chỉ nới khi có cascade SFU |
| PARTY_MAX_MEMBERS | core-api | tuning | number | 100 |  | .env.example:290, k8s/base/core-api/configmap.yaml:149 | Truyền vào LiveKit maxParticipants lúc tạo room — SFU tự chặn join vượt ngưỡng |
| PARTY_TOKEN_TTL_SECONDS | core-api | secret | number |  |  | .env.example:291, k8s/base/core-api/configmap.yaml:150 |  |
| PARTY_EMPTY_ROOM_TIMEOUT_SECONDS | core-api | tuning | number | 300 |  | .env.example:293, k8s/base/core-api/configmap.yaml:151 | LiveKit tự đóng room trống sau N giây — backstop tầng SFU cho phòng vô chủ |
| PARTY_SWEEPER_INTERVAL_MS | core-api | tuning | number | 30000 |  | .env.example:294, k8s/base/core-api/configmap.yaml:152 |  |
| PARTY_STALE_ROOM_SECONDS | core-api | tuning | number | 120 |  | .env.example:296, k8s/base/core-api/configmap.yaml:153 | Phòng active mà không còn member active quá N giây → sweeper đóng (webhook có thể rớt) |
| PARTY_TITLE_MAX_LENGTH | core-api | tuning | number | 100 |  | .env.example:297, k8s/base/core-api/configmap.yaml:154 |  |
| PARTY_HOST_DISCONNECT_GRACE_SECONDS | core-api | tuning | number | 15 |  | .env.example:300, k8s/base/core-api/configmap.yaml:155 | Host rớt kết nối NGOÀI Ý MUỐN (webhook participant_left) — chờ tự kết nối lại trước khi đóng phòng (host_left); REST leave chủ động vẫn đóng ngay, không qua grace này (§ 4) |
| PARTY_MEMBER_DISCONNECT_GRACE_SECONDS | core-api | tuning | number | 15 |  | .env.example:302 | Member rớt mạng ngoài ý muốn còn hiển thị trong roster trong thời gian ngắn để reconnect; REST leave chủ động không qua grace. |
| PARTY_HOST_GRACE_CHECK_INTERVAL_MS | core-api | tuning | number | 5000 |  | .env.example:304, k8s/base/core-api/configmap.yaml:156 | Tần suất quét phòng hết grace — tách riêng PARTY_SWEEPER_INTERVAL_MS (30s, backstop khác hẳn: phòng vô chủ hoàn toàn) vì grace ngắn hơn nhiều, cần phát hiện sát giờ hơn |
| PARTY_COMMENT_MAX_LENGTH | core-api | tuning | number | 300 |  | .env.example:305 |  |
