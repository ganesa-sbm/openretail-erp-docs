# TASK-022 — Inter-store and internal transfers

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.inventory` |
| Traces to | FR-142..FR-145, BR-017 |
| Depends on | TASK-019 |
| Size | M |
| Primary role | Core backend |

## Goal

Move stock between stores/locations with goods-in-transit visibility and no double counting.

## Scope

**In:** Transfer request, dispatch, in-transit location, receipt with discrepancy handling, transfer note printing, transfer valuation.

**Out:** Logistics integrations.

## Implementation notes

- Dispatch and receipt are two movements with an in-transit location in between — stock is never nowhere and never in two places.
- Short/excess receipt creates a discrepancy requiring approval, not a silent adjustment.
- Transfers between stores in different sync nodes must converge (append-only facts, TASK-068).

## Acceptance criteria

1. Dispatched-not-received stock appears in the in-transit report and in neither store's sellable stock.
2. Receiving less than dispatched raises a discrepancy and posts only the received quantity.
3. A transfer created offline at the sending node lands correctly at the receiving node after sync.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
