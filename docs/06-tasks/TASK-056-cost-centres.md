# TASK-056 — Cost centres and dimensional reporting

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.accounting` |
| Traces to | FR-450..FR-453 |
| Depends on | TASK-049 |
| Size | S |
| Primary role | Accounting specialist |

## Goal

Let a multi-counter or multi-department shop see where money is actually made.

## Scope

**In:** Cost-centre master and hierarchy, dimension capture on documents, allocation rules for shared costs, dimensional P&L, scope enforcement by cost centre.

**Out:** Full project accounting.

## Implementation notes

- Dimensions are captured on the journal line, not just the voucher header, or dimensional reports will never balance.
- Allocation of shared costs is explicit and auditable, not an implicit report-time split.
- Dimensional access respects data scopes (TASK-010).

## Acceptance criteria

1. Dimensional P&L across all cost centres equals the total P&L.
2. Shared-cost allocation posts real entries with an audit trail.
3. A cost-centre-scoped user sees only their dimension's figures.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
