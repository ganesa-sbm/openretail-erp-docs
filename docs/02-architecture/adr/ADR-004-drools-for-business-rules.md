# ADR-004 — Drools for externalised business rules

Status: ACCEPTED · Date: 2026-09-29 · Drivers: BR-051, RSK-3

## Context

Tax determination, discount eligibility, approval thresholds, loyalty accrual and reorder suggestions change per tenant, per season and per regulation, and must be changeable without a release.

Options: hard-coded strategies (fast, inflexible), a small custom DSL/expression engine (MVEL/SpEL tables), or Drools.

## Decision

Use **Drools** for declarative business rules, with:

- Rules stored in the database as `rule_package` → `rule_version` (DRL text + metadata + author + approver), replicated to nodes by sync.
- A compiled `KieContainer` cache per package version, rebuilt on activation, with warm-up before switchover.
- **Mandatory simulation** before activation: the candidate version runs against a recorded fixture set (last N real transactions, anonymised) and shows a diff of outcomes; activation requires a second approver (four-eyes) and is audited.
- Rule execution is **pure**: rules receive facts and produce decisions/outputs; they never perform I/O or mutate entities directly.
- A hard timeout and rule-count limit per evaluation guards POS latency; on timeout the last-known-good version's result or a safe default is used and an alert is raised.

## Consequences

**Positive**: business logic that genuinely varies is data; versioned, auditable, testable in isolation; rule authors do not need a build pipeline.

**Negative**: Drools is a heavyweight dependency (memory, startup); DRL is a skill not every implementer has (see skills documents); a badly written rule can be slow.

**Guardrails**: only the identified decision points are rule-driven — posting rules, validation invariants and anything affecting ledger integrity stay in code; every rule package declares its decision-point contract (input facts, output type) and is unit-tested.
