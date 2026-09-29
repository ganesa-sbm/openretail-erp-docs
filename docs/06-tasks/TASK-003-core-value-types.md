# TASK-003 — Core kernel value types: Money, Quantity, ids, clock

| Field | Value |
| --- | --- |
| Phase | 1 |
| Module(s) | `erp.core.kernel` |
| Traces to | NFR-026, ADR-002 |
| Depends on | TASK-002 |
| Size | M |
| Primary role | Core backend |

## Goal

Provide the primitive correctness layer everything else depends on: money, quantity, identifiers, time.

## Scope

**In:** `Money`, `Quantity`, `TaxRate`, `Percentage`, `DocumentNumber`, `EntityId`/`NodeId` (UUIDv7), `Clock` abstraction, rounding policy, serialization.

**Out:** Domain aggregates.

## Implementation notes

- Money is `BigDecimal` scale 4 internally with an explicit `CurrencyCode`; arithmetic between different currencies throws.
- Rounding policy is explicit and centralised (half-up at presentation/posting scale 2 by default, configurable per currency).
- Quantity is `BigDecimal` scale 3 with a `UomId`; arithmetic across incompatible UOMs throws.
- UUIDv7 generated client-side so offline nodes never need a server round-trip for identity (time-ordered for index locality).
- All time goes through the injected `Clock`; `Instant.now()` is banned by an ArchUnit rule.

## Acceptance criteria

1. Property-based tests: addition associativity within scale, no precision loss across 10^6 random operations, rounding matches the documented policy.
2. Serialization round-trips through JSON and JPA without precision loss.
3. UUIDv7 values are monotonically increasing per node and unique across 10 simulated nodes over 10^6 ids.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
