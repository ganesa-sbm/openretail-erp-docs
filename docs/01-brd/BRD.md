# Business Requirements Document — OpenRetail ERP

Status: DRAFT · Version 0.1 · Owner: Product

## 1. Executive summary

Small and medium retailers in India and comparable markets run on a mix of paper, spreadsheets and legacy Windows billing software that is licence-locked, online-dependent or unable to model their inventory (batches, expiry, size/colour matrices, blends, mixing). OpenRetail ERP delivers inventory, POS and double-entry accounting in one open-source, offline-first product that runs on a single shop PC and scales to a LAN of counters, with optional cloud sync for multi-store owners.

The commercial premise: zero licence cost, no internet dependency, and configurability (UI layouts, business rules, reports, plugins) that lets one codebase serve departmental stores, textile shops, hardware stores, paint dealers and small processors.

## 2. Business objectives

| ID | Objective | Measure of success |
| --- | --- | --- |
| OBJ-1 | Let a shop bill customers without interruption regardless of network or cloud availability | 100% of POS operations function with network disconnected; zero data loss on reconnect |
| OBJ-2 | Produce statutory-grade books without a separate accounting package | Trial balance, P&L and balance sheet generated directly from POS/purchase activity with no manual re-entry |
| OBJ-3 | Fit multiple retail verticals without code forks | New vertical onboarded through configuration + plugin only, no core change |
| OBJ-4 | Cut billing time per customer | ≤ 8 seconds per line item scan-to-print on commodity hardware |
| OBJ-5 | Make the owner's data safe and portable | Daily verified backups; one-command restore; full export in open formats |
| OBJ-6 | Keep total cost of ownership near zero | 100% Apache/EPL/MIT/GPL-compatible open-source stack, no paid runtime component |

## 3. Scope

### In scope (product)

Inventory management, purchasing, POS/billing, double-entry accounting, GST/VAT-style tax handling, customer & supplier masters, loyalty basics, report designer, notification service (SMS/email), analytics dashboards, user/role administration, backup/restore, offline-first sync, plugin framework, payload-driven UI, terminal fallback client.

### Out of scope (initial release)

Payroll and HR, full manufacturing MRP/scheduling, e-commerce storefront, third-party marketplace integration, fixed-asset depreciation schedules beyond simple straight-line, statutory e-filing submission (export files are produced; filing is manual), multi-currency ledgers (single functional currency per tenant in Phase 1).

### Deployment modes in scope

| Mode | Description | Target customer |
| --- | --- | --- |
| M1 Standalone | JavaFX client + embedded/local PostgreSQL on one PC | Single-counter shop |
| M2 LAN | Central store server (Spring Boot + PostgreSQL); JavaFX and browser (Vaadin) thin clients | 2–10 counters, one store |
| M3 Hybrid | Each counter holds a local DB and keeps billing when the server is down; sync layer reconciles | Stores with unreliable power/network |
| M4 Cloud-connected | Store nodes sync to a cloud tenant for consolidated reporting, off-site backup, remote monitoring | Multi-store owners |

## 4. Stakeholders

See `stakeholders.md`.

## 5. Business requirements

### 5.1 Inventory

| ID | Requirement | Priority | Mode |
| --- | --- | --- | --- |
| BR-001 | Maintain product masters with category, brand, HSN/SAC, UOM and conversions, tax class, reorder level | MUST | M1 |
| BR-002 | Support variant matrices (size × colour × design) for textiles and footwear without creating unmanageable SKU lists manually | MUST | M1 |
| BR-003 | Track stock by batch with manufacture date, expiry date, MRP and landed cost; enforce FEFO issue for expiry-tracked goods | MUST | M1 |
| BR-004 | Record stock-in via purchase, purchase return reversal, opening stock, transfer-in and production output | MUST | M1 |
| BR-005 | Record stock-out via sale, sales return reversal, transfer-out, damage/shrinkage write-off and production consumption | MUST | M1 |
| BR-006 | Support barcode generation, printing (label templates) and scanning; support weight-embedded barcodes from weighing scales | MUST | M1 |
| BR-007 | Support multi-location and multi-bin stock within a store, plus inter-store transfers with in-transit visibility | SHOULD | M2 |
| BR-008 | Physical stock-take with count sheets, blind counts, and variance posting to an adjustment ledger | MUST | M1 |
| BR-009 | Alert on reorder level, slow-moving stock, near-expiry stock and negative stock attempts | MUST | M1 |
| BR-010 | Support simple processing/assembly: consume components per BOM, produce output with its own batch and costed value | SHOULD | M1 |
| BR-011 | Support per-item pricing rules: MRP, selling price lists, customer-group price, quantity slabs, promotional prices with validity windows | MUST | M1 |
| BR-012 | Support paint/hardware specifics: base + tint mixing recording, loose quantity sale from bulk packs, unit conversion at sale time | SHOULD | M1 |

