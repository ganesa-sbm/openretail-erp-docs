# TASK-038 — Receipt, invoice and document printing

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.reporting, erp.client.fx` |
| Traces to | FR-255..FR-259, BR-033, CMP-02 |
| Depends on | TASK-031 |
| Size | M |
| Primary role | UI engineer |

## Goal

Print what the customer and the law require, on the cheap thermal hardware shops own.

## Scope

**In:** Printer profiles per counter, ESC/POS and A4 rendering, template binding, print queue with retry, reprint marked DUPLICATE and audited, no-paper/offline-printer handling, print preview.

**Out:** Label printing (TASK-016) and the report renderer internals (TASK-061).

## Implementation notes

- Printing is asynchronous and must never block finalisation — the sale is recorded even if the printer is dead.
- Statutory content is validated at template publication, not discovered missing at the counter.
- Reprints are audited with the reprint count visible on the document.

## Acceptance criteria

1. Bill print starts within the documented budget after finalise on an 80 mm thermal printer.
2. Printer offline or out of paper leaves the sale recorded, queues the print, and prints on recovery without duplication.
3. A statutory content check rejects a template missing tax breakup or the invoice number.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
