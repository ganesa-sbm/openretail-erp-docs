# TASK-041 — Purchase orders

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.purchasing` |
| Traces to | FR-306..FR-312, BR-037 |
| Depends on | TASK-040 |
| Size | M |
| Primary role | Core backend |

## Goal

Order goods with a document that receipts and invoices can be matched against.

## Scope

**In:** PO creation (manual, from suggestions, from low stock), approval by value threshold, PO printing/emailing, amendment with version history, partial fulfilment tracking, cancellation and closure.

**Out:** Supplier acknowledgement workflows.

## Implementation notes

- POs are commitments, not stock — no inventory effect until receipt.
- Amendments create versions; the received/matched quantities always reference the version in force.
- Approval thresholds route through the approval workflow (TASK-029).

## Acceptance criteria

1. A PO above the threshold cannot be sent without approval; the approval is audited.
2. Partial receipts leave the PO open with correct pending quantities; over-receipt beyond tolerance is blocked.
3. Closing a PO with pending quantity requires a reason and is reported.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
