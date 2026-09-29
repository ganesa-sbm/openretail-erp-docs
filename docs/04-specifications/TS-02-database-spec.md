# TS-02 — Database specification

Engine: PostgreSQL 15+ (reference), MySQL 8 subset (ADR-008). Migrations: Flyway, forward-only, `V<version>__<name>.sql` per dialect location.

## 1. Common columns

Every business table:

```sql
id           uuid primary key,              -- UUIDv7, client-generated
tenant_id    uuid not null,
node_id      uuid not null,                 -- origin node
version      bigint not null default 0,     -- optimistic locking + sync
created_at   timestamptz not null default now(),
created_by   uuid not null,
updated_at   timestamptz not null default now(),
updated_by   uuid not null,
deleted      boolean not null default false
```

Store-scoped tables add `store_id uuid not null`. Every table has an index leading with `(tenant_id[, store_id])`.

## 2. Core tables (abridged DDL)

```sql
create table product (
  id uuid primary key, tenant_id uuid not null,
  code text not null, name text not null, short_name text,
  category_id uuid references category, brand_id uuid references brand,
  hsn_code text, tax_class_id uuid not null references tax_class,
  base_uom_id uuid not null references uom,
  batch_tracked boolean not null default false,
  expiry_tracked boolean not null default false,
  serial_tracked boolean not null default false,
  reorder_level numeric(18,3), reorder_qty numeric(18,3),
  attributes jsonb not null default '{}'::jsonb,
  active boolean not null default true,
  -- common columns …
  unique (tenant_id, code)
);

create table product_variant (
  id uuid primary key, tenant_id uuid not null,
  product_id uuid not null references product,
  sku text not null, axis1 text, axis2 text, axis3 text,
  price_delta numeric(18,4) not null default 0,
  attributes jsonb not null default '{}'::jsonb,
  unique (tenant_id, sku)
);

create table barcode (
  id uuid primary key, tenant_id uuid not null,
  variant_id uuid not null references product_variant,
  code text not null, kind text not null,      -- EAN13, CODE128, INTERNAL, SUPPLIER
  unique (tenant_id, code)
);

create table stock_batch (
  id uuid primary key, tenant_id uuid not null, store_id uuid not null,
  variant_id uuid not null references product_variant,
  batch_code text not null,
  manufacture_date date, expiry_date date,
  mrp numeric(18,4), landed_cost numeric(18,4) not null default 0,
  unique (tenant_id, store_id, variant_id, batch_code)
);
create index on stock_batch (tenant_id, store_id, variant_id, expiry_date)
  where deleted = false;   -- FEFO selection (PostgreSQL only)

create table sales_invoice (
  id uuid primary key, tenant_id uuid not null, store_id uuid not null,
  counter_id uuid not null, series text not null, seq integer not null,
  number text generated always as (series || '/' || lpad(seq::text,6,'0')) stored,
  business_date date not null, customer_id uuid references party,
  status text not null,                              -- FINALISED, VOIDED
  subtotal numeric(18,4) not null, discount_total numeric(18,4) not null,
  tax_total numeric(18,4) not null, round_off numeric(18,4) not null,
  grand_total numeric(18,4) not null,
  voucher_id uuid references voucher,
  unique (tenant_id, series, seq)
);

create table voucher (
  id uuid primary key, tenant_id uuid not null, store_id uuid not null,
  type text not null, series text not null, seq integer not null,
  business_date date not null, narration text,
  source_type text, source_id uuid,
  reversed_by uuid references voucher, reverses uuid references voucher,
  status text not null,
  unique (tenant_id, series, seq)
);

create table journal_line (
  id uuid primary key, voucher_id uuid not null references voucher,
  line_no int not null, account_id uuid not null references account,
  debit numeric(18,4) not null default 0, credit numeric(18,4) not null default 0,
  cost_centre_id uuid, party_id uuid, narration text,
  check (debit >= 0 and credit >= 0 and not (debit > 0 and credit > 0))
);
create index on journal_line (account_id, voucher_id);

create table audit_event (
  id uuid primary key, tenant_id uuid not null, node_id uuid not null,
  seq bigserial not null, occurred_at timestamptz not null,
  actor_id uuid, actor_name text, event_type text not null,
  entity text, entity_id uuid, reason text,
  before_json jsonb, after_json jsonb,
  prev_hash bytea, hash bytea not null,
  unique (node_id, seq)
);
```

