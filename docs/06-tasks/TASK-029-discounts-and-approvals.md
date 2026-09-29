# TASK-029 — Discounts, overrides and approval workflow

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.pricing, erp.sales.pos` |
| Traces to | FR-179..FR-184, BR-024, BR-064 |
| Depends on | TASK-028 |
| Size | M |
| Primary role | Core backend |

## Goal

Control the biggest leakage point in retail: discretionary discounts and price overrides.

## Scope

**In:** Line and bill discounts, coupon codes, discount eligibility via rules, threshold-based approval routing, manager override at the counter (local and remote), override reason codes, exception reporting.

**Out:** Loyalty accrual (TASK-036).

## Implementation notes

- Approvals route through the `APPROVAL_ROUTING` decision point; thresholds are configuration, not code.
- An offline counter must still be able to obtain an override — supervisor authenticates locally against cached credentials; the event is audited and syncs later.
- Every override records who, why (reason code), and the amount given away; the exception report is a day-close staple.

## Acceptance criteria

1. A discount above the threshold cannot be applied without an authorised override; the attempt is audited either way.
2. Offline override works with a locally cached supervisor credential and appears at the server after sync.
3. The discount exception report reconciles to the discount ledger account for the period.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
