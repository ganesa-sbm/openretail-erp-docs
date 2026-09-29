# TASK-005 — Persistence baseline: JPA, Flyway, dual-dialect test harness

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.core.persistence` |
| Traces to | NFR-019, ADR-008 |
| Depends on | TASK-003 |
| Size | M |
| Primary role | Core backend |

## Goal

Establish how every module persists data and how it is tested, once, so 80 later tasks do not each invent it.

## Scope

**In:** Datasource/transaction config, naming strategy, Flyway per-module locations, base repository abstractions, Testcontainers harness for PostgreSQL and MySQL, query-count assertions.

**Out:** Business schemas.

## Implementation notes

- Repositories expose domain types; JPA entities never leak out of `infrastructure`.
- Flyway locations are per module so modules can be added without editing a central list.
- The dual-dialect harness runs the same test class against both databases via a JUnit extension.
- A reusable assertion counts SQL statements per use case to catch N+1 regressions.

## Acceptance criteria

1. Same integration test passes on PostgreSQL and MySQL unchanged.
2. Migration from an empty database and from a snapshot both succeed in CI.
3. The N+1 assertion fails on a deliberately lazy-loaded loop.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
