# TASK-074 — Plugin administration, signing and distribution

| Field | Value |
| --- | --- |
| Phase | 2 |
| Module(s) | `erp.server, erp.client.fx` |
| Traces to | FR-593..FR-596 |
| Depends on | TASK-073 |
| Size | M |
| Primary role | DevOps |

## Goal

Let an administrator install and manage plugins safely on a shop's machines.

## Scope

**In:** Plugin admin UI (install from file/repository, enable/disable, view permissions and version), signing pipeline in CI, key management, a simple plugin repository index, rollout to nodes via sync, upgrade with data migration.

**Out:** A public marketplace.

## Implementation notes

- Installing a plugin displays its requested permissions and requires explicit acceptance, audited.
- Rollout to counters flows through the normal distribution path so an offline node picks it up on reconnect.
- Plugin data migrations run under the same rules as core migrations (TASK-085).

## Acceptance criteria

1. Installing, enabling, disabling and uninstalling a plugin works from the UI and is audited.
2. A tampered plugin JAR fails signature verification and is refused.
3. A plugin upgrade with a schema change applies cleanly and is rollback-tested.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
