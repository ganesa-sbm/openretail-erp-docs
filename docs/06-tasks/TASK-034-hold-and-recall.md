# TASK-034 — Hold, recall, park and multi-bill handling

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.sales.pos` |
| Traces to | FR-233..FR-236 |
| Depends on | TASK-032 |
| Size | S |
| Primary role | UI engineer |

## Goal

Let a cashier serve the next customer without losing the current bill — the single most-used convenience in a busy shop.

## Scope

**In:** Hold with auto-label, recall list with search, multiple concurrent bills per counter, park across shifts (permission-gated), expiry of stale holds, stock reservation policy.

**Out:** Cross-counter recall (explicitly out of scope in Phase 1; document why).

## Implementation notes

- Held bills persist locally so a crash or power cut does not lose them.
- Reservation policy is explicit: held bills do not reserve stock by default; document the consequence at the counter.
- Stale holds are surfaced at day close and must be resolved before closing.

## Acceptance criteria

1. A held bill survives an abrupt process kill and is recallable after restart with all lines and the customer.
2. Three concurrent bills at one counter can be switched between with single keystrokes.
3. Day close blocks on unresolved holds with a clear list.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
