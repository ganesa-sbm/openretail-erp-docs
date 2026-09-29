# TASK-080 — Node health monitoring and operations dashboard

| Field | Value |
| --- | --- |
| Phase | 2 |
| Module(s) | `erp.server, erp.analytics` |
| Traces to | NFR-021, FR-621..FR-624 |
| Depends on | TASK-012 |
| Size | M |
| Primary role | DevOps |

## Goal

Let whoever supports the shops see trouble before the shop calls.

## Scope

**In:** Per-node health metrics (uptime, version, disk, DB size, sync backlog, last backup, clock offset, printer status), health dashboard, thresholds and alerts, version/patch inventory, remote diagnostic bundle request.

**Out:** Remote control of nodes (deliberately excluded); cloud aggregation (TASK-089).

## Implementation notes

- Health data flows through the normal sync path so it works without cloud and without extra infrastructure.
- Thresholds are configurable; defaults must be tuned to avoid alert fatigue.
- No remote code execution or remote control — support requests a diagnostic bundle, the shop approves.

## Acceptance criteria

1. The dashboard shows every node's state and flags a node with a stalled sync, failed backup or low disk.
2. Alerts fire at thresholds and clear automatically on recovery.
3. A diagnostic bundle can be requested and delivered through sync without a direct connection.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
