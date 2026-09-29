# TASK-053 — Financial statements: trial balance, P&L, balance sheet

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.accounting` |
| Traces to | FR-432..FR-437, BR-048, NFR-004 |
| Depends on | TASK-049 |
| Size | L |
| Primary role | Accounting specialist |

## Goal

Give the owner and accountant the three reports that define whether the books work.

## Scope

**In:** Trial balance (any date range, with/without opening), P&L with configurable groupings, balance sheet with schedules, comparative periods, cost-centre dimension, drill-down to voucher, export.

**Out:** Cash-flow statement (backlog).

## Implementation notes

- Statements read from the ledger with balance projections for speed; the projection must be rebuildable and verified against direct computation nightly.
- Every figure drills down to the vouchers behind it — an owner who cannot verify a number will not trust the software.
- Report definitions (which accounts roll into which line) are configuration, not code.

## Acceptance criteria

1. Trial balance for a year of the Large fixture renders within the NFR-004 budget and sums to zero.
2. P&L plus balance sheet reconcile: net profit equals the movement in retained earnings.
3. Drill-down from any line reaches the source documents.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
