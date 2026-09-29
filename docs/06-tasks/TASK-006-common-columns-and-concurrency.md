# TASK-006 — Common columns, optimistic concurrency and soft delete

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.core.persistence` |
| Traces to | NFR-026, ADR-001 |
| Depends on | TASK-005 |
| Size | S |
| Primary role | Core backend |

## Goal

Give every business table the columns sync, audit and concurrency depend on, applied automatically rather than by memory.

## Scope

**In:** `id`, `node_id`, `version`, `created_at/by`, `updated_at/by`, `deleted_at` handling; JPA listeners; a migration fragment/macro; validation test that scans the schema.

**Out:** Financial tables' immutability rules (TASK-007, TASK-050).

## Implementation notes

- Soft delete is only permitted on master data; financial and audit tables reject it at the schema level.
- `version` drives optimistic locking and the API's ETag/If-Match handling (TASK-070).
- `node_id` records the originating node for every row — required by sync and by forensics.

## Acceptance criteria

1. A schema test enumerates all business tables and fails if any lacks the common columns.
2. Concurrent update of the same row produces an optimistic-lock error, surfaced as a 409 by the API.
3. Attempting a soft delete on a financial table fails.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
