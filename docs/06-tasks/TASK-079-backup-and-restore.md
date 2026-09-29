# TASK-079 — Backup, restore and disaster recovery tooling

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.server, packaging` |
| Traces to | BR-071, NFR-007, NFR-008 |
| Depends on | TASK-013 |
| Size | L |
| Primary role | DevOps |

## Goal

Make data loss recoverable, and prove it regularly rather than hoping.

## Scope

**In:** Scheduled local backups with verification, retention and rotation, encrypted optional cloud/removable-media copies, one-click restore wizard, point-in-time recovery where supported, backup health alerts, documented DR runbook, restore drill tooling.

**Out:** Cloud storage provisioning (TASK-089).

## Implementation notes

- A backup that has not been verified does not count: every backup is checksum-verified and periodically test-restored automatically to a scratch location.
- Backup failure is an alert that reaches the owner (TASK-064), because silent backup failure is the classic catastrophe.
- Restore must be usable by a support engineer over the phone with a shopkeeper at the keyboard.

## Acceptance criteria

1. Scheduled backup meets the RPO in NFR-007 and restore meets the RTO on the minimum hardware profile.
2. A monthly automated test-restore verifies integrity and reports the result.
3. A full DR drill on a clean VM from a real backup reproduces the store exactly, including audit-chain verification.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
