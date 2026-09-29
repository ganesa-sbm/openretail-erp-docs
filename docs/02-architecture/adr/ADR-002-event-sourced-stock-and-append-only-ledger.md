# ADR-002 — Movement-sourced stock and append-only ledger

Status: ACCEPTED · Date: 2026-09-29 · Drivers: BR-040, BR-047, NFR-023, CMP-05

## Context

A mutable `stock.on_hand` column is the classic source of both concurrency bugs and unexplainable discrepancies, and it is nearly impossible to reconcile after an offline period. Likewise, editable financial documents destroy auditability.

## Decision

- Stock on hand is **derived** from immutable `stock_movement` rows. `stock_snapshot` is a rebuildable projection, never the truth.
- Journal lines are append-only; vouchers are immutable after posting. Corrections are reversal vouchers.
- Documents (invoices, receipts, notes) are immutable after finalisation; pre-finalisation state (cart, draft) is local and never replicated.
- Balances everywhere are derived; caches are rebuildable and verified nightly.

This is "event-sourcing-lite": we persist domain facts as rows in relational tables rather than adopting an event-store framework.

## Consequences

**Positive**: perfect auditability; conflict-free replication of the financially sensitive data; any balance can be explained by listing its movements; rebuild recovers from projection bugs.

**Negative**: more rows (a busy store writes ~1–2M movement rows/year — well within PostgreSQL on an SSD); reports must use projections; "fixing a typo" requires a reversal, which needs UI affordances and user education.

**Rejected**: mutable balances with row locks (fails offline); full CQRS/event-store (operational complexity unjustified at this scale).
