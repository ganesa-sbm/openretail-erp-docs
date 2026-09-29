# TASK-073 — Plugin SPI, loader and lifecycle

| Field | Value |
| --- | --- |
| Phase | 2 |
| Module(s) | `erp.plugin.api, erp.core.kernel` |
| Traces to | FR-587..FR-592, ADR-005, BR-059 |
| Depends on | TASK-002 |
| Size | L |
| Primary role | Architect |

## Goal

Make verticals extensible without forking the core.

## Scope

**In:** `erp.plugin.api` module with the SPI (TS-04), JPMS child-layer loader, `ServiceLoader` discovery, `plugin.json` parsing, version compatibility checks, permission declaration, lifecycle (install/enable/disable/uninstall/upgrade), isolation and failure containment, signature verification.

**Out:** Specific plugins (TASK-076..078).

## Implementation notes

- Plugins load in child module layers so their dependencies cannot collide with core's (ADR-005).
- A plugin failure must be contained: a throwing or hanging plugin is disabled with an alert, and the core keeps billing.
- Unsigned plugins load only in developer mode; signature and licence are recorded in the SBOM.
- Compatibility is declared as a `coreApiVersion` range and enforced at load.

## Acceptance criteria

1. Load/unload cycles leave no leaked threads, class loaders or file handles (harness asserts).
2. A plugin declaring an incompatible core version is refused with a clear message.
3. A deliberately faulty plugin is isolated and disabled without stopping the POS.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
