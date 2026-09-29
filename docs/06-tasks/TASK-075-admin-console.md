# TASK-075 — Administration console: users, settings, audit, sync and health

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.client.fx` |
| Traces to | FR-597..FR-604, BR-065 |
| Depends on | TASK-010 |
| Size | L |
| Primary role | UI engineer |

## Goal

Give the admin one place to run the system: people, configuration, evidence and node state.

## Scope

**In:** User/role management, permission matrix editor, settings screens with validation, audit viewer with filters and export, conflict inbox with resolution, node health and sync status, backup status, licence/version info.

**Out:** Cloud fleet management (TASK-089).

## Implementation notes

- The audit viewer must answer real questions fast: who gave this discount, who changed this price, what happened on this document — filters and indexes designed around those queries.
- The conflict inbox requires a resolution note and audits the decision (per the sync practice card).
- Settings changes that affect posting or tax require confirmation and are audited with before/after.

## Acceptance criteria

1. An admin answers each documented audit question in under three clicks on the Large fixture.
2. Resolving a conflict from the inbox propagates to all nodes and is audited with the note.
3. Changing a control-account mapping is blocked while unposted quarantined events exist.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
