# TASK-043 — Purchase invoices and three-way matching

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.purchasing, erp.accounting` |
| Traces to | FR-320..FR-326, BR-039 |
| Depends on | TASK-042 |
| Size | L |
| Primary role | Accounting specialist |

## Goal

Pay only what was ordered and received, at the agreed price.

## Scope

**In:** Purchase invoice entry, matching against PO and GRN, tolerance configuration, mismatch resolution workflow, GRNI clearing, tax capture, posting to payables.

**Out:** OCR invoice capture.

## Implementation notes

- Three-way match compares quantity and price with configurable tolerances; out-of-tolerance blocks posting until resolved or approved.
- Posting clears GRNI and creates the payable — these must net to zero for fully invoiced receipts.
- Purchase tax is captured for statutory input-credit reporting with the supplier's identifiers.

## Acceptance criteria

1. A matched invoice posts and leaves GRNI at zero for the receipt.
2. A price mismatch beyond tolerance blocks posting and appears in the mismatch queue with the three documents side by side.
3. Input-tax report totals equal the sum of posted purchase invoice tax for the period.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
