# TASK-011 — Node identity, registration and pairing

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.core.kernel, erp.sync` |
| Traces to | BR-028, DEP-04 |
| Depends on | TASK-009 |
| Size | M |
| Primary role | Core backend |

## Goal

Give every install a stable identity and a safe way to join a store, which numbering, sync and licensing all depend on.

## Scope

**In:** Node record (id, role, store, prefix), first-run wizard, pairing with the store server via a short-lived code, certificate provisioning, node deactivation.

**Out:** Full sync protocol (TASK-067).

## Implementation notes

- Node id is UUIDv7 generated locally; the counter prefix used in document numbering is assigned by the server at pairing and is unique per store (ADR-006).
- Pairing exchanges a one-time code for a client certificate/token; codes expire in minutes and are single-use.
- A deactivated node is refused at sync and told to stop billing after a grace period, without losing local data.

## Acceptance criteria

1. Two counters cannot be assigned the same prefix even if paired simultaneously (concurrency test).
2. Re-pairing an existing node preserves its id and local data.
3. Standalone mode works with a self-assigned node and no server.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
