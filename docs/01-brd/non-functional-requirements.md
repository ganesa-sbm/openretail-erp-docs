# Non-functional requirements

| ID | Category | Requirement | Verification |
| --- | --- | --- | --- |
| NFR-001 | Performance | POS item lookup by barcode ≤ 150 ms p95 with 100k SKUs and 5M historical bill lines | JMH + seeded dataset benchmark in CI |
| NFR-002 | Performance | Bill finalisation (post + print spool) ≤ 1.5 s p95 on reference hardware | Load test with 10 concurrent counters |
| NFR-003 | Performance | Cold start of JavaFX client to login screen ≤ 6 s; login to POS ready ≤ 3 s | Startup timing test on reference hardware |
| NFR-004 | Performance | Trial balance for 12 months over 1M journal lines ≤ 10 s | Report benchmark |
| NFR-005 | Scalability | One store server supports 10 concurrent counters + 5 back-office users without degradation beyond NFR-002 | Gatling/k6 LAN load test |
| NFR-006 | Scalability | Cloud tenant supports 50 stores per tenant with nightly consolidated reporting inside a 30-minute window | Synthetic multi-store dataset |
| NFR-007 | Availability | Counter billing availability ≥ 99.9% measured at the counter, independent of server/cloud availability | Chaos test: kill server during billing |
| NFR-008 | Reliability | Zero committed-transaction loss on abrupt power failure at a counter | Power-cut simulation (kill -9 + fsync audit) |
| NFR-009 | Recoverability | RPO ≤ 24 h local / ≤ 1 h with cloud sync; RTO ≤ 2 h on replacement hardware | Quarterly restore drill, automated weekly |
| NFR-010 | Security | No plaintext secrets at rest; Argon2id for passwords; AES-256-GCM for encrypted columns | Security review + automated scan |
| NFR-011 | Security | All inter-node traffic TLS 1.2+; certificate pinning for cloud sync | Config test, TLS scanner |
| NFR-012 | Security | Audit log append-only and tamper-evident (hash chain per node) | Tamper test |
| NFR-013 | Usability | All POS operations reachable by keyboard; no operation requires more than 3 keystrokes from the billing screen | Usability walkthrough checklist |
| NFR-014 | Usability | UI readable at 1366×768; supports 125%/150% scaling; minimum 14 px base font | Manual matrix test |
| NFR-015 | Localisation | All user-facing strings externalised; English + one Indic language shipped; date/number/currency locale-aware | i18n lint (no hardcoded strings) |
| NFR-016 | Portability | Runs on Windows 10+, Ubuntu 20.04+, and macOS 12+ with a bundled JRE | Packaging CI matrix |
| NFR-017 | Maintainability | Modules communicate only via published APIs; no cyclic module dependencies | ArchUnit + JPMS enforcement |
| NFR-018 | Testability | Core domain logic has no Spring or JavaFX dependency and is unit-testable in isolation | ArchUnit rule |
| NFR-019 | Observability | Structured logs, per-node health metrics, sync lag, backup status exposed via local endpoint and dashboard | Ops acceptance test |
| NFR-020 | Compliance | Financial records retained 8 years; no hard delete; export in open formats (CSV, XML, PDF/A for statements) | Retention policy test |
| NFR-021 | Installability | Unattended install from a single artifact with no internet, ≤ 15 min including database | Clean-VM install test |
| NFR-022 | Upgradability | Schema migrations forward-only, idempotent, and safe to run on a node that was offline for months | Migration test from N-3 versions |
| NFR-023 | Data integrity | Stock and ledger balances derivable by replaying movements; nightly self-check reports any divergence | Reconciliation job assertion |
| NFR-024 | Licensing | Every dependency's licence is in the permitted set; build fails otherwise | Licence-check Gradle plugin in CI |
| NFR-025 | Resource use | Client memory ≤ 700 MB RSS steady state; server ≤ 2 GB for 10 counters | Soak test 8 h |
| NFR-026 | Accessibility | Keyboard focus visible, colour contrast ≥ 4.5:1, no colour-only status encoding | Contrast audit |
