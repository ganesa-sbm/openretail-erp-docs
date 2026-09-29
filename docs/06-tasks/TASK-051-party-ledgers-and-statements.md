# TASK-051 — Party ledgers, statements and reconciliation

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.accounting` |
| Traces to | FR-421..FR-425, BR-046 |
| Depends on | TASK-050 |
| Size | M |
| Primary role | Accounting specialist |

## Goal

Answer 'what does this customer/supplier owe' instantly and provably.

## Scope

**In:** Subsidiary ledgers per party, running balance, statement generation and emailing, ageing by bucket, control-account reconciliation job, on-account adjustment.

**Out:** Dunning automation (backlog).

## Implementation notes

- Subsidiary ledgers are projections of journal lines, not a parallel source of truth — they must be rebuildable.
- A daily reconciliation job asserts subsidiary totals equal the control accounts and alerts on any drift.
- Statements are documents: reproducible for a past date, not just 'as of now'.

## Acceptance criteria

1. Rebuilt party ledgers reproduce the current balances exactly on the Large fixture.
2. Reconciliation job detects a deliberately introduced drift and alerts with the party and amount.
3. A statement regenerated for a past date matches the one issued then (golden file).

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
