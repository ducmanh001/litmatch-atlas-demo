# Database tables owned by module party-room

id: db:party-room
module: module:party-room
tables: party_room_comments, party_room_members, party_rooms
see: ../modules/party-room.md

### table: party_room_comments

Bình luận realtime trong Party Room — append-only để retry/poll có thứ tự ổn định và giữ bằng chứng cho Trust & Safety; không xoá theo vòng đời LiveKit room.

Entity: PartyRoomComment (apps/core-api/src/modules/party-room/entities/party-room-comment.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | uuid |  | gen_random_uuid() | PK |  |
| seq | bigint |  |  |  | DB cấp thứ tự cho cursor, không dùng createdAt vì nhiều comment có thể cùng mili-giây. |
| room_id | uuid |  |  | FK→party_rooms.id |  |
| sender_user_id | uuid |  |  | FK→users.id |  |
| content | text |  |  |  |  |
| idempotency_key | character varying(255) |  |  | UQ | Prefix `party:comment:{userId}:{roomId}:{clientKey}` — unique DB để retry không nhân đôi. |
| created_at | timestamp with time zone |  | now() |  |  |

Constraints: unique uq_party_room_comments_idempotency_key (idempotency_key)

Indexes: idx_party_room_comments_room_seq (room_id, seq); UNIQUE uq_party_room_comments_idempotency_key (idempotency_key)

Access: party-room(owner) r2/w2 via manager+manager.save(instance)+querybuilder+repository [apps/core-api/src/modules/party-room/party-room.service.ts:815, apps/core-api/src/modules/party-room/party-room.service.ts:842]

### table: party_room_members

Membership của 1 user trong 1 phòng. Active = `leftAt IS NULL`; rejoin sau khi rời tạo ROW MỚI (giữ lịch sử). Bất biến enforce ở DB bằng partial unique index (migration 1752700000000): 1 membership active/(room,user) + 1 phòng active/user toàn hệ thống.

Entity: PartyRoomMember (apps/core-api/src/modules/party-room/entities/party-room-member.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | uuid |  | gen_random_uuid() | PK |  |
| room_id | uuid |  |  | FK→party_rooms.id |  |
| user_id | uuid |  |  | FK→users.id |  |
| role | character varying(16) |  |  |  |  |
| joined_at | timestamp with time zone |  | now() |  |  |
| left_at | timestamp with time zone | null |  |  |  |
| created_at | timestamp with time zone |  | now() |  |  |
| updated_at | timestamp with time zone |  | now() |  |  |
| speaker_invite_pending | boolean |  | false |  | Host đã mời audience lên speaker nhưng người đó chưa đồng ý — không phải role publish. |
| disconnected_at | timestamp with time zone | null |  |  | Rớt LiveKit ngoài ý muốn nhưng vẫn còn trong grace period. REST leave chủ động đặt `leftAt` ngay; reconnect trước khi hết grace xoá mốc này và giữ nguyên membership. |

Indexes: idx_party_members_disconnect_grace (disconnected_at, id) WHERE ((left_at IS NULL) AND (disconnected_at IS NOT NULL)); idx_party_members_room_active (room_id, role) WHERE (left_at IS NULL); UNIQUE uq_party_members_active_room_user (room_id, user_id) WHERE (left_at IS NULL); UNIQUE uq_party_members_active_user (user_id) WHERE (left_at IS NULL)

Access: party-room(owner) r23/w15 via manager+manager.save(instance)+querybuilder+repository [apps/core-api/src/modules/party-room/jobs/party-room-sweeper.service.ts:152, apps/core-api/src/modules/party-room/jobs/party-room-sweeper.service.ts:195]

### table: party_rooms

Phòng party multi-user trên LiveKit (docs/services/party-room-service.md). `speakerLimit` là SNAPSHOT config lúc tạo phòng — đổi config không retro phòng đang sống; đây là giới hạn cứng theo docs/03 § 3.8.A (consumer tăng N×(N-1)).

Entity: PartyRoom (apps/core-api/src/modules/party-room/entities/party-room.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | uuid |  | gen_random_uuid() | PK |  |
| host_user_id | uuid |  |  | FK→users.id |  |
| title | character varying(500) |  |  |  |  |
| status | character varying(16) |  | 'active'::character varying |  |  |
| speaker_limit | integer |  |  |  |  |
| close_reason | character varying(32) | null |  |  |  |
| closed_at | timestamp with time zone | null |  |  |  |
| created_at | timestamp with time zone |  | now() |  |  |
| updated_at | timestamp with time zone |  | now() |  |  |
| livekit_url | character varying(512) | null |  |  | SNAPSHOT URL LiveKit chốt theo region của HOST lúc tạo phòng (GĐ7 — ADR 0005): room sống trên 1 node/region, mọi participant vào sau nhận đúng URL này bất kể region của họ và bất kể LIVEKIT_REGION_UR… |
| host_disconnected_at | timestamp with time zone | null |  |  | Set khi webhook SFU báo host mất kết nối NGOÀI Ý MUỐN (participant_left) — KHÔNG set khi host chủ động rời qua REST (đóng ngay, không qua cột này). Còn active + không null nghĩa là đang trong grace p… |
| category | character varying(16) |  | 'talk'::character varying |  |  |

Constraints: check chk_party_rooms_speaker_limit ((speaker_limit >= 1))

Indexes: idx_party_rooms_active_host_grace (host_disconnected_at, id) WHERE ((status)::text = 'active'::text); idx_party_rooms_status_category_created (status, category, created_at, id); idx_party_rooms_status_created (status, created_at)

Access: party-room(owner) r14/w5/lock6 via manager+manager(lock option)+manager.save(instance)+querybuilder+repository [apps/core-api/src/modules/party-room/jobs/party-room-sweeper.service.ts:117, apps/core-api/src/modules/party-room/jobs/party-room-sweeper.service.ts:184]

