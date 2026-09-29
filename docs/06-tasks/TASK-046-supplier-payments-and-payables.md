# TASK-046 — Supplier payments and payables

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.accounting` |
| Traces to | FR-335..FR-340, BR-042 |
| Depends on | TASK-043 |
| Size | M |
| Primary role | Accounting specialist |

## Goal

Pay suppliers, apply payments correctly, and always know what is owed.

## Scope

**In:** Payment voucher, allocation against invoices/debit notes, part payments, advances and adjustment, ageing report, due-date alerts, payment modes with ledger mapping.

**Out:** Bank payment file generation (backlog).

## Implementation notes

- Payment allocation is explicit; unallocated advances sit in a separate account until applied.
- Ageing buckets are configurable and must reconcile to the payables control account at all times.
- Payments post immediately; reversals are new entries with a reason.

## Acceptance criteria

1. Part payment across three invoices allocates exactly and leaves correct balances.
2. Payables ageing total equals the payables control account balance in the daily reconciliation job.
3. Due-date alerts fire per the configured lead time and are deduplicated.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
