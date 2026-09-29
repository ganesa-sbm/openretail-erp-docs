# TASK-012 — Observability baseline: structured logging, metrics, diagnostics bundle

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.core.kernel` |
| Traces to | NFR-021 |
| Depends on | TASK-001 |
| Size | M |
| Primary role | DevOps |

## Goal

Make production diagnosable on machines you cannot SSH into.

## Scope

**In:** Structured JSON logging with correlation ids, rotation and size caps, metrics (Micrometer) for the documented KPIs, health endpoints, and a one-click diagnostic bundle.

**Out:** Cloud monitoring (TASK-089).

## Implementation notes

- Logs never contain PII or secrets; a redaction filter is tested against known field names.
- Log volume is bounded (rotation + retention) — a counter's disk must not fill from logs.
- The diagnostic bundle includes version, node id/role, last sync token, outbox depth, clock offset, recent logs, health metrics and DB size, zipped and optionally encrypted.

## Acceptance criteria

1. Bundle generation from the UI takes under 30 s on the Large fixture and contains every documented item.
2. A correlation id ties a POS action to its server-side handling in the logs.
3. Redaction test passes for card/PII/secret fields.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
