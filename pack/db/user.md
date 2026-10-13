# Database tables owned by module user

id: db:user
module: module:user
tables: user_privacy_settings, users
see: ../modules/user.md

### table: user_privacy_settings

Privacy preference của chính user — không compose vào PublicProfileDto.

Entity: PrivacySetting (apps/core-api/src/modules/user/entities/privacy-setting.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| user_id | uuid |  |  | PK FK→users.id |  |
| show_online_status | boolean |  | true |  |  |
| show_distance | boolean |  | true |  |  |
| searchable_by_phone | boolean |  | false |  |  |
| hide_profile | boolean |  | false |  |  |

Access: user(owner) r4/w1 via raw-sql+repository [apps/core-api/src/modules/user/services/privacy-settings.service.ts:21, apps/core-api/src/modules/user/services/privacy-settings.service.ts:29]; discovery(OTHER MODULE) r1/w0 via raw-sql [apps/core-api/src/modules/discovery/nearby.service.ts:315]; feed(OTHER MODULE) r1/w0 via raw-sql [apps/core-api/src/modules/feed/feed.service.ts:110]

### table: users

Entity: User (apps/core-api/src/modules/user/entities/user.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | uuid |  | gen_random_uuid() | PK |  |
| nickname | character varying(50) |  |  |  |  |
| gender | character varying(10) |  | 'unknown'::character varying |  |  |
| birth_date | date | null |  |  |  |
| region | character varying(10) | null |  |  |  |
| avatar_id | character varying(64) |  |  |  |  |
| trust_score | integer |  | 100 |  | Điểm tin cậy nội bộ cho matching priority (docs/06) — không expose ra API. |
| status | character varying(16) |  | 'active'::character varying |  |  |
| is_guest | boolean |  | false |  |  |
| created_at | timestamp with time zone |  | now() |  |  |
| updated_at | timestamp with time zone |  | now() |  |  |
| role | character varying(16) |  | 'user'::character varying |  | RBAC (docs/12 § 12.7 Task 0) — nhúng vào access token lúc issue/refresh, không nhận từ client. `type: 'varchar'` tường minh bắt buộc: `Role` là type alias (không phải class/enum runtime như Gender/Us… |
| interests | jsonb | null |  |  | Sở thích hiển thị công khai (edit-profile.html, tối đa PROFILE_MAX_INTERESTS) — NULL = chưa khai. |
| seeking_gender | character varying(8) | null |  |  | Bộ "Đang tìm kiếm" — preference hồ sơ, KHÔNG phải tiêu chí ticket Matching (per-ticket riêng). |
| seeking_age_min | integer | null |  |  |  |
| seeking_age_max | integer | null |  |  |  |

Indexes: idx_users_created_at_id (created_at, id); idx_users_discovery_browse (status, gender, birth_date)

Access: user(owner) r11/w7/lock1 via manager+manager.save(instance)+querybuilder+repository [apps/core-api/src/modules/user/user.service.ts:75, apps/core-api/src/modules/user/user.service.ts:88]; auth(OTHER MODULE) r1/w3/lock1 via manager+manager(lock option)+manager.save(instance)+querybuilder [apps/core-api/src/modules/auth/auth.service.ts:225, apps/core-api/src/modules/auth/auth.service.ts:286]; friend(OTHER MODULE) r3/w0/lock1 via manager+manager(lock option)+querybuilder+raw-sql [apps/core-api/src/modules/friend/services/profile-social.service.ts:221, apps/core-api/src/modules/friend/services/profile-social.service.ts:325]; matching(OTHER MODULE) r3/w0/lock1 via manager+manager(lock option) [apps/core-api/src/modules/matching/jobs/matcher-worker.service.ts:246, apps/core-api/src/modules/matching/services/guest-match-quota.service.ts:53]; safety(OTHER MODULE) r1/w0/lock1 via repository+repository(lock option) [apps/core-api/src/modules/safety/safety.service.ts:87]

