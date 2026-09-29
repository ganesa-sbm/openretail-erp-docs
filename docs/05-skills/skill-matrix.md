# Skill matrix

## 1. Skill inventory

| ID | Skill | Area |
| --- | --- | --- |
| SKL-001 | Java 21 (records, sealed types, pattern matching, virtual threads) | Core |
| SKL-002 | JPMS modules and multi-module Gradle builds | Core |
| SKL-003 | Domain-driven design: aggregates, invariants, events | Core |
| SKL-004 | Spring Boot (DI, transactions, scheduling, configuration) | Backend |
| SKL-005 | Spring Security (authn, authz, JWT, method security) | Backend/Security |
| SKL-006 | Hibernate/JPA: mapping, fetching, locking, performance | Persistence |
| SKL-007 | SQL and PostgreSQL internals (indexes, EXPLAIN, jsonb, partitioning) | Persistence |
| SKL-008 | Flyway migrations and zero-downtime schema evolution | Persistence |
| SKL-009 | JavaFX (scene graph, properties/bindings, FXML, threading, TestFX) | UI |
| SKL-010 | Vaadin Flow | UI |
| SKL-011 | Compose Multiplatform / Kotlin | UI (Ph.3) |
| SKL-012 | Lanterna TUI | UI (Ph.4) |
| SKL-013 | UX for high-speed data entry / retail POS | UX |
| SKL-014 | Drools / DRL authoring and rule governance | Rules |
| SKL-015 | Accounting domain: double entry, subsidiary ledgers, closing | Domain |
| SKL-016 | Inventory domain: valuation, batches, FEFO, stock-take | Domain |
| SKL-017 | Tax domain (GST/VAT) and statutory reporting | Domain |
| SKL-018 | Distributed systems: replication, conflict resolution, idempotency | Architecture |
| SKL-019 | Offline-first design and degraded-mode UX | Architecture |
| SKL-020 | Applied cryptography: hashing, AEAD, key management, TLS | Security |
| SKL-021 | Threat modelling and secure coding | Security |
| SKL-022 | Reporting engines and print technologies (PDFBox, ESC/POS, ZPL) | Reporting |
| SKL-023 | Peripheral integration (serial/USB, scanners, scales, drawers) | Integration |
| SKL-024 | Testing: JUnit 5, Testcontainers, ArchUnit, property-based, simulation | Quality |
| SKL-025 | Performance engineering: JMH, profiling, JFR, query tuning | Quality |
| SKL-026 | CI/CD, Gradle, jlink/jpackage, release engineering | DevOps |
| SKL-027 | Linux/Windows administration, PostgreSQL operations, backup/restore | Ops |
| SKL-028 | Observability: structured logging, Micrometer, alerting | Ops |
| SKL-029 | Technical writing and specification | Cross |
| SKL-030 | Requirements elicitation with non-technical retailers | Cross |
| SKL-031 | Localisation and accessibility engineering | Cross |
| SKL-032 | Open-source licence compliance and SBOM | Cross |
| SKL-033 | Implementation/onboarding of a live shop (data migration, training) | Field |
| SKL-034 | Support triage and diagnostics from log bundles | Field |

## 2. Role × skill requirements

Target levels; blank = not required.

| Skill | Arch | Core BE | UI Eng | Domain (Acct) | Rules Eng | QA | DevOps | Support | Impl. Consultant |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| SKL-001 Java 21 | 4 | 4 | 3 | 3 | 3 | 3 | 2 | 1 | 1 |
| SKL-002 JPMS/Gradle | 4 | 3 | 2 | 2 | 2 | 2 | 3 | | |
| SKL-003 DDD | 4 | 4 | 2 | 3 | 2 | 2 | | | |
| SKL-004 Spring Boot | 3 | 4 | 2 | 3 | 3 | 2 | 2 | 1 | |
| SKL-005 Spring Security | 4 | 3 | 2 | | | 2 | 2 | 1 | |
| SKL-006 Hibernate/JPA | 3 | 4 | 2 | 3 | | 2 | | | |
| SKL-007 SQL/PostgreSQL | 4 | 4 | 2 | 3 | 2 | 3 | 3 | 2 | 2 |
| SKL-008 Flyway | 3 | 3 | 1 | 2 | | 2 | 3 | 1 | |
| SKL-009 JavaFX | 2 | 2 | 4 | | | 3 | | 1 | 1 |
| SKL-010 Vaadin | 2 | 2 | 3 | | | 2 | | | |
| SKL-013 POS UX | 3 | 2 | 4 | 2 | | 2 | | 2 | 3 |
| SKL-014 Drools | 3 | 2 | 1 | 3 | 4 | 2 | | 1 | 3 |
| SKL-015 Accounting | 3 | 3 | 1 | 4 | 3 | 3 | | 2 | 4 |
| SKL-016 Inventory | 3 | 3 | 2 | 3 | 3 | 3 | | 2 | 4 |
| SKL-017 Tax | 2 | 2 | 1 | 4 | 3 | 2 | | 2 | 4 |
| SKL-018 Distributed | 4 | 3 | 1 | | | 3 | 2 | 1 | |
| SKL-019 Offline-first | 4 | 3 | 3 | 2 | | 3 | 2 | 2 | 2 |
| SKL-020 Crypto | 4 | 2 | 1 | | | 2 | 3 | 1 | |
| SKL-021 Threat modelling | 4 | 2 | 2 | | | 2 | 2 | | |
| SKL-022 Reporting/print | 2 | 2 | 3 | 2 | | 2 | | 2 | 3 |
| SKL-023 Peripherals | 2 | 1 | 3 | | | 2 | 2 | 3 | 3 |
| SKL-024 Testing | 3 | 3 | 3 | 2 | 2 | 4 | 2 | | |
| SKL-025 Performance | 4 | 3 | 3 | | | 3 | 3 | | |
| SKL-026 CI/CD/packaging | 3 | 2 | 2 | | | 2 | 4 | 1 | |
| SKL-027 Sysadmin/PG ops | 2 | 2 | 1 | | | 2 | 4 | 3 | 3 |
| SKL-028 Observability | 3 | 2 | 1 | | | 2 | 4 | 3 | |
| SKL-029 Writing | 4 | 3 | 3 | 3 | 3 | 3 | 3 | 3 | 3 |
| SKL-030 Elicitation | 3 | 1 | 2 | 3 | 2 | 1 | | 2 | 4 |
| SKL-031 i18n/a11y | 2 | 1 | 3 | | | 2 | | | 2 |
| SKL-032 Licence/SBOM | 3 | 1 | 1 | | | 1 | 3 | | |
| SKL-033 Implementation | 1 | | | 2 | 2 | | 2 | 3 | 4 |
| SKL-034 Support triage | 1 | 2 | 2 | 1 | 1 | 2 | 3 | 4 | 3 |

## 3. Minimum viable team by phase

| Phase | Team |
| --- | --- |
| 1 Core ERP | 1 architect, 2 core backend, 1 UI (JavaFX), 1 domain/accounting, 1 QA, 0.5 DevOps |
| 2 LAN/Web | + 1 UI (Vaadin), + 0.5 rules engineer, + 0.5 support |
| 3 Mobile | + 1 Compose/Kotlin engineer |
| 4 Resilience | existing team + 0.5 UI for TUI |
| 5 Cloud | + 1 backend (cloud/multi-tenant), + 0.5 DevOps/SRE |

## 4. Critical single points of failure

Drools governance (SKL-014), sync/conflict design (SKL-018/019) and accounting correctness (SKL-015) each need at least two people at level 3+ before the phase that depends on them ships. Track this explicitly in the training plan.
