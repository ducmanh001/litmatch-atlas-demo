# Database tables owned by module calling

id: db:calling
module: module:calling
tables: call_sessions, voice_match_reactions
see: ../modules/calling.md

### table: call_sessions

Phiên voice call 2 người trên LiveKit (docs/services/calling-service.md § 1). 1 voice MatchSession = tối đa 1 call — unique DB trên matchSessionId. durationSeconds tính bằng giờ server core-api (nguồn sự thật), không tin client/SFU.

Entity: CallSession (apps/core-api/src/modules/calling/entities/call-session.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | uuid |  |  | PK |  |
| match_session_id | uuid | null |  | FK→match_sessions.id UQ |  |
| room_name | character varying(64) |  |  | UQ | Room LiveKit = `call-{id}` — lưu để webhook lookup ngược; client không tự chọn room. |
| user_a_id | uuid |  |  | FK→users.id |  |
| user_b_id | uuid |  |  | FK→users.id |  |
| status | character varying(16) |  | 'pending'::character varying |  |  |
| joined_a_at | timestamp with time zone | null |  |  |  |
| joined_b_at | timestamp with time zone | null |  |  |  |
| started_at | timestamp with time zone | null |  |  |  |
| ended_at | timestamp with time zone | null |  |  |  |
| end_reason | character varying(24) | null |  |  |  |
| duration_seconds | integer | null |  |  |  |
| billed_minutes | integer |  | 0 |  | Legacy response field, luôn 0 từ khi Voice Match không còn tính phí theo phút. |
| created_at | timestamp with time zone |  | now() |  |  |
| updated_at | timestamp with time zone |  | now() |  |  |
| reconnect_started_at | timestamp with time zone | null |  |  | Mốc bắt đầu cửa sổ reconnect; trong khoảng này timer được tạm dừng. |
| disconnected_a_at | timestamp with time zone | null |  |  | Dấu vết rời phòng của từng bên, để chỉ resume timer khi cả hai đã quay lại. |
| disconnected_b_at | timestamp with time zone | null |  |  |  |
| call_kind | character varying(16) |  | 'voice_match'::character varying |  |  |

Constraints: check call_sessions_billed_minutes_check ((billed_minutes >= 0)); check call_sessions_call_kind_check (((call_kind)::text = ANY ((ARRAY['voice_match'::character varying, 'friend'::character varying])::text[]))); check call_sessions_duration_seconds_check ((duration_seconds >= 0)); check call_sessions_end_reason_check (((end_reason)::text = ANY ((ARRAY['completed'::character varying, 'free_limit'::character varying, 'insufficient_balance'::character varying, 'pending_timeout…; check call_sessions_status_check (((status)::text = ANY ((ARRAY['pending'::character varying, 'active'::character varying, 'ended'::character varying])::text[]))); unique uq_call_sessions_match_session (match_session_id); unique uq_call_sessions_room_name (room_name)

Indexes: idx_call_sessions_active_updated (updated_at, id) WHERE ((status)::text = 'active'::text); idx_call_sessions_reconnect_started (reconnect_started_at, id) WHERE (((status)::text = 'active'::text) AND (reconnect_started_at IS NOT NULL)); idx_call_sessions_status_created (status, created_at); UNIQUE uq_call_sessions_active_friend_pair (user_a_id, user_b_id) WHERE (((call_kind)::text = 'friend'::text) AND ((status)::text = ANY ((ARRAY['pendin…; UNIQUE uq_call_sessions_match_session (match_session_id); UNIQUE uq_call_sessions_room_name (room_name)

Access: calling(owner) r16/w8/lock5 via manager+manager(lock option)+manager.save(instance)+querybuilder+repository [apps/core-api/src/modules/calling/calling.service.ts:116, apps/core-api/src/modules/calling/calling.service.ts:146]

### table: voice_match_reactions

Một lượt yêu thích immutable sau Voice Match; unique DB là chốt chống double tap/retry.

Entity: VoiceMatchReaction (apps/core-api/src/modules/calling/entities/voice-match-reaction.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | uuid |  | gen_random_uuid() | PK |  |
| call_id | uuid |  |  | FK→call_sessions.id |  |
| rater_user_id | uuid |  |  | FK→users.id |  |
| created_at | timestamp with time zone |  | now() |  |  |

Constraints: unique uq_voice_match_reactions_call_rater (call_id,rater_user_id)

Indexes: UNIQUE uq_voice_match_reactions_call_rater (call_id, rater_user_id)

Access: calling(owner) r3/w2 via manager+manager.save(instance)+repository [apps/core-api/src/modules/calling/calling.service.ts:293, apps/core-api/src/modules/calling/calling.service.ts:387]

