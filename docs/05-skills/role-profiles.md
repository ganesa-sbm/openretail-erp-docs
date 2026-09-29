# Role profiles

Each profile: purpose, owns, required skills (with target level), typical deliverables, and evidence of competence.

## Solution architect

**Purpose**: keep the system coherent across modes, modules and phases.
**Owns**: architecture documents, ADRs, module boundaries, sync design, security architecture, performance budgets.
**Skills**: SKL-001/4, SKL-002/4, SKL-003/4, SKL-005/4, SKL-007/4, SKL-018/4, SKL-019/4, SKL-020/4, SKL-021/4, SKL-025/4, SKL-029/4.
**Deliverables**: ADRs, reference implementations of hard paths (sync, posting), design reviews, performance budget enforcement in CI.
**Evidence**: has designed a system with offline clients and conflict resolution; can explain why an append-only ledger removes a class of sync bugs; can read an EXPLAIN plan and a JFR profile.

## Core backend engineer

**Purpose**: build domain, application services and persistence.
**Owns**: `erp.core.*`, `erp.inventory`, `erp.purchasing`, `erp.sales.pos`, `erp.accounting`, REST API.
**Skills**: SKL-001/4, SKL-003/4, SKL-004/4, SKL-006/4, SKL-007/4, SKL-008/3, SKL-015/3, SKL-016/3, SKL-024/3.
**Deliverables**: aggregates with invariant tests, use-case services, repositories, migrations, API endpoints with contract tests.
**Evidence**: can implement a use case that posts a balanced voucher and emits an audit event, with tests, in a day; knows why `BigDecimal` and not `double`.

## UI engineer (JavaFX)

**Purpose**: build the POS and back-office client and the payload-driven renderer.
**Owns**: `erp.client.fx`, `erp.platform.ui` FX renderer, peripheral adapters.
**Skills**: SKL-009/4, SKL-013/4, SKL-001/3, SKL-022/3, SKL-023/3, SKL-024/3, SKL-025/3, SKL-031/3.
**Deliverables**: POS screen meeting latency budgets, renderer components, TestFX suites, printer/scanner/scale adapters.
**Evidence**: can keep the FX application thread free under load; has shipped keyboard-driven data entry; can debug a scene-graph performance problem.

## Web/mobile UI engineer

**Purpose**: Vaadin back-office (Ph.2) and Compose apps (Ph.3).
**Skills**: SKL-010/3 or SKL-011/3, SKL-013/3, SKL-001/3, SKL-024/3, SKL-031/3.

## Domain specialist — accounting & tax

**Purpose**: guarantee the books are right and statutory outputs are accepted.
**Owns**: chart templates, posting rules, statements, tax configuration, closing procedures, acceptance criteria in FS-04.
**Skills**: SKL-015/4, SKL-017/4, SKL-016/3, SKL-007/3, SKL-004/3, SKL-030/3, SKL-029/3.
**Deliverables**: posting-rule matrix, statement definitions, tax test corpus, reviewer sign-off on any ledger-affecting change.
**Evidence**: can hand-derive the journal entries for a split-tender sale with returns and round-off; has closed a financial year in practice.

## Rules engineer

**Purpose**: own the Drools layer and rule governance.
**Skills**: SKL-014/4, SKL-004/3, SKL-015/3, SKL-016/3, SKL-024/2, SKL-025/2.
**Deliverables**: decision-point contracts, rule templates for non-programmers, simulation harness and fixtures, rule performance benchmarks.
**Evidence**: can convert a discount policy into rules with a passing simulation diff and explain the activation/rollback path.

## QA engineer

**Purpose**: make correctness measurable.
**Owns**: test strategy (TS-05), sync simulator, performance suite, chaos scenarios, release gates.
**Skills**: SKL-024/4, SKL-025/3, SKL-007/3, SKL-018/3, SKL-019/3, SKL-009/3, SKL-015/3.
**Evidence**: has built a deterministic simulation test; can reproduce and shrink a failing randomized seed.

## DevOps / release engineer

**Purpose**: build, package, ship, back up, monitor.
**Owns**: Gradle build, CI pipelines, jpackage installers, blueprint/environment, backup tooling, monitoring, SBOM and licence checks.
**Skills**: SKL-026/4, SKL-027/4, SKL-028/4, SKL-007/3, SKL-020/3, SKL-032/3.
**Evidence**: can produce a signed offline installer bundling a JRE and PostgreSQL and prove a restore works on a clean VM.

## Support engineer

**Purpose**: keep live shops running.
**Skills**: SKL-034/4, SKL-027/3, SKL-028/3, SKL-023/3, SKL-007/2, SKL-019/2.
**Deliverables**: triage runbooks, diagnostic bundle analysis, known-issue base, escalation quality.

## Implementation consultant

**Purpose**: take a shop from paper/legacy software to live.
**Skills**: SKL-033/4, SKL-030/4, SKL-015/4, SKL-016/4, SKL-017/4, SKL-013/3, SKL-022/3, SKL-014/3, SKL-007/2.
**Deliverables**: data migration (items, parties, opening stock, opening balances), chart and tax configuration, layout/report tailoring, staff training, go-live checklist and hypercare.
**Evidence**: can migrate 10k items and opening balances with a reconciliation that ties out, and train a cashier in an hour.

## Product owner

**Purpose**: decide what gets built and in what order; own the BRD.
**Skills**: SKL-030/4, SKL-029/4, SKL-015/2, SKL-016/2, SKL-013/3.
**Deliverables**: prioritised backlog referencing BR/FR ids, release scope, acceptance sign-off.
