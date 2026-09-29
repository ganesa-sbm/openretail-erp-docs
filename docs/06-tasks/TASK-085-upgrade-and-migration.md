# TASK-085 — Upgrade, migration orchestration and rollback

| Field | Value |
| --- | --- |
| Phase | 2 |
| Module(s) | `packaging, erp.core.persistence` |
| Traces to | NFR-025, BR-073 |
| Depends on | TASK-013 |
| Size | L |
| Primary role | DevOps |

## Goal

Make upgrading a shop a non-event, including a shop that skipped three releases.

## Scope

**In:** Version compatibility matrix, pre-upgrade backup and health check, migration orchestration across modules and plugins, progress UI, resumable interrupted upgrade, rollback procedure, staged rollout across counters, post-upgrade verification.

**Out:** Continuous auto-update (deliberately excluded for shop machines).

## Implementation notes

- Upgrade always takes a verified backup first and refuses to proceed if backup or disk checks fail.
- Counters and the server may briefly differ in version: the API must tolerate one minor version skew, tested explicitly.
- An interrupted upgrade (power cut) must resume or roll back cleanly — never leave a half-migrated database.

## Acceptance criteria

1. Upgrading from each of the last three releases succeeds on the Large fixture within the documented window.
2. Killing the process mid-migration and restarting converges to a consistent, working state.
3. A staged rollout with mixed versions across counters keeps billing working (skew test).

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
