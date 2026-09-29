# TASK-040 — Supplier master and purchasing setup

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.purchasing` |
| Traces to | FR-301..FR-305, BR-036 |
| Depends on | TASK-017 |
| Size | M |
| Primary role | Core backend |

## Goal

Model who you buy from, with the tax and payment attributes purchasing and payables need.

## Scope

**In:** Supplier master with tax identifiers and PII encryption, addresses/contacts, payment terms, supplier item codes and pack sizes, supplier groups, price agreements, blocking.

**Out:** Supplier portal.

## Implementation notes

- Supplier item code mapping is essential for imports and receipts — a supplier's code must resolve to our variant.
- Payment terms drive payables ageing and due-date alerts.
- A blocked supplier cannot receive new POs but existing ones can be settled.

## Acceptance criteria

1. A goods receipt using supplier item codes resolves to the correct variants.
2. Payment terms produce correct due dates in the payables ageing report.
3. Supplier tax identifiers appear correctly on statutory purchase exports.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
