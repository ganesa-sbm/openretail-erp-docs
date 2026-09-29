# TASK-044 — Purchase returns and debit notes

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.purchasing` |
| Traces to | FR-327..FR-330 |
| Depends on | TASK-043 |
| Size | M |
| Primary role | Core backend |

## Goal

Send goods back and get the money or credit back, with the ledger following.

## Scope

**In:** Return against GRN or invoice, reason codes, batch selection, debit note generation, supplier credit tracking, stock and ledger reversal at original cost.

**Out:** Freight claims.

## Implementation notes

- Returns reverse at the original receipt cost, not current cost, or valuation breaks.
- Debit notes offset payables and appear on the supplier statement.
- Returning a batch already partly sold is blocked with a clear message.

## Acceptance criteria

1. Return of a received batch reverses stock and posts a debit note that reconciles on the supplier statement.
2. Cost reversal leaves the valuation report tying to the inventory control account.
3. A return exceeding the received quantity is rejected.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
