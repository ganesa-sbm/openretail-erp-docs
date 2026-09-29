# TASK-007 — Tamper-evident audit log with hash chaining

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.security` |
| Traces to | BR-070, NFR-011, CMP-08 |
| Depends on | TASK-006 |
| Size | M |
| Primary role | Core backend |

## Goal

Make sensitive changes provable after the fact, including on a node an owner controls physically.

## Scope

**In:** `audit_event` table, append-only enforcement, per-node hash chain, writer API, verification job, admin viewer query support.

**Out:** Audit UI (TASK-075); log shipping (TASK-081).

## Implementation notes

- Each event stores actor, node, timestamp, entity, action, before/after (redacted for secrets) and the hash of the previous event on that node.
- Writing happens in the same transaction as the change; a failed audit write fails the business operation.
- Verification recomputes the chain and reports the first divergence.
- Chains are per node; sync transports them without re-hashing (ADR-001).

## Acceptance criteria

1. Every state-changing use case in the codebase emits an audit event (an architecture test enforces this on annotated services).
2. Manually altering a row in the audit table is detected by the verification job with the exact position.
3. Verification of 1,000,000 events completes within the documented budget.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