### 5.2 Point of sale

| ID | Requirement | Priority | Mode |
| --- | --- | --- | --- |
| BR-020 | Bill by barcode scan, code search or name search with sub-200 ms lookup on 100k SKUs | MUST | M1 |
| BR-021 | Support hold/recall bills, multiple concurrent bills per counter, and bill editing before finalisation | MUST | M1 |
| BR-022 | Split tender: cash, card, UPI/QR, wallet, credit (on account), gift voucher, loyalty points | MUST | M1 |
| BR-023 | Apply line and bill-level discounts subject to role limits and approval workflow beyond threshold | MUST | M1 |
| BR-024 | Print/reprint thermal and A4 invoices, duplicate copies, and email/SMS the invoice on demand | MUST | M1 |
| BR-025 | Handle sales returns and exchanges with original-bill lookup and credit-note issuance | MUST | M1 |
| BR-026 | Cash drawer session management: opening float, mid-day pickups, closing count, variance report per cashier | MUST | M1 |
| BR-027 | Continue billing offline when the store server or cloud is unreachable, with guaranteed unique invoice numbering per counter | MUST | M3 |
| BR-028 | Support customer capture (phone-first), loyalty accrual/redemption, and credit limit enforcement | SHOULD | M1 |

### 5.3 Purchasing and suppliers

| ID | Requirement | Priority | Mode |
| --- | --- | --- | --- |
| BR-030 | Purchase order → goods receipt → purchase invoice → payment, with partial receipts and three-way match tolerance | MUST | M1 |
| BR-031 | Capture landed cost components (freight, duty, handling) and apportion to item cost | SHOULD | M1 |
| BR-032 | Supplier ledger with ageing, outstanding payables and payment scheduling | MUST | M1 |
| BR-033 | Purchase returns/debit notes with batch traceability | MUST | M1 |
| BR-034 | Import supplier price lists and purchase invoices from CSV/Excel | SHOULD | M1 |

### 5.4 Accounting

| ID | Requirement | Priority | Mode |
| --- | --- | --- | --- |
| BR-040 | Double-entry general ledger with user-extensible chart of accounts and mandatory balanced postings | MUST | M1 |
| BR-041 | Auto-post subsidiary transactions (sale, purchase, payment, receipt, stock adjustment, production) to the GL via configurable posting rules | MUST | M1 |
| BR-042 | Produce day book, ledger statements, trial balance, P&L, balance sheet and cash/bank books for any date range | MUST | M1 |
| BR-043 | Tax computation (GST-style CGST/SGST/IGST or single VAT), tax ledgers, HSN summary and return-ready export files | MUST | M1 |
| BR-044 | Period locking, financial-year close with opening-balance carry-forward, and post-lock adjustment via journal only | MUST | M1 |
| BR-045 | Bank reconciliation against imported statements | SHOULD | M2 |
| BR-046 | Cost-centre / branch tagging on postings for segmented P&L | SHOULD | M2 |
| BR-047 | No hard delete of financial documents; corrections via reversal entries with reason capture | MUST | M1 |

### 5.5 Configurability and extensibility

| ID | Requirement | Priority | Mode |
| --- | --- | --- | --- |
| BR-050 | Screens are described by layout payloads (JSON) so fields, columns, order, visibility and validation can be changed without a build | MUST | M1 |
| BR-051 | Business rules (tax determination, discount limits, approval routing, loyalty accrual, reorder suggestion) are authored as editable rules, versioned and audited | MUST | M1 |
| BR-052 | Drag-and-drop report designer producing printable and exportable (PDF/CSV/XLSX) reports, with templates per printer type | MUST | M1 |
| BR-053 | Vertical-specific behaviour ships as plugins (textile matrix, paint mixing, hardware bundles, processing BOM) discoverable and enable/disable-able per tenant | MUST | M1 |
| BR-054 | Label/invoice templates are user-editable and bound to printer profiles | MUST | M1 |

### 5.6 Notifications and analytics

| ID | Requirement | Priority | Mode |
| --- | --- | --- | --- |
| BR-060 | Template-based SMS and email for invoice, payment reminder, low stock, expiry, promotion and day-close summary | MUST | M2 |
| BR-061 | Queue and retry notifications created offline; never block a business transaction on delivery | MUST | M3 |
| BR-062 | Dashboards for sales trends, margins, top/bottom SKUs, stock value and ageing, cashier performance, supplier spend | MUST | M2 |
| BR-063 | Basic demand forecasting and reorder suggestion using historical consumption and seasonality | SHOULD | M4 |

