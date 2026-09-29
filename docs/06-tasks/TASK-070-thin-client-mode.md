# TASK-070 — Thin-client mode for the JavaFX client

| Field | Value |
| --- | --- |
| Phase | 2 |
| Module(s) | `erp.client.fx` |
| Traces to | FR-575..FR-578, DEP-02 |
| Depends on | TASK-069 |
| Size | M |
| Primary role | UI engineer |

## Goal

Let counters run against the store server without a local database, and fall back gracefully when it dies.

## Scope

**In:** Remote repository implementations over REST, connection management, caching of masters, mode switching (standalone / thin / hybrid), degradation to local mode with a local DB when configured, reconnect and drain.

**Out:** A distinct web client (TASK-072).

## Implementation notes

- The client code above the repository boundary must not know which mode it is in — same screens, same tests.
- Hybrid mode keeps a local DB warm so failover is seconds, not a restart.
- Mode and connectivity are always visible to the cashier.

## Acceptance criteria

1. Killing the server mid-bill in hybrid mode keeps billing alive locally and drains to the server on recovery with no duplicates.
2. The same TestFX POS scenario passes in standalone, thin and hybrid modes.
3. Master-data cache invalidates within the documented propagation time after a server-side change.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
