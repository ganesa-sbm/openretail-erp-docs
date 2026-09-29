# TASK-017 — Tax classes and tax configuration

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.inventory, erp.accounting` |
| Traces to | FR-117, BR-040..BR-043 |
| Depends on | TASK-014 |
| Size | M |
| Primary role | Accounting specialist |

## Goal

Configure tax once, correctly, so every document and statutory report agrees.

## Scope

**In:** Tax class master, rate components with validity dates, HSN/SAC codes, place-of-supply handling, inclusive/exclusive pricing, exemptions, per-customer overrides.

**Out:** Tax computation on documents (TASK-027 decision point, TASK-050 posting).

## Implementation notes

- Rates are date-versioned; historical documents must recompute with the rate in force on their document date, never the current rate.
- Inclusive pricing is stored with the derived base and tax split so reports never re-derive inconsistently.
- A tax class change never rewrites posted documents.

## Acceptance criteria

1. A rate change effective mid-month leaves last month's invoices and reports unchanged and applies to new ones.
2. Inclusive and exclusive configurations produce the same ledger totals for equivalent prices (round-off within the documented tolerance).
3. Statutory export groups correctly by HSN and rate (verified against the tax test corpus).

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