### 5.7 Security, administration and continuity

| ID | Requirement | Priority | Mode |
| --- | --- | --- | --- |
| BR-070 | Role-based access control at module, action and data-scope level (store, counter, cost centre) | MUST | M1 |
| BR-071 | Strong password storage (Argon2id), lockout on repeated failure, optional PIN login for cashiers on trusted counters | MUST | M1 |
| BR-072 | All network traffic over TLS; LAN deployments use an internally issued certificate installed by the setup wizard | MUST | M2 |
| BR-073 | Encrypt sensitive columns (customer contact, credit terms, bank details, user secrets) at rest | MUST | M1 |
| BR-074 | Immutable audit log of logins, permission changes, rule/layout changes, discount overrides, approvals, price changes, voids and reversals | MUST | M1 |
| BR-075 | Scheduled local backups with integrity verification, retention policy, and one-command restore; optional encrypted cloud copy | MUST | M1 |
| BR-076 | Terminal (text) client able to bill and query stock on minimal hardware or over SSH when the GUI is unavailable | SHOULD | M4 phase |
| BR-077 | Remote monitoring of node health (last sync, disk, backup status, version) for multi-store owners and support staff | SHOULD | M4 |

## 6. Non-functional requirements

See `non-functional-requirements.md`.

## 7. Assumptions

- A shop PC is at least 4-core / 8 GB RAM / SSD; counters may be weaker thin clients.
- Single functional currency per tenant in Phase 1.
- Tax model is configurable but the reference implementation targets Indian GST semantics.
- Users have low computer literacy; keyboard-first workflows and large touch targets are mandatory.
- Internet, where present, is intermittent consumer broadband or mobile hotspot.

## 8. Constraints

- 100% open-source components with licences compatible with distribution (Apache-2.0, EPL, MIT, BSD; GPL only where isolated).
- Java-centric stack: JavaFX client, Spring Boot services, Vaadin web layer, Drools rules, Hibernate/JPA, PostgreSQL/MySQL.
- Must install and run without an internet connection, including first-run setup.

## 9. Risks

| ID | Risk | Impact | Likelihood | Mitigation |
| --- | --- | --- | --- | --- |
| RSK-1 | Sync conflicts corrupt stock or ledger balances | High | Medium | Event-sourced movements, per-entity conflict policy, no destructive merges, reconciliation report with manual resolution queue |
| RSK-2 | Offline invoice numbering collides across counters | High | Medium | Counter-prefixed sequences reserved at provisioning; uniqueness enforced by composite key |
| RSK-3 | Rule engine misconfiguration produces wrong tax | High | Medium | Rule versioning, mandatory simulation/test harness before activation, audit trail, rollback |
| RSK-4 | Scope creep from vertical-specific demands | Medium | High | Plugin boundary is contractual; core changes need an ADR |
| RSK-5 | Payload-driven UI becomes a slow ad-hoc form builder | Medium | Medium | Fixed component catalogue, schema validation, performance budget per screen |
| RSK-6 | JavaFX packaging/runtime issues on old Windows machines | Medium | Medium | jlink/jpackage runtime bundling, tested matrix from Windows 10 upward, Lanterna fallback |
| RSK-7 | Backups unverified until a disaster | High | Medium | Automated restore drill on a scratch database weekly; backup health surfaced in dashboard |

## 10. Success criteria (release gates)

1. A shop can go from installer to first invoice in under 30 minutes with no internet.
2. A month of POS activity produces a trial balance that balances to zero with no manual journals.
3. Two counters billing offline for 8 hours reconcile with zero data loss and zero duplicate invoice numbers.
4. A new vertical (paint mixing) is enabled entirely via plugin + layout payload + rules.
5. Restore from backup on a clean machine reproduces the source database bit-for-bit in business terms.

## 11. Phasing

| Phase | Content | Exit criteria |
| --- | --- | --- |
| 1 | Core ERP: JavaFX + Spring Boot + PostgreSQL; inventory, POS, purchasing, accounting, RBAC, reports, backups | Release gates 1, 2, 5 |
| 2 | LAN/web: central server, Vaadin client, notifications, dashboards, multi-counter | Gate 3 (online), 10-counter load test |
| 3 | Mobile: Compose Multiplatform stock-take and owner dashboard | Stock-take done end-to-end from a phone |
| 4 | Resilience: Lanterna terminal client, degraded-mode operations | Billing from terminal client on a 1 GB RAM machine |
| 5 | Cloud sync, consolidated multi-store reporting, remote monitoring, forecasting | Gate 3 (offline/hybrid), multi-store consolidated P&L |
