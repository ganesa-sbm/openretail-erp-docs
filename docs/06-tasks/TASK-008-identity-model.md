# TASK-008 — Identity model: users, roles, permissions, data scopes

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.security` |
| Traces to | BR-060..063, FR-501 |
| Depends on | TASK-006 |
| Size | M |
| Primary role | Core backend |

## Goal

Model who can do what, where — the basis for every later permission check.

## Scope

**In:** User, role, permission, role assignment, store/cost-centre data scopes, seeded default roles (Admin, Manager, Cashier, Storekeeper, Accountant, Auditor), permission registry.

**Out:** Authentication (TASK-009); enforcement (TASK-010).

## Implementation notes

- Permissions are declared as constants in the owning module and registered at startup so the registry cannot drift from the code.
- Data scopes are first-class: a permission grant is always (permission, scope).
- Auditor is read-only by construction — no write permission is grantable to it.
- Default roles are seeded but editable; seeding is idempotent across upgrades.

## Acceptance criteria

1. Seeding on a fresh install produces the documented role/permission matrix exactly (golden-file test).
2. A permission used in code but not registered fails a startup self-check in tests.
3. Scope filtering is unit-tested for store-restricted and cost-centre-restricted users.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
