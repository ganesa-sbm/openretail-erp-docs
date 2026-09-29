# TASK-030 — Counter sessions, cash drawer and day close

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.sales.pos` |
| Traces to | FR-201..FR-208, BR-025, BR-035 |
| Depends on | TASK-011 |
| Size | M |
| Primary role | Core backend |

## Goal

Make cash accountable per shift, which is what actually prevents disputes.

## Scope

**In:** Shift/session open with opening float, cash in/out with reasons, drawer kick, blind close counting, over/short calculation, X and Z reports, session-to-ledger posting.

**Out:** Bank deposit workflow (TASK-054).

## Implementation notes

- Close is blind by default — the counter enters counted cash before seeing expected (a real control, and configurable).
- A session cannot be closed with unsettled held bills; they must be recalled or voided with a reason.
- Session posting creates the cash ledger entries; over/short goes to a dedicated account.

## Acceptance criteria

1. Z report totals equal the sum of the session's invoices by tender type, verified against the ledger.
2. Over/short is computed and posted correctly and appears in the daily exception report.
3. Reopening a closed session is impossible; corrections are new entries.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
