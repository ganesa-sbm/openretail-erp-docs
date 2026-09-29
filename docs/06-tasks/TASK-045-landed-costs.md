# TASK-045 — Landed costs and cost allocation

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.purchasing, erp.inventory` |
| Traces to | FR-331..FR-334, BR-041 |
| Depends on | TASK-043 |
| Size | M |
| Primary role | Accounting specialist |

## Goal

Get the true cost of goods, including freight and duties, into the stock value.

## Scope

**In:** Landed-cost document, cost types, allocation bases (value, quantity, weight, volume), allocation to receipts/lines, revaluation of remaining stock, effect on COGS of already-sold units.

**Out:** Multi-currency import handling (Phase 5 backlog).

## Implementation notes

- Allocation to already-sold units posts to COGS adjustment; only remaining stock is revalued — document and test this split.
- Landed costs can arrive weeks after the receipt; the process must handle that without rewriting history.
- Allocation bases are configurable per cost type.

## Acceptance criteria

1. Allocating freight across a receipt updates unit costs per the chosen basis and the stock value report.
2. Allocation after partial sale splits correctly between stock revaluation and COGS adjustment.
3. All landed-cost postings are traceable from the receipt to the ledger entry.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
