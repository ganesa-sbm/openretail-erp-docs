# ADR-008 — PostgreSQL as reference database, MySQL as a supported subset

Status: ACCEPTED · Date: 2026-09-29

## Context

The requirement names PostgreSQL/MySQL. Supporting both fully doubles the testing surface; supporting neither well is worse. Embedded options (H2, SQLite, Derby) were considered for the standalone mode to simplify installation.

## Decision

PostgreSQL 15+ is the reference and the default for every node role, including standalone (installed silently as a local service). MySQL 8 is supported through a compatibility subset with its own Flyway migration set and a conformance test suite. Embedded databases are used only for unit tests, never for production data.

## Rationale

- `jsonb` (plugin attributes, layout payloads, change-log payloads), partial/expression indexes, deferred constraints, `timestamptz` semantics and logical decoding all materially simplify the design.
- One engine in production everywhere means the offline counter and the cloud behave identically.
- H2/SQLite would diverge in numeric, locking and concurrency behaviour precisely where financial correctness matters.

## Consequences

**Positive**: a single, well-understood engine to tune, back up and support; features above are usable without abstraction gymnastics.

**Negative**: PostgreSQL must be installed on every counter (handled by the installer); MySQL support costs a parallel migration set and CI matrix, and forfeits partial indexes, deferred constraints and native `jsonb` (documented in data architecture §8). Any feature that cannot be expressed in the MySQL subset must either be PostgreSQL-only with graceful degradation or be redesigned.
