# TASK-071 — Webhooks and outbound integration events

| Field | Value |
| --- | --- |
| Phase | 5 |
| Module(s) | `erp.server` |
| Traces to | FR-579..FR-581 |
| Depends on | TASK-069 |
| Size | S |
| Primary role | Core backend |

## Goal

Let shops connect the ERP to other tools they already use.

## Scope

**In:** Webhook subscriptions per event type, HMAC signing, retry with backoff and dead-letter, delivery log, replay, payload versioning.

**Out:** Inbound third-party APIs.

## Implementation notes

- Deliveries come from the outbox with at-least-once semantics; payloads carry an event id so receivers can deduplicate.
- Signing secrets are stored encrypted and rotatable without downtime.
- A failing endpoint must never back up the internal outbox — isolate per subscription.

## Acceptance criteria

1. A subscribed endpoint receives correctly signed events; signature verification is documented and tested.
2. A permanently failing subscription dead-letters and alerts without affecting others.
3. Replay from the delivery log re-sends selected events.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
