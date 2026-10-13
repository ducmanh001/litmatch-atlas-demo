# Database tables owned by module auth

id: db:auth
module: module:auth
tables: auth_identities, phone_otps, refresh_tokens
see: ../modules/auth.md

### table: auth_identities

1 cách đăng nhập của 1 user. unique(provider, providerUid) ở tầng DB — 2 request đăng ký song song cùng deviceId/phone không thể tạo 2 account (docs/10 § 10.1.C).

Entity: AuthIdentity (apps/core-api/src/modules/auth/entities/auth-identity.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | uuid |  | gen_random_uuid() | PK |  |
| user_id | uuid |  |  | FK→users.id |  |
| provider | character varying(16) |  |  |  |  |
| provider_uid | character varying(255) |  |  |  |  |
| created_at | timestamp with time zone |  | now() |  |  |

Constraints: unique uq_auth_identities_provider_uid (provider,provider_uid)

Indexes: idx_auth_identities_user_id (user_id); UNIQUE uq_auth_identities_provider_uid (provider, provider_uid)

Access: auth(owner) r6/w5 via manager+manager.save(instance)+querybuilder+repository [apps/core-api/src/modules/auth/auth.service.ts:156, apps/core-api/src/modules/auth/auth.service.ts:175]

### table: phone_otps

OTP lưu dạng HMAC-SHA256(code, pepper) — attempt_count enforce ở server, không tin client.

Entity: PhoneOtp (apps/core-api/src/modules/auth/entities/phone-otp.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | uuid |  | gen_random_uuid() | PK |  |
| phone | character varying(20) |  |  |  |  |
| code_hash | character(64) |  |  |  |  |
| expires_at | timestamp with time zone |  |  |  |  |
| attempt_count | integer |  | 0 |  |  |
| consumed_at | timestamp with time zone | null |  |  |  |
| created_at | timestamp with time zone |  | now() |  |  |

Indexes: idx_phone_otps_phone_created (phone, created_at)

Access: auth(owner) r2/w5 via querybuilder+repository [apps/core-api/src/modules/auth/services/otp.service.ts:32, apps/core-api/src/modules/auth/services/otp.service.ts:52]

### table: refresh_tokens

Refresh token lưu dạng SHA-256 hash (không bao giờ lưu plaintext). `familyId` nhóm các token sinh ra từ cùng 1 lần login — phát hiện reuse (token đã rotate mà bị dùng lại) thì revoke cả family.

Entity: RefreshToken (apps/core-api/src/modules/auth/entities/refresh-token.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | uuid |  | gen_random_uuid() | PK |  |
| user_id | uuid |  |  | FK→users.id |  |
| token_hash | character(64) |  |  | UQ |  |
| family_id | uuid |  |  |  |  |
| expires_at | timestamp with time zone |  |  |  |  |
| revoked_at | timestamp with time zone | null |  |  |  |
| rotated_at | timestamp with time zone | null |  |  |  |
| created_at | timestamp with time zone |  | now() |  |  |

Constraints: unique uq_refresh_tokens_token_hash (token_hash)

Indexes: idx_refresh_tokens_family_id (family_id); idx_refresh_tokens_user_id (user_id); UNIQUE uq_refresh_tokens_token_hash (token_hash)

Access: auth(owner) r2/w14/lock1 via manager+manager(lock option)+querybuilder+repository [apps/core-api/src/modules/auth/repositories/typeorm-refresh-session.adapter.ts:18, apps/core-api/src/modules/auth/repositories/typeorm-refresh-session.adapter.ts:29]

