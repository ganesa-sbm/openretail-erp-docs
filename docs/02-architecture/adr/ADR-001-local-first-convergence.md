# ADR-001 — Local-first data with asynchronous convergence

Status: ACCEPTED · Date: 2026-09-29 · Drivers: BR-027, NFR-007, RSK-1

## Context

Shops lose power and network regularly. A central-database design (all counters talking to one PostgreSQL) stops billing whenever the server, switch or cable fails. A pure cloud design is worse. Alternatives considered:

1. Central DB with thin clients (simplest, but violates BR-027).
2. Central DB + local "offline cache" that queues writes (partial; reads of stock/pricing still fail, and the queue becomes an ad-hoc sync layer without discipline).
3. Full local stack per counter with asynchronous replication (most work, strongest availability).
4. Multi-master database replication (BDR/Galera) (operationally heavy for a shop PC, licence/expertise cost, conflicts still surface at the application level).

## Decision

Each COUNTER node runs a complete local stack (application + PostgreSQL) and writes locally first. A store server is the convergence point and the reporting source. Replication is application-level (see sync architecture), not database-level. THIN_CLIENT remains available for back-office machines where availability is not critical.

## Consequences

**Positive**: billing survives any server/network failure; read performance is local; the same code runs standalone, which makes the cheapest deployment the default development target.

**Negative**: a change-log, conflict policy and reconciliation job must be built and tested seriously; more disk per counter; upgrades must handle long-offline nodes; reports that need cross-counter data require the server or a pull.

**Mitigations**: documents are immutable and append-only so conflicts are confined to masters (ADR-002); masters are server-owned; deterministic simulation tests are mandatory for the sync module.
