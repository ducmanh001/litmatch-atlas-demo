# Database tables owned by module support

id: db:support
module: module:support
tables: support_tickets
see: ../modules/support.md

### table: support_tickets

Entity: SupportTicket (apps/core-api/src/modules/support/entities/support-ticket.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | uuid |  | gen_random_uuid() | PK |  |
| user_id | uuid |  |  | FK→users.id |  |
| category | character varying(16) |  |  |  |  |
| message | character varying(2000) |  |  |  |  |
| status | character varying(16) |  | 'open'::character varying |  |  |
| staff_response | character varying(2000) | null |  |  |  |
| idempotency_key | character varying(128) |  |  |  |  |
| created_at | timestamp with time zone |  | now() |  |  |
| updated_at | timestamp with time zone |  | now() |  |  |

Constraints: check chk_support_ticket_category (((category)::text = ANY ((ARRAY['feedback'::character varying, 'bug'::character varying, 'idea'::character varying])::text[]))); check chk_support_ticket_status (((status)::text = ANY ((ARRAY['open'::character varying, 'in_progress'::character varying, 'resolved'::character varying, 'closed'::character varying])::text[…; unique uq_support_ticket_user_idempotency (user_id,idempotency_key)

Indexes: idx_support_tickets_status_created (status, created_at, id); idx_support_tickets_user_created (user_id, created_at, id); UNIQUE uq_support_ticket_user_idempotency (user_id, idempotency_key)

Access: support(owner) r3/w3/lock1 via manager+manager(lock option)+manager.save(instance)+querybuilder+repository [apps/core-api/src/modules/support/support.service.ts:36, apps/core-api/src/modules/support/support.service.ts:37]

