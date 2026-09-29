# TASK-010 — Authorization enforcement and data scoping

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.security` |
| Traces to | BR-061..063 |
| Depends on | TASK-009 |
| Size | M |
| Primary role | Core backend |

## Goal

Make permissions actually enforced at every entry point, not just displayed in the UI.

## Scope

**In:** `@RequiresPermission` aspect on application services, scope predicates injected into repositories, API filter, UI capability queries, approval-required interception.

**Out:** Approval workflow itself (TASK-029).

## Implementation notes

- Enforcement is server-side at the application-service boundary; UI hiding is convenience only.
- Scope predicates are applied in the repository layer so no query can accidentally cross a store boundary.
- Denials are audited with the attempted action.

## Acceptance criteria

1. An architecture test fails if any public application-service method lacks a permission annotation.
2. A store-scoped user cannot read another store's documents through any API endpoint (contract test sweeps all endpoints).
3. Denied operations return RFC 7807 403 with a stable error code.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
