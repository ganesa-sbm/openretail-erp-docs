# TASK-089 — Cloud tier: sync, encrypted backup, fleet monitoring (Phase 5)

| Field | Value |
| --- | --- |
| Phase | 5 |
| Module(s) | `cloud` |
| Traces to | BR-077, FR-639..FR-645, NFR-007 |
| Depends on | TASK-079 |
| Size | XL |
| Primary role | DevOps |

## Goal

Add the optional internet tier for owners with several shops, without making anything depend on it.

## Scope

**In:** Cloud sync endpoint and tenant isolation, encrypted off-site backup, multi-store consolidated reporting, fleet health dashboard, remote diagnostic bundle collection, subscription/entitlement handling, infrastructure as code, cloud deployment pipeline.

**Out:** Full multi-tenant SaaS hosting of the POS itself.

## Implementation notes

- Nothing on the shop floor may depend on the cloud: losing it must degrade only reporting and off-site backup (ADR-001).
- Backups are encrypted client-side; the cloud operator must not be able to read shop data.
- Tenant isolation is enforced at the data layer and verified by contract tests, not only by application code.

## Acceptance criteria

1. Disabling the cloud tier leaves all store operations, including sync between counters and the store server, fully functional.
2. An off-site backup restores to a clean environment and passes audit-chain verification.
3. Cross-tenant access attempts fail in the contract test sweep.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
