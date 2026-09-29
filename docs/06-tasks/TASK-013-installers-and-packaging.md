# TASK-013 — Installer and packaging: jlink/jpackage with embedded runtime and database

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `packaging` |
| Traces to | NFR-022, NFR-025, DEP-03 |
| Depends on | TASK-001 |
| Size | L |
| Primary role | DevOps |

## Goal

Let a shopkeeper install the product from a USB stick with no internet and no Java knowledge.

## Scope

**In:** jlink runtime image, jpackage installers (MSI, DEB/RPM, DMG), embedded/bundled PostgreSQL provisioning, first-run wizard hand-off, service/autostart registration, uninstall, silent install for rollouts.

**Out:** Upgrade orchestration (TASK-085); cloud deployment.

## Implementation notes

- The installer must be fully offline: JRE, application modules, database and sample data all bundled.
- Windows is the primary target (most counters); Linux and macOS installers must exist and be CI-built.
- Installers are signed where signing material exists; the checksum is published either way.
- First run must reach a usable POS screen without the user editing any configuration file.

## Acceptance criteria

1. Clean-VM install on Windows 11, Ubuntu LTS and macOS produces a working standalone node with a first invoice, offline.
2. Silent install with a response file works for multi-counter rollouts.
3. Uninstall leaves data and backups intact by default and says so.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
