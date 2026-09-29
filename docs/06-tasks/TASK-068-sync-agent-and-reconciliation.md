# TASK-068 — Sync agent, conflict records and reconciliation jobs

| Field | Value |
| --- | --- |
| Phase | 3 |
| Module(s) | `erp.sync` |
| Traces to | FR-563..FR-568, BR-058 |
| Depends on | TASK-067 |
| Size | L |
| Primary role | Core backend |

## Goal

Run sync unattended and prove, continuously, that nodes agree.

## Scope

**In:** Background agent with scheduling and adaptive backoff, connectivity detection, conflict record creation with both versions, merge tooling for natural-key duplicates, reconciliation jobs comparing per-entity hashes, backlog metrics and alerts.

**Out:** Manual conflict UI (TASK-075).

## Implementation notes

- Reconciliation compares content hashes per entity range rather than row-by-row — cheap enough to run nightly on every node.
- A conflict record keeps both versions forever; resolution is a new decision, not an overwrite of history.
- Sync must yield to billing: adaptive throttling keeps POS latency inside budget during large syncs.

## Acceptance criteria

1. A deliberately diverged row is detected by the nightly reconciliation with the entity, key and both hashes.
2. Duplicate natural keys created on two offline nodes produce a merge-able conflict record; merging remaps references in one transaction.
3. POS latency stays within budget while a 100k-change backlog syncs (measured).

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
