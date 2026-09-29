# Practice card — Write a database migration

**When**: any schema change.
**Prerequisites**: SKL-007/2, SKL-008/2. Read `TS-02-database-spec.md`.
**Hard constraints**: forward-only; a node may have been offline for months and will apply several releases at once; both PostgreSQL and MySQL must work; upgrades happen on a shop counter at 8 a.m. with no DBA.

## Steps

1. **Name it** `V<yyyyMMddHHmm>__<verb>_<object>.sql` in the owning module's Flyway location. Never edit a migration that has been released — add a new one.
2. **Stay in the compatible subset** (`ADR-008`, `TS-02` §9): no PostgreSQL-only types in shared tables, no `CHECK` constraints MySQL ignores silently, portable identifier quoting, explicit collation-independent comparisons. Dialect-specific migrations go in `postgresql/`/`mysql/` locations only when unavoidable, and must be behaviourally equivalent.
3. **Prefer expand/contract** for anything with data:
   - Release N: add the new nullable column/table; backfill in batches; dual-write in code.
   - Release N+1: make it non-null / switch reads.
   - Release N+2: drop the old column.
   A single-release rename on a live shop database is how you lose a morning of billing.
4. **Batch backfills** (e.g. 5,000 rows per statement with a bounded loop or a repeatable job) and make them **resumable** — a counter can lose power mid-upgrade.
5. **Never destroy financial data.** Dropping or rewriting `journal_line`, `stock_movement`, `sales_invoice*` or `audit_event` requires an ADR and a second reviewer. Corrections are reversals, not updates.
6. **Indexes**: create concurrently on PostgreSQL where the migration runs online; keep the MySQL equivalent. Justify each index with the query it serves.
7. **Sync impact**: new replicated tables must be registered with a conflict policy; new columns on replicated tables must be nullable or defaulted so older nodes' payloads still apply.
8. **Projections**: if the change affects a projection's inputs, add a rebuild step (projections are rebuildable by design — use that instead of hand-patching).
9. **Test**: apply from a database at release N-3 with realistic data volume; run both dialects under Testcontainers; measure the migration duration on the Large fixture and record it in the release notes if it exceeds 30 s; test a power-cut mid-migration (kill the process, restart, must converge).

## Checklist

- [ ] Forward-only; released migrations untouched
- [ ] Portable SQL, or equivalent per-dialect pairs
- [ ] Expand/contract for data-bearing changes
- [ ] Backfill batched and resumable
- [ ] No destructive change to financial or audit tables (or ADR + second reviewer)
- [ ] Sync registration/compatibility handled
- [ ] Tested from N-3 on both dialects; duration measured; interruption tested
