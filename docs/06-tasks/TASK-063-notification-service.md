# TASK-063 — Notification service: templates, channels and delivery

| Field | Value |
| --- | --- |
| Phase | 2 |
| Module(s) | `erp.notification` |
| Traces to | FR-533..FR-539, BR-053, BR-054 |
| Depends on | TASK-004 |
| Size | L |
| Primary role | Core backend |

## Goal

Send the messages that keep customers and owners informed, reliably and without spamming.

## Scope

**In:** Template master with variables and locales, SMS and email channels behind `NotificationChannel`, provider adapters, outbox-driven dispatch, retry with backoff, deduplication, delivery status, consent and quiet hours, cost/usage reporting.

**Out:** WhatsApp/push (plugins, later phases).

## Implementation notes

- Sends originate from the transactional outbox so a message is never sent for a transaction that rolled back.
- Offline nodes queue notifications locally and dispatch on reconnect; the queue is bounded and reports its depth.
- Consent and quiet hours are enforced centrally — a shop cannot accidentally SMS customers at 2 a.m.
- Deduplication is keyed by (template, recipient, source document) within a window.

## Acceptance criteria

1. A rolled-back sale sends nothing; a committed sale sends exactly one receipt message.
2. Provider outage retries with backoff and delivers after recovery without duplicates.
3. Quiet hours defer rather than drop, and consent withdrawal stops sends immediately.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
