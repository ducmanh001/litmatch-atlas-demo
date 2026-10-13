# Database tables owned by module (common)

id: db:(common)
module: module:(common)
tables: admin_audit_logs
see: ../modules/common.md

### table: admin_audit_logs

Log hành động nhạy cảm của admin/moderator (docs/06 dòng 13, docs/05 dòng 96) — append-only tuyệt đối, DB trigger chặn UPDATE/DELETE (migration 1753700000000, cùng pattern `forbid_ledger_entry_mutation`). Không có method update/delete nào ở service layer — bất biến ngay từ API, không chỉ ở DB.

Entity: AdminAuditLog (apps/core-api/src/common/audit/audit-log.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | uuid |  | gen_random_uuid() | PK |  |
| actor_user_id | uuid |  |  |  |  |
| action | character varying(100) |  |  |  | vd 'user.banned', 'user.unbanned' — namespace tự do, không phải mã lỗi DomainException. |
| target_type | character varying(50) |  |  |  |  |
| target_id | character varying(128) |  |  |  |  |
| metadata | jsonb | null |  |  |  |
| created_at | timestamp with time zone |  | now() |  |  |

Indexes: idx_admin_audit_logs_actor (actor_user_id); idx_admin_audit_logs_target (target_id)

Triggers: trg_admin_audit_logs_append_only (append-only)

Access: (common)(owner) r1/w2 via repository [apps/core-api/src/common/audit/audit-log.service.ts:30, apps/core-api/src/common/audit/audit-log.service.ts:31]