## 3. Constraints and integrity

| Rule | Mechanism |
| --- | --- |
| Voucher balanced | Domain check + deferred constraint trigger comparing Σ debit/credit per voucher (PostgreSQL); application-only in MySQL |
| Numbering unique | `unique (tenant_id, series, seq)` |
| No hard delete of financial rows | Revoked DELETE privilege on financial tables for the application role; soft-delete flag used elsewhere |
| Audit immutability | Application role has INSERT+SELECT only on `audit_event`; UPDATE/DELETE revoked |
| Cross-store leakage | Hibernate filter injecting `tenant_id`/`store_id` predicates + integration tests asserting it |
| Referential integrity across plugin schemas | Application-level only (no FKs, plugin design §4) |

## 4. Indexing policy

Baseline indexes: every FK, every `(tenant_id, store_id, business_date)` on document tables, barcode lookup (unique), `stock_movement (tenant_id, store_id, variant_id, business_date)`, `journal_line (account_id)`, `party (tenant_id, phone_hmac)`. Partial indexes exclude soft-deleted rows. Index additions require an EXPLAIN plan in the PR; unused indexes are pruned quarterly using `pg_stat_user_indexes`.

## 5. Partitioning and growth

Expected volumes per busy store per year: ~250k invoices, ~1.2M invoice lines, ~2.5M stock movements, ~3M journal lines. Under these volumes PostgreSQL on an SSD needs no partitioning; `stock_movement`, `journal_line` and `audit_event` are created as range-partitionable by `business_date` (yearly) and partitioning is enabled when a table exceeds 50M rows. Archive schema moves closed years out of the live tables (data architecture §7).

## 6. Projections

`stock_snapshot`, `account_balance_period`, `sales_daily_agg`, `sales_item_agg`, `cashier_daily_agg`, `customer_agg`, `tender_agg`, `purchase_agg` — plain tables (not materialised views) so they can be updated incrementally and rebuilt in batches; each carries `computed_at` and a `source_watermark` for verification.

## 7. Encryption

Encrypted columns are `bytea` holding AES-256-GCM (nonce ‖ ciphertext ‖ tag) with the row id as associated data, written through a Hibernate `AttributeConverter`. Searchable encrypted fields carry a companion `*_hmac bytea` (HMAC-SHA256 with a tenant search key) for exact match. Encrypted: `party.name/phone/email/address`, `party.bank_details`, `user.totp_secret`, `setting.value` where flagged secret.

## 8. Migration rules

1. Forward-only; never edit a released migration.
2. Additive first: add nullable column → backfill in batches → add constraint in a later release.
3. No long-running locks: `create index concurrently`, `alter table … add column` without default rewrite (PG 11+ safe), batched backfills with progress logging.
4. Every migration must be safe on a node that skipped several versions (NFR-022); migrations are idempotent and independently ordered.
5. Data migrations that touch financial rows must produce an audit event and a before/after count in the migration log.
6. A migration test harness runs upgrades from the last 3 released schema versions plus a "long-offline" fixture on every CI run.

## 9. Database roles

| Role | Privileges |
| --- | --- |
| `erp_app` | SELECT/INSERT/UPDATE on business tables; INSERT/SELECT only on `audit_event`; no DELETE on financial tables; no DDL |
| `erp_migrate` | DDL + full DML, used only by Flyway at startup |
| `erp_readonly` | SELECT on projections and reporting views, for external BI |
| `erp_backup` | Replication/backup privileges only |

Connection pooling: HikariCP, 10 connections on a counter, 40 on a store server; statement timeout 5 s for interactive, 60 s for reports, unlimited for migrations.
