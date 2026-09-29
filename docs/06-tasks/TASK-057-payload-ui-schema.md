# TASK-057 — Payload-driven UI: schema, validator and runtime contract

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.platform.ui` |
| Traces to | FR-501..FR-506, ADR-003 |
| Depends on | TASK-003 |
| Size | L |
| Primary role | UI engineer |

## Goal

Make screens configurable per shop without forking the client.

## Scope

**In:** Layout JSON schema (TS-03), component catalogue metadata, binding resolution, restricted expression evaluator, declarative validators, action registry, versioning and publication validation.

**Out:** The FX renderer (TASK-058) and the designer (TASK-059).

## Implementation notes

- The expression language is restricted by construction: no I/O, no loops, bounded evaluation time — a bad expression must not hang a counter.
- Publication validation resolves every binding and action against the live catalogue and data contexts; unresolved references block publication.
- Schema is versioned with a documented migration path for stored layouts.

## Acceptance criteria

1. An invalid layout (unknown component, unresolved binding, cyclic visibility) is rejected at publication with a precise message.
2. Expression evaluation is sandboxed and time-bounded (fuzz test with pathological expressions).
3. A layout stored under schema version N-1 loads after an upgrade via migration.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
