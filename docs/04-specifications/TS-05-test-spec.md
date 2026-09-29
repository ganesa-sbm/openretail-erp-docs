# TS-05 — Test and quality specification

## 1. Test pyramid

| Level | Tooling | Scope | Gate |
| --- | --- | --- | --- |
| Unit | JUnit 5, AssertJ, Mockito | Domain invariants, calculations, rule fixtures | ≥ 85% line coverage in `erp.core.*`, `erp.accounting`, `erp.inventory` |
| Architecture | ArchUnit, JPMS | Layering, no cycles, domain free of Spring/JavaFX | Must pass |
| Integration | Testcontainers (PostgreSQL + MySQL) | Repositories, migrations, transactions, posting rules | Must pass on both engines |
| Contract | OpenAPI diff, Spring Cloud Contract or REST-assured suites | Client ↔ server compatibility | Breaking change fails build |
| UI | TestFX (JavaFX), Vaadin TestBench alternative (Playwright against the web UI) | Key screens, POS keyboard flows | Smoke suite per PR, full suite nightly |
| Sync simulation | Custom deterministic simulator | Convergence, conflicts, partitions | Invariants must hold over 10k randomized runs |
| Performance | JMH (micro), k6/Gatling (system) | NFR-001..006 budgets | Regression > 10% fails build |
| Security | SpotBugs+FindSecBugs, OWASP dependency-check, secret scan, ZAP baseline on web layer | Vulnerabilities | No high/critical |
| Acceptance | Cucumber-style scenarios from FS acceptance criteria | Business flows end-to-end | Release gate |

## 2. Domain invariant tests (must-have list)

1. Voucher balance for every generated posting across a randomized transaction corpus.
2. Stock on hand from movement replay equals `stock_snapshot` after any operation sequence.
3. Returned quantity never exceeds sold quantity across arbitrary return sequences.
4. Invoice totals = Σ lines − discounts + taxes + round-off, at 4-decimal precision.
5. FEFO selection always picks the earliest non-expired batch with stock.
6. Numbering gapless per series under concurrent finalisation (100 threads).
7. Locked-period postings always rejected, including through import and plugin paths.
8. Every state-changing use case emits exactly one audit event with a valid hash chain link.

## 3. Sync simulation harness

A deterministic simulator instantiates N in-process nodes with real persistence (Testcontainers) or an in-memory adapter, drives scripted and randomized operation mixes, injects partitions, delays, duplicate deliveries, out-of-order batches, node restarts and backup-restore regressions, then asserts:

- convergence (all nodes agree on all append-only facts),
- no duplicate document numbers,
- balanced ledger on every node,
- conflict records exist for every divergent master edit and none are silently dropped,
- catch-up performance for a 30-day offline node within budget.

Seeds are recorded so any failure is reproducible; failing seeds become permanent regression cases.

## 4. Performance test scenarios

| Scenario | Setup | Assertion |
| --- | --- | --- |
| POS lookup | 100k SKUs, 5M invoice lines | p95 ≤ 150 ms (NFR-001) |
| Bill finalisation | 10 counters × 1 bill/6 s for 30 min | p95 ≤ 1.5 s, no errors (NFR-002/005) |
| Trial balance | 1M journal lines | ≤ 10 s (NFR-004) |
| Dashboard Today | 1 year of data | ≤ 1.5 s cold |
| Sync catch-up | 30 days, 10k documents | ≤ 10 min, memory ≤ budget |
| Client startup | Reference hardware | ≤ 6 s to login (NFR-003) |
| Soak | 8 h continuous billing | No memory growth beyond 10%, no handle leaks (NFR-025) |

## 5. Resilience / chaos tests

Power-cut simulation (SIGKILL) at 20 instrumented points in the finalisation path — invariant: either a complete invoice or none. Server killed mid-sync-batch. Disk full during backup. Printer offline/paper-out. Scanner disconnect. Clock jump ±1 day. Corrupted plugin JAR. Restored-from-old-backup node re-joining.

## 6. Data fixtures

Three reference datasets, generated deterministically and used by all performance and acceptance tests: **Small** (departmental store, 5k SKUs, 3 months), **Medium** (textile, 40k variants, 1 year, 2 counters), **Large** (chain, 100k SKUs, 3 years, 5 stores × 4 counters). Generators live in the repo; datasets are reproducible from a seed rather than checked in.

## 7. CI pipeline

PR: compile → unit → architecture → integration (PostgreSQL) → contract → UI smoke → licence check → SAST/dependency scan → performance micro-benchmarks. ~15 min budget.
Nightly: full UI suite, MySQL integration, sync simulation (10k runs), performance system tests, soak (weekly), migration tests from N-3, ZAP baseline, restore drill.
Release: all of the above plus packaging (jpackage for Windows/Linux/macOS), clean-VM install test, upgrade test, SBOM generation and signature.

## 8. Manual test charters (exploratory)

Per release: peripheral matrix (3 thermal printers, 2 scanners, 2 scales, 1 label printer), low-spec hardware run, 150%-scaling display, localisation sweep (all strings in the second language), accessibility keyboard-only pass, and a "shop day" end-to-end rehearsal (open → 50 bills → returns → purchase receipt → day close → backup → restore).

## 9. Definition of done (release)

All gates green; zero known critical/high defects; performance budgets met; restore drill passed; release notes with migration and rollback notes; SBOM published; upgrade tested from the previous two releases.
