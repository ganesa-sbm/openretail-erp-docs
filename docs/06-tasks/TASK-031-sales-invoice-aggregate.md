# TASK-031 — Sales invoice aggregate and finalisation

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.sales.pos` |
| Traces to | FR-209..FR-216, BR-026, ADR-002 |
| Depends on | TASK-026 |
| Size | L |
| Primary role | Core backend |

## Goal

Get the central transaction of the product exactly right: lines, taxes, tenders, totals, immutability.

## Scope

**In:** `SalesInvoice` aggregate, line lifecycle, tax computation, round-off, tender balance, finalisation invariants, void before finalise, domain events.

**Out:** Persistence of payments to gateways (TASK-033).

## Implementation notes

- Finalisation is the only transition that makes a document real: it asserts draft status, non-empty lines, tender total equals payable, then emits `SaleCompleted`.
- A finalised invoice is immutable forever; changes are returns or credit notes.
- Round-off is a dedicated line/account, never a silent adjustment to a tax or revenue figure.
- Totals are computed once in the domain and reused by print, posting and reports — no recomputation drift.

## Acceptance criteria

1. Unbalanced tender, empty lines, or a finalised invoice all reject finalisation with distinct errors (unit tests on invariants).
2. Invoice totals, tax breakup and the resulting journal entry agree exactly for the tax test corpus.
3. Attempting to modify a finalised invoice through any path fails, including direct repository use (architecture test).

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
