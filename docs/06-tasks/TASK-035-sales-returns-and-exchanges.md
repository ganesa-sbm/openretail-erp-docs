# TASK-035 — Sales returns, exchanges and credit notes

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.sales.pos, erp.accounting` |
| Traces to | FR-237..FR-243, BR-029 |
| Depends on | TASK-031 |
| Size | L |
| Primary role | Core backend |

## Goal

Handle returns correctly — the path most prone to fraud and to accounting error.

## Scope

**In:** Return against invoice (full/partial), return without invoice (permission + policy), exchange as a single transaction, credit note generation, refund tenders, restocking with batch/condition, return window policy, reason codes.

**Out:** Warranty/RMA workflows.

## Implementation notes

- Returns reference the original line where possible so cost, tax and discount are reversed at the original rates, not current ones.
- Refund tender must default to the original tender; cash refunds above a threshold require approval.
- Returned stock goes back with its original batch and cost, or to a quarantine location if damaged.

## Acceptance criteria

1. A partial return of one line from a discounted multi-line invoice reverses proportional discount and tax exactly.
2. Returning more than was sold is impossible across repeated partial returns (test with three sequential returns).
3. An exchange produces one net document, correct stock in both directions, and a balanced posting.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
