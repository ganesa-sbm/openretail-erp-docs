# TASK-021 — Stock take and adjustments

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.inventory` |
| Traces to | FR-137..FR-141, BR-016 |
| Depends on | TASK-019 |
| Size | M |
| Primary role | Core backend |

## Goal

Let a shop count stock without closing, and turn the result into auditable adjustments.

## Scope

**In:** Stock-take sessions (full/partial/cyclic), count sheets, scanner-assisted counting, variance calculation, approval, adjustment posting with reason codes, variance report.

**Out:** Handheld app (TASK-088).

## Implementation notes

- Counts are recorded against a snapshot taken at session start; movements during the count are reconciled, not ignored.
- Adjustment posting is a single approved operation producing movements plus the accounting entry.
- Variance above a configurable threshold requires a higher approval level (TASK-029).

## Acceptance criteria

1. A count performed while sales continue produces variance that accounts for the intervening movements.
2. Adjustments post matching stock and ledger entries; the reason code appears in the variance report.
3. A partial count of one category does not affect other categories' stock.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
