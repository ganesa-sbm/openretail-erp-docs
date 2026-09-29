# TASK-019 — Stock movement ledger and on-hand projection

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.inventory` |
| Traces to | FR-125..FR-131, ADR-002, BR-011..BR-014 |
| Depends on | TASK-018 |
| Size | L |
| Primary role | Core backend |

## Goal

Make stock a derived, auditable consequence of immutable movements rather than a mutable number someone can quietly edit.

## Scope

**In:** `stock_movement` append-only table, movement types (receipt, issue, sale, return, transfer in/out, adjustment, production in/out, wastage), posting service, on-hand projection with incremental update and full rebuild, as-of queries.

**Out:** Valuation (TASK-020).

## Implementation notes

- No code anywhere may update an on-hand column directly; on-hand is a projection (architecture test enforces the table's write access).
- Movements are never updated or deleted; corrections are reversing movements referencing the original.
- Projection update happens in the same transaction as the movement for read-your-writes at the counter; the full rebuild exists for recovery.
- Negative stock is configurable per store and defaults to blocked.

## Acceptance criteria

1. Rebuilding the projection from movements reproduces the current on-hand exactly on the Large fixture (hash comparison).
2. As-of on-hand for any past date matches the sum of movements up to that date.
3. Concurrent sales of the last unit at two counters resolve without producing negative stock when blocking is enabled.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
