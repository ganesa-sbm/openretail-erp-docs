# TASK-002 — JPMS module graph and architecture enforcement

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `all` |
| Traces to | NFR-018, ADR-005 |
| Depends on | TASK-001 |
| Size | M |
| Primary role | Architect |

## Goal

Define the module graph from the architecture document as real `module-info.java` files and make the rules executable so they cannot silently rot.

## Scope

**In:** Empty-but-real modules for every planned module, `module-info.java` with explicit exports, ArchUnit rules, cycle detection, layer rules.

**Out:** Module implementations.

## Implementation notes

- Domain packages must not import Spring, Hibernate, JavaFX or Vaadin — ArchUnit rule, not a convention.
- Only `api` packages are exported; infrastructure stays encapsulated.
- No cycles between modules; dependency direction follows the hexagonal layering.
- `erp.plugin.api` must not depend on any internal module.

## Acceptance criteria

1. `./gradlew checkArchitecture` passes and fails on a deliberately introduced violation (test proves it).
2. A module diagram is generated from the actual module graph in CI.
3. Adding a forbidden import breaks the build within the module's own test task.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
