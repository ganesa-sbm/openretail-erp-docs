# TASK-001 — Repository, Gradle multi-module skeleton and build conventions

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `build` |
| Traces to | NFR-023, NFR-018 |
| Depends on | — |
| Size | M |
| Primary role | DevOps |

## Goal

Create the buildable skeleton every other task lands in: Gradle multi-project with Java 21 toolchain, shared convention plugins, static analysis, and a reproducible build.

## Scope

**In:** Gradle settings and convention plugins, version catalog, Spotless/Checkstyle, JaCoCo, SBOM and licence-check tasks, CI workflow running build+test on Linux/Windows.

**Out:** Application code, CI deployment jobs, installers (TASK-013).

## Implementation notes

- One convention plugin for Java/JPMS subprojects; no per-module duplicated config.
- Version catalog (`libs.versions.toml`) is the only place dependency versions appear.
- Licence check fails the build on any non-permissive/copyleft-incompatible dependency (NFR-024).
- Build must work fully offline from a warmed cache — a shop developer may have no internet.

## Acceptance criteria

1. `./gradlew build` succeeds from a clean clone on Linux and Windows.
2. `./gradlew build --offline` succeeds after one warm run.
3. SBOM (CycloneDX) and licence report are produced as build artefacts.
4. CI runs build, test, static analysis and licence check on every PR.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
