# Database tables owned by module economy

id: db:economy
module: module:economy
tables: iap_products, iap_receipts, ledger_accounts, ledger_entries, outbox_events, payos_packages, payos_payment_orders, transactions, vip_plans, wallets
see: ../modules/economy.md

### table: iap_products

Catalog: product id trên store → số diamond. Giá tiền thật nằm ở store, đây chỉ là mapping.

Entity: IapProduct (apps/core-api/src/modules/economy/entities/iap.entities.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| product_id | character varying(128) |  |  | PK |  |
| provider | character varying(16) |  |  |  |  |
| diamonds | bigint |  |  |  |  |
| active | boolean |  | true |  |  |
| created_at | timestamp with time zone |  | now() |  |  |

Constraints: check iap_products_diamonds_check ((diamonds > 0))

Access: economy(owner) r3/w0 via repository [apps/core-api/src/modules/economy/economy.service.ts:114, apps/core-api/src/modules/economy/economy.service.ts:152]

### table: iap_receipts

Receipt đã verify — nguồn đối soát với Apple/Google. Unique (provider, provider_transaction_id) chặn double-credit ở DB.

Entity: IapReceipt (apps/core-api/src/modules/economy/entities/iap.entities.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | uuid |  | gen_random_uuid() | PK |  |
| provider | character varying(16) |  |  |  |  |
| provider_transaction_id | character varying(255) |  |  |  |  |
| user_id | uuid |  |  | FK→users.id |  |
| product_id | character varying(128) |  |  |  |  |
| status | character varying(16) |  | 'credited'::character varying |  |  |
| transaction_id | uuid | null |  | FK→transactions.id |  |
| raw_payload | jsonb |  | '{}'::jsonb |  |  |
| created_at | timestamp with time zone |  | now() |  |  |
| refund_transaction_id | uuid | null |  | FK→transactions.id | Trỏ tới transaction type=reversal đã hoàn receipt này — null nếu chưa refund. |
| refund_checked_at | timestamp with time zone | null |  |  | Watermark job quét backstop (IapRefundPollService) — null nghĩa là chưa từng được quét. |

Constraints: unique uq_iap_receipts_provider_txn (provider,provider_transaction_id)

Indexes: idx_iap_receipts_refund_checked (refund_checked_at) WHERE ((status)::text = 'credited'::text); UNIQUE uq_iap_receipts_provider_txn (provider, provider_transaction_id)

Access: economy(owner) r2/w7/lock1 via manager+manager.save(instance)+querybuilder+raw-sql+repository [apps/core-api/src/modules/economy/economy.service.ts:347, apps/core-api/src/modules/economy/jobs/iap-refund-poll.service.ts:93]

### table: ledger_accounts

Tài khoản nội bộ của sổ cái. Unique (kind, user_id, currency) enforce bằng partial index ở migration.

Entity: LedgerAccount (apps/core-api/src/modules/economy/entities/ledger-account.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | uuid |  | gen_random_uuid() | PK |  |
| kind | character varying(32) |  |  |  |  |
| user_id | uuid | null |  | FK→users.id |  |
| currency | character varying(8) |  | 'DIA'::character varying |  |  |
| created_at | timestamp with time zone |  | now() |  |  |

Indexes: UNIQUE uq_ledger_accounts_system (kind, currency) WHERE (user_id IS NULL); UNIQUE uq_ledger_accounts_user (kind, user_id, currency) WHERE (user_id IS NOT NULL)

Access: economy(owner) r6/w1 via querybuilder+raw-sql+repository [apps/core-api/src/modules/economy/economy.service.ts:244, apps/core-api/src/modules/economy/jobs/reconciliation.service.ts:186]

### table: ledger_entries

Bút toán Nợ/Có — NGUỒN SỰ THẬT DUY NHẤT của tiền (luật 2 AGENTS.md). Append-only tuyệt đối: DB trigger chặn UPDATE/DELETE (migration 1752000000000). amount là bigint dương — TypeORM trả về string cho bigint, xử lý bằng BigInt() khi tính toán.

Entity: LedgerEntry (apps/core-api/src/modules/economy/entities/ledger-entry.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | uuid |  | gen_random_uuid() | PK |  |
| transaction_id | uuid |  |  | FK→transactions.id |  |
| account_id | uuid |  |  | FK→ledger_accounts.id |  |
| direction | character varying(6) |  |  |  |  |
| amount | bigint |  |  |  |  |
| currency | character varying(8) |  |  |  |  |
| created_at | timestamp with time zone |  | now() |  |  |

Constraints: check ledger_entries_amount_check ((amount > 0)); check ledger_entries_direction_check (((direction)::text = ANY ((ARRAY['debit'::character varying, 'credit'::character varying])::text[])))

Indexes: idx_ledger_entries_account_created (account_id, created_at); idx_ledger_entries_transaction (transaction_id)

Triggers: trg_ledger_entries_append_only (append-only)

Access: economy(owner) r5/w2 via manager+manager.querybuilder+manager.save(instance)+raw-sql+repository [apps/core-api/src/modules/economy/economy.service.ts:244, apps/core-api/src/modules/economy/jobs/reconciliation.service.ts:131]

### table: outbox_events

Outbox Pattern (docs/03 § 3.6): event ghi cùng DB transaction với nghiệp vụ, relay publish Kafka sau.

Entity: OutboxEvent (apps/core-api/src/modules/economy/entities/outbox-event.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | uuid |  | gen_random_uuid() | PK |  |
| topic | character varying(128) |  |  |  |  |
| event_type | character varying(64) |  |  |  |  |
| payload | jsonb |  |  |  |  |
| attempts | integer |  | 0 |  |  |
| published_at | timestamp with time zone | null |  |  |  |
| created_at | timestamp with time zone |  | now() |  |  |
| dead_lettered_at | timestamp with time zone | null |  |  | Terminal relay state; the original payload remains available for inspection/replay. |
| last_error | text | null |  |  |  |

Indexes: idx_outbox_events_relayable (created_at) WHERE ((published_at IS NULL) AND (dead_lettered_at IS NULL)); idx_outbox_events_unpublished (created_at) WHERE (published_at IS NULL)

Access: economy(owner) r0/w7/lock1 via manager+manager.save(instance)+querybuilder [apps/core-api/src/modules/economy/economy.service.ts:429, apps/core-api/src/modules/economy/economy.service.ts:648]

### table: payos_packages

Catalog nạp web Việt Nam. Giá/DIA chỉ được đọc từ đây, không nhận từ client.

Entity: PayosPackage (apps/core-api/src/modules/economy/entities/payos.entities.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| package_id | character varying(64) |  |  | PK |  |
| amount_vnd | bigint |  |  |  |  |
| diamonds | bigint |  |  |  |  |
| active | boolean |  | true |  |  |
| created_at | timestamp with time zone |  | now() |  |  |

Constraints: check payos_packages_amount_vnd_check ((amount_vnd > 0)); check payos_packages_diamonds_check ((diamonds > 0))

Access: economy(owner) r2/w0 via repository [apps/core-api/src/modules/economy/services/payos.service.ts:69, apps/core-api/src/modules/economy/services/payos.service.ts:92]

### table: payos_payment_orders

Intent payOS tách khỏi ledger: tạo link chưa mint DIA; chỉ webhook verified mới ghi sổ. amount/diamonds/packageId là snapshot bất biến tại thời điểm tạo order.

Entity: PayosPaymentOrder (apps/core-api/src/modules/economy/entities/payos.entities.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | uuid |  | gen_random_uuid() | PK |  |
| user_id | uuid |  |  | FK→users.id |  |
| package_id | character varying(64) |  |  | FK→payos_packages.package_id |  |
| amount_vnd | bigint |  |  |  |  |
| diamonds | bigint |  |  |  |  |
| currency | character varying(3) |  | 'VND'::character varying |  |  |
| order_code | bigint |  | nextval('payos_order_code_seq'::regclass) | UQ | bigint nhưng serialize string — JavaScript không làm tròn mã thanh toán. |
| idempotency_key | character varying(255) |  |  | UQ |  |
| request_hash | character(64) |  |  |  |  |
| status | character varying(32) |  | 'pending'::character varying |  |  |
| payment_link_id | character varying(128) | null |  | UQ |  |
| checkout_url | text | null |  |  |  |
| qr_code | text | null |  |  |  |
| expires_at | timestamp with time zone |  |  |  |  |
| transaction_id | uuid | null |  | FK→transactions.id UQ | Mỗi order chỉ được nối với đúng một transaction credit. |
| created_at | timestamp with time zone |  | now() |  |  |
| updated_at | timestamp with time zone |  | now() |  |  |

Constraints: check payos_payment_orders_amount_vnd_check ((amount_vnd > 0)); check payos_payment_orders_currency_check (((currency)::text = 'VND'::text)); check payos_payment_orders_diamonds_check ((diamonds > 0)); check payos_payment_orders_status_check (((status)::text = ANY ((ARRAY['pending'::character varying, 'paid'::character varying, 'cancelled'::character varying, 'expired'::character varying])::text[])…; unique uq_payos_payment_orders_idempotency_key (idempotency_key); unique uq_payos_payment_orders_order_code (order_code); unique uq_payos_payment_orders_payment_link_id (payment_link_id); unique uq_payos_payment_orders_transaction_id (transaction_id)

Indexes: idx_payos_payment_orders_user_created (user_id, created_at); UNIQUE uq_payos_payment_orders_idempotency_key (idempotency_key); UNIQUE uq_payos_payment_orders_order_code (order_code); UNIQUE uq_payos_payment_orders_payment_link_id (payment_link_id); UNIQUE uq_payos_payment_orders_transaction_id (transaction_id)

Access: economy(owner) r6/w6/lock1 via manager+querybuilder+repository [apps/core-api/src/modules/economy/services/payos.service.ts:87, apps/core-api/src/modules/economy/services/payos.service.ts:108]

### table: transactions

Metadata 1 giao dịch nghiệp vụ — KHÔNG chứa số tiền (số tiền nằm ở ledger_entries, tránh 2 nguồn sự thật — docs/02). Mang idempotency_key unique ở DB (luật 2 AGENTS.md).

Entity: LedgerTransaction (apps/core-api/src/modules/economy/entities/transaction.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | uuid |  | gen_random_uuid() | PK |  |
| type | character varying(32) |  |  |  |  |
| status | character varying(16) |  | 'completed'::character varying |  |  |
| idempotency_key | character varying(255) |  |  | UQ |  |
| request_hash | character(64) |  |  |  | SHA-256 của nội dung request — phát hiện key trùng nhưng payload khác (409). |
| actor_user_id | uuid | null |  | FK→users.id |  |
| reversal_of | uuid | null |  | FK→transactions.id |  |
| metadata | jsonb |  | '{}'::jsonb |  |  |
| created_at | timestamp with time zone |  | now() |  |  |

Constraints: unique uq_transactions_idempotency_key (idempotency_key)

Indexes: idx_transactions_actor_created (actor_user_id, created_at); UNIQUE uq_transactions_idempotency_key (idempotency_key)

Access: economy(owner) r7/w3 via manager+querybuilder+repository [apps/core-api/src/modules/economy/economy.service.ts:674, apps/core-api/src/modules/economy/services/ledger.service.ts:59]

### table: vip_plans

Gói VIP mua bằng diamond — giá/thời hạn ở DB, không hardcode (docs/05 § 5.1).

Entity: VipPlan (apps/core-api/src/modules/economy/entities/vip-plan.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| id | character varying(64) |  |  | PK |  |
| tier | character varying(16) |  |  |  |  |
| days | integer |  |  |  |  |
| price_diamond | bigint |  |  |  |  |
| active | boolean |  | true |  |  |
| created_at | timestamp with time zone |  | now() |  |  |

Constraints: check vip_plans_days_check ((days > 0)); check vip_plans_price_diamond_check ((price_diamond > 0))

Access: economy(owner) r5/w2 via repository [apps/core-api/src/modules/economy/economy.service.ts:134, apps/core-api/src/modules/economy/economy.service.ts:153]

### table: wallets

SNAPSHOT dẫn xuất từ ledger — KHÔNG phải nguồn sự thật (luật 2 AGENTS.md). Chỉ LedgerService được ghi, trong cùng DB transaction với bút toán. Rebuild được bất cứ lúc nào bằng EconomyService.rebuildWallet(). `balance` CÓ THỂ âm sau refund/chargeback (user nợ diamond — docs/services/economy-service.md § 5); chống tiêu quá số dư là guard tầng ứng dụng (SELECT ... FOR UPDATE + balance - amount >= 0), không phải CHECK ở DB.

Entity: Wallet (apps/core-api/src/modules/economy/entities/wallet.entity.ts)

| column | type | null | default | key | meaning |
|---|---|---|---|---|---|
| user_id | uuid |  |  | PK FK→users.id |  |
| balance | bigint |  | 0 |  |  |
| earnings | bigint |  | 0 |  |  |
| vip_tier | character varying(16) | null |  |  |  |
| vip_expires_at | timestamp with time zone | null |  |  |  |
| updated_at | timestamp with time zone |  | now() |  |  |

Constraints: check wallets_earnings_check ((earnings >= 0))

Access: economy(owner) r9/w5/lock2 via manager+manager.save(instance)+querybuilder+raw-sql+repository+repository(lock option) [apps/core-api/src/modules/economy/economy.service.ts:85, apps/core-api/src/modules/economy/economy.service.ts:102]

