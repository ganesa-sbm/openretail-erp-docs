# TASK-004 — Domain event bus and transactional outbox

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.core.kernel` |
| Traces to | ADR-002, FR-501 |
| Depends on | TASK-003 |
| Size | M |
| Primary role | Core backend |

## Goal

Let modules react to each other without direct dependencies, with delivery guarantees that survive power loss.

## Scope

**In:** In-process synchronous bus for same-transaction handlers, transactional outbox table, dispatcher with retry/backoff, idempotent consumers, dead-letter handling.

**Out:** External message brokers; webhooks (TASK-071).

## Implementation notes

- Events are written to the outbox in the same transaction as the state change — no publish-before-commit.
- Dispatcher is at-least-once; every consumer must be idempotent by event id (helper provided and tested).
- Backoff is exponential with jitter and a poison-message threshold that quarantines rather than blocks the queue.
- Events are immutable records with a schema version field.

## Acceptance criteria

1. Killing the process between commit and dispatch still delivers the event after restart (test kills the JVM).
2. Duplicate delivery produces no duplicate side effects in the reference consumer.
3. A poison event is quarantined within N attempts and does not stall later events.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
