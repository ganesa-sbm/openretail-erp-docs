# Traceability matrix

Links requirements to the tasks that implement them, in both directions. Ranges in a task's "Traces to" field (e.g. `FR-101..FR-108`) are shown as written; the reverse index below lists the range endpoints only, so use the forward table when hunting for a specific mid-range identifier.

Document sources: `docs/01-brd/BRD.md` (BR), `docs/01-brd/non-functional-requirements.md` (NFR), `docs/01-brd/compliance-and-audit.md` (CMP), `docs/04-specifications/` (FR, TS), `docs/02-architecture/adr/` (ADR), `docs/02-architecture/deployment-architecture.md` (DEP).

## 1. Task → requirements

| Task | Traces to |
| --- | --- |
| [TASK-001](../06-tasks/TASK-001-repo-and-build-skeleton.md) | NFR-023, NFR-018 |
| [TASK-002](../06-tasks/TASK-002-jpms-module-graph.md) | NFR-018, ADR-005 |
| [TASK-003](../06-tasks/TASK-003-core-value-types.md) | NFR-026, ADR-002 |
| [TASK-004](../06-tasks/TASK-004-event-bus-and-outbox.md) | ADR-002, FR-501 |
| [TASK-005](../06-tasks/TASK-005-persistence-baseline.md) | NFR-019, ADR-008 |
| [TASK-006](../06-tasks/TASK-006-common-columns-and-concurrency.md) | NFR-026, ADR-001 |
| [TASK-007](../06-tasks/TASK-007-audit-log-hash-chain.md) | BR-070, NFR-011, CMP-08 |
| [TASK-008](../06-tasks/TASK-008-identity-model.md) | BR-060..063, FR-501 |
| [TASK-009](../06-tasks/TASK-009-authentication.md) | BR-060, NFR-009, FR-505 |
| [TASK-010](../06-tasks/TASK-010-authorization-enforcement.md) | BR-061..063 |
| [TASK-011](../06-tasks/TASK-011-node-identity-and-pairing.md) | BR-028, DEP-04 |
| [TASK-012](../06-tasks/TASK-012-observability-baseline.md) | NFR-021 |
| [TASK-013](../06-tasks/TASK-013-installers-and-packaging.md) | NFR-022, NFR-025, DEP-03 |
| [TASK-014](../06-tasks/TASK-014-product-and-variant-master.md) | FR-101..FR-108, BR-001..BR-004 |
| [TASK-015](../06-tasks/TASK-015-units-of-measure.md) | FR-109..FR-112, BR-005 |
| [TASK-016](../06-tasks/TASK-016-barcodes-and-labels.md) | FR-113..FR-116, BR-006 |
| [TASK-017](../06-tasks/TASK-017-tax-classes.md) | FR-117, BR-040..BR-043 |
| [TASK-018](../06-tasks/TASK-018-batches-and-expiry.md) | FR-118..FR-124, BR-007..BR-010 |
| [TASK-019](../06-tasks/TASK-019-stock-movement-ledger.md) | FR-125..FR-131, ADR-002, BR-011..BR-014 |
| [TASK-020](../06-tasks/TASK-020-inventory-valuation.md) | FR-132..FR-136, BR-015 |
| [TASK-021](../06-tasks/TASK-021-stock-take.md) | FR-137..FR-141, BR-016 |
| [TASK-022](../06-tasks/TASK-022-stock-transfers.md) | FR-142..FR-145, BR-017 |
| [TASK-023](../06-tasks/TASK-023-bom-and-production.md) | FR-146..FR-152, BR-018..BR-020 |
| [TASK-024](../06-tasks/TASK-024-paint-tinting-and-loose-stock.md) | FR-153..FR-157, BR-021 |
| [TASK-025](../06-tasks/TASK-025-reorder-and-stock-alerts.md) | FR-158..FR-161, BR-022 |
| [TASK-026](../06-tasks/TASK-026-price-lists-and-resolution.md) | FR-162..FR-168, BR-023 |
| [TASK-027](../06-tasks/TASK-027-drools-decision-service.md) | FR-169..FR-173, ADR-004, BR-050 |
| [TASK-028](../06-tasks/TASK-028-rule-authoring-and-simulation.md) | FR-174..FR-178, BR-051, BR-070 |
| [TASK-029](../06-tasks/TASK-029-discounts-and-approvals.md) | FR-179..FR-184, BR-024, BR-064 |
| [TASK-030](../06-tasks/TASK-030-counter-sessions-and-day-close.md) | FR-201..FR-208, BR-025, BR-035 |
| [TASK-031](../06-tasks/TASK-031-sales-invoice-aggregate.md) | FR-209..FR-216, BR-026, ADR-002 |
| [TASK-032](../06-tasks/TASK-032-pos-billing-screen.md) | FR-217..FR-226, NFR-001, NFR-012 |
| [TASK-033](../06-tasks/TASK-033-tenders-and-payments.md) | FR-227..FR-232, BR-027 |
| [TASK-034](../06-tasks/TASK-034-hold-and-recall.md) | FR-233..FR-236 |
| [TASK-035](../06-tasks/TASK-035-sales-returns-and-exchanges.md) | FR-237..FR-243, BR-029 |
| [TASK-036](../06-tasks/TASK-036-customers-credit-and-loyalty.md) | FR-244..FR-250, BR-030, BR-031 |
| [TASK-037](../06-tasks/TASK-037-offline-document-numbering.md) | FR-251..FR-254, ADR-006, BR-032, CMP-01 |
| [TASK-038](../06-tasks/TASK-038-document-printing.md) | FR-255..FR-259, BR-033, CMP-02 |
| [TASK-039](../06-tasks/TASK-039-peripheral-adapters.md) | FR-260..FR-264, BR-034 |
| [TASK-040](../06-tasks/TASK-040-supplier-master.md) | FR-301..FR-305, BR-036 |
| [TASK-041](../06-tasks/TASK-041-purchase-orders.md) | FR-306..FR-312, BR-037 |
| [TASK-042](../06-tasks/TASK-042-goods-receipts.md) | FR-313..FR-319, BR-038 |
| [TASK-043](../06-tasks/TASK-043-purchase-invoices-and-matching.md) | FR-320..FR-326, BR-039 |
| [TASK-044](../06-tasks/TASK-044-purchase-returns.md) | FR-327..FR-330 |
| [TASK-045](../06-tasks/TASK-045-landed-costs.md) | FR-331..FR-334, BR-041 |
| [TASK-046](../06-tasks/TASK-046-supplier-payments-and-payables.md) | FR-335..FR-340, BR-042 |
| [TASK-047](../06-tasks/TASK-047-supplier-catalogue-import.md) | FR-341..FR-344 |
| [TASK-048](../06-tasks/TASK-048-chart-of-accounts.md) | FR-401..FR-406, BR-043 |
| [TASK-049](../06-tasks/TASK-049-posting-engine.md) | FR-407..FR-414, BR-044, ADR-002 |
| [TASK-050](../06-tasks/TASK-050-automatic-posting-rules.md) | FR-415..FR-420, BR-045 |
| [TASK-051](../06-tasks/TASK-051-party-ledgers-and-statements.md) | FR-421..FR-425, BR-046 |
| [TASK-052](../06-tasks/TASK-052-tax-ledgers-and-exports.md) | FR-426..FR-431, BR-047, CMP-03 |
| [TASK-053](../06-tasks/TASK-053-financial-statements.md) | FR-432..FR-437, BR-048, NFR-004 |
| [TASK-054](../06-tasks/TASK-054-bank-and-reconciliation.md) | FR-438..FR-443, BR-049 |
| [TASK-055](../06-tasks/TASK-055-period-locks-and-year-close.md) | FR-444..FR-449, BR-048, CMP-06 |
| [TASK-056](../06-tasks/TASK-056-cost-centres.md) | FR-450..FR-453 |
| [TASK-057](../06-tasks/TASK-057-payload-ui-schema.md) | FR-501..FR-506, ADR-003 |
| [TASK-058](../06-tasks/TASK-058-fx-payload-renderer.md) | FR-507..FR-511, NFR-003 |
| [TASK-059](../06-tasks/TASK-059-layout-designer.md) | FR-512..FR-516 |
| [TASK-060](../06-tasks/TASK-060-report-engine.md) | FR-517..FR-522, BR-052 |
| [TASK-061](../06-tasks/TASK-061-report-designer.md) | FR-523..FR-528, BR-052 |
| [TASK-062](../06-tasks/TASK-062-printing-service.md) | FR-529..FR-532, BR-033 |
| [TASK-063](../06-tasks/TASK-063-notification-service.md) | FR-533..FR-539, BR-053, BR-054 |
| [TASK-064](../06-tasks/TASK-064-notification-subscriptions.md) | FR-540..FR-543 |
| [TASK-065](../06-tasks/TASK-065-analytics-dashboards.md) | FR-544..FR-550, BR-055 |
| [TASK-066](../06-tasks/TASK-066-demand-forecasting.md) | FR-551..FR-554, BR-056 |
| [TASK-067](../06-tasks/TASK-067-sync-protocol.md) | FR-555..FR-562, ADR-001, BR-057 |
| [TASK-068](../06-tasks/TASK-068-sync-agent-and-reconciliation.md) | FR-563..FR-568, BR-058 |
| [TASK-069](../06-tasks/TASK-069-store-server-and-rest-api.md) | TS-01, FR-569..FR-574, ADR-007 |
| [TASK-070](../06-tasks/TASK-070-thin-client-mode.md) | FR-575..FR-578, DEP-02 |
| [TASK-071](../06-tasks/TASK-071-webhooks.md) | FR-579..FR-581 |
| [TASK-072](../06-tasks/TASK-072-vaadin-web-client.md) | FR-582..FR-586, DEP-02 |
| [TASK-073](../06-tasks/TASK-073-plugin-spi-and-loader.md) | FR-587..FR-592, ADR-005, BR-059 |
| [TASK-074](../06-tasks/TASK-074-plugin-administration.md) | FR-593..FR-596 |
| [TASK-075](../06-tasks/TASK-075-admin-console.md) | FR-597..FR-604, BR-065 |
| [TASK-076](../06-tasks/TASK-076-textile-plugin.md) | BR-066, FR-605..FR-609 |
| [TASK-077](../06-tasks/TASK-077-paint-hardware-plugin.md) | BR-067, FR-610..FR-614 |
| [TASK-078](../06-tasks/TASK-078-processing-and-grocery-plugins.md) | BR-068, FR-615..FR-620 |
| [TASK-079](../06-tasks/TASK-079-backup-and-restore.md) | BR-071, NFR-007, NFR-008 |
| [TASK-080](../06-tasks/TASK-080-node-health-monitoring.md) | NFR-021, FR-621..FR-624 |
| [TASK-081](../06-tasks/TASK-081-sync-simulation-harness.md) | TS-05, NFR-006 |
| [TASK-082](../06-tasks/TASK-082-performance-test-suite.md) | TS-05, NFR-001..NFR-005 |
| [TASK-083](../06-tasks/TASK-083-security-hardening.md) | NFR-009..NFR-011, BR-069 |
| [TASK-084](../06-tasks/TASK-084-localization-and-accessibility.md) | NFR-013, NFR-014, BR-072 |
| [TASK-085](../06-tasks/TASK-085-upgrade-and-migration.md) | NFR-025, BR-073 |
| [TASK-086](../06-tasks/TASK-086-data-migration-toolkit.md) | BR-074, FR-625..FR-628 |
| [TASK-087](../06-tasks/TASK-087-lanterna-terminal-client.md) | BR-075, FR-629..FR-632, NFR-022 |
| [TASK-088](../06-tasks/TASK-088-mobile-app.md) | BR-076, FR-633..FR-638 |
| [TASK-089](../06-tasks/TASK-089-cloud-tier.md) | BR-077, FR-639..FR-645, NFR-007 |
| [TASK-090](../06-tasks/TASK-090-user-documentation.md) | BR-078, NFR-012 |
| [TASK-091](../06-tasks/TASK-091-acceptance-and-release-gates.md) | TS-05, BR-079 |
| [TASK-092](../06-tasks/TASK-092-pilot-and-hypercare.md) | BR-080 |

## 2. Requirement → tasks

| Requirement | Tasks |
| --- | --- |
| ADR-001 | TASK-006, TASK-067 |
| ADR-002 | TASK-003, TASK-004, TASK-019, TASK-031, TASK-049 |
| ADR-003 | TASK-057 |
| ADR-004 | TASK-027 |
| ADR-005 | TASK-002, TASK-073 |
| ADR-006 | TASK-037 |
| ADR-007 | TASK-069 |
| ADR-008 | TASK-005 |
| BR-001 | TASK-014 |
| BR-004 | TASK-014 |
| BR-005 | TASK-015 |
| BR-006 | TASK-016 |
| BR-007 | TASK-018 |
| BR-010 | TASK-018 |
| BR-011 | TASK-019 |
| BR-014 | TASK-019 |
| BR-015 | TASK-020 |
| BR-016 | TASK-021 |
| BR-017 | TASK-022 |
| BR-018 | TASK-023 |
| BR-020 | TASK-023 |
| BR-021 | TASK-024 |
| BR-022 | TASK-025 |
| BR-023 | TASK-026 |
| BR-024 | TASK-029 |
| BR-025 | TASK-030 |
| BR-026 | TASK-031 |
| BR-027 | TASK-033 |
| BR-028 | TASK-011 |
| BR-029 | TASK-035 |
| BR-030 | TASK-036 |
| BR-031 | TASK-036 |
| BR-032 | TASK-037 |
| BR-033 | TASK-038, TASK-062 |
| BR-034 | TASK-039 |
| BR-035 | TASK-030 |
| BR-036 | TASK-040 |
| BR-037 | TASK-041 |
| BR-038 | TASK-042 |
| BR-039 | TASK-043 |
| BR-040 | TASK-017 |
| BR-041 | TASK-045 |
| BR-042 | TASK-046 |
| BR-043 | TASK-017, TASK-048 |
| BR-044 | TASK-049 |
| BR-045 | TASK-050 |
| BR-046 | TASK-051 |
| BR-047 | TASK-052 |
| BR-048 | TASK-053, TASK-055 |
| BR-049 | TASK-054 |
| BR-050 | TASK-027 |
| BR-051 | TASK-028 |
| BR-052 | TASK-060, TASK-061 |
| BR-053 | TASK-063 |
| BR-054 | TASK-063 |
| BR-055 | TASK-065 |
| BR-056 | TASK-066 |
| BR-057 | TASK-067 |
| BR-058 | TASK-068 |
| BR-059 | TASK-073 |
| BR-060 | TASK-008, TASK-009 |
| BR-061 | TASK-010 |
| BR-064 | TASK-029 |
| BR-065 | TASK-075 |
| BR-066 | TASK-076 |
| BR-067 | TASK-077 |
| BR-068 | TASK-078 |
| BR-069 | TASK-083 |
| BR-070 | TASK-007, TASK-028 |
| BR-071 | TASK-079 |
| BR-072 | TASK-084 |
| BR-073 | TASK-085 |
| BR-074 | TASK-086 |
| BR-075 | TASK-087 |
| BR-076 | TASK-088 |
| BR-077 | TASK-089 |
| BR-078 | TASK-090 |
| BR-079 | TASK-091 |
| BR-080 | TASK-092 |
| CMP-01 | TASK-037 |
| CMP-02 | TASK-038 |
| CMP-03 | TASK-052 |
| CMP-06 | TASK-055 |
| CMP-08 | TASK-007 |
| DEP-02 | TASK-070, TASK-072 |
| DEP-03 | TASK-013 |
| DEP-04 | TASK-011 |
| FR-101 | TASK-014 |
| FR-108 | TASK-014 |
| FR-109 | TASK-015 |
| FR-112 | TASK-015 |
| FR-113 | TASK-016 |
| FR-116 | TASK-016 |
| FR-117 | TASK-017 |
| FR-118 | TASK-018 |
| FR-124 | TASK-018 |
| FR-125 | TASK-019 |
| FR-131 | TASK-019 |
| FR-132 | TASK-020 |
| FR-136 | TASK-020 |
| FR-137 | TASK-021 |
| FR-141 | TASK-021 |
| FR-142 | TASK-022 |
| FR-145 | TASK-022 |
| FR-146 | TASK-023 |
| FR-152 | TASK-023 |
| FR-153 | TASK-024 |
| FR-157 | TASK-024 |
| FR-158 | TASK-025 |
| FR-161 | TASK-025 |
| FR-162 | TASK-026 |
| FR-168 | TASK-026 |
| FR-169 | TASK-027 |
| FR-173 | TASK-027 |
| FR-174 | TASK-028 |
| FR-178 | TASK-028 |
| FR-179 | TASK-029 |
| FR-184 | TASK-029 |
| FR-201 | TASK-030 |
| FR-208 | TASK-030 |
| FR-209 | TASK-031 |
| FR-216 | TASK-031 |
| FR-217 | TASK-032 |
| FR-226 | TASK-032 |
| FR-227 | TASK-033 |
| FR-232 | TASK-033 |
| FR-233 | TASK-034 |
| FR-236 | TASK-034 |
| FR-237 | TASK-035 |
| FR-243 | TASK-035 |
| FR-244 | TASK-036 |
| FR-250 | TASK-036 |
| FR-251 | TASK-037 |
| FR-254 | TASK-037 |
| FR-255 | TASK-038 |
| FR-259 | TASK-038 |
| FR-260 | TASK-039 |
| FR-264 | TASK-039 |
| FR-301 | TASK-040 |
| FR-305 | TASK-040 |
| FR-306 | TASK-041 |
| FR-312 | TASK-041 |
| FR-313 | TASK-042 |
| FR-319 | TASK-042 |
| FR-320 | TASK-043 |
| FR-326 | TASK-043 |
| FR-327 | TASK-044 |
| FR-330 | TASK-044 |
| FR-331 | TASK-045 |
| FR-334 | TASK-045 |
| FR-335 | TASK-046 |
| FR-340 | TASK-046 |
| FR-341 | TASK-047 |
| FR-344 | TASK-047 |
| FR-401 | TASK-048 |
| FR-406 | TASK-048 |
| FR-407 | TASK-049 |
| FR-414 | TASK-049 |
| FR-415 | TASK-050 |
| FR-420 | TASK-050 |
| FR-421 | TASK-051 |
| FR-425 | TASK-051 |
| FR-426 | TASK-052 |
| FR-431 | TASK-052 |
| FR-432 | TASK-053 |
| FR-437 | TASK-053 |
| FR-438 | TASK-054 |
| FR-443 | TASK-054 |
| FR-444 | TASK-055 |
| FR-449 | TASK-055 |
| FR-450 | TASK-056 |
| FR-453 | TASK-056 |
| FR-501 | TASK-004, TASK-008, TASK-057 |
| FR-505 | TASK-009 |
| FR-506 | TASK-057 |
| FR-507 | TASK-058 |
| FR-511 | TASK-058 |
| FR-512 | TASK-059 |
| FR-516 | TASK-059 |
| FR-517 | TASK-060 |
| FR-522 | TASK-060 |
| FR-523 | TASK-061 |
| FR-528 | TASK-061 |
| FR-529 | TASK-062 |
| FR-532 | TASK-062 |
| FR-533 | TASK-063 |
| FR-539 | TASK-063 |
| FR-540 | TASK-064 |
| FR-543 | TASK-064 |
| FR-544 | TASK-065 |
| FR-550 | TASK-065 |
| FR-551 | TASK-066 |
| FR-554 | TASK-066 |
| FR-555 | TASK-067 |
| FR-562 | TASK-067 |
| FR-563 | TASK-068 |
| FR-568 | TASK-068 |
| FR-569 | TASK-069 |
| FR-574 | TASK-069 |
| FR-575 | TASK-070 |
| FR-578 | TASK-070 |
| FR-579 | TASK-071 |
| FR-581 | TASK-071 |
| FR-582 | TASK-072 |
| FR-586 | TASK-072 |
| FR-587 | TASK-073 |
| FR-592 | TASK-073 |
| FR-593 | TASK-074 |
| FR-596 | TASK-074 |
| FR-597 | TASK-075 |
| FR-604 | TASK-075 |
| FR-605 | TASK-076 |
| FR-609 | TASK-076 |
| FR-610 | TASK-077 |
| FR-614 | TASK-077 |
| FR-615 | TASK-078 |
| FR-620 | TASK-078 |
| FR-621 | TASK-080 |
| FR-624 | TASK-080 |
| FR-625 | TASK-086 |
| FR-628 | TASK-086 |
| FR-629 | TASK-087 |
| FR-632 | TASK-087 |
| FR-633 | TASK-088 |
| FR-638 | TASK-088 |
| FR-639 | TASK-089 |
| FR-645 | TASK-089 |
| NFR-001 | TASK-032, TASK-082 |
| NFR-003 | TASK-058 |
| NFR-004 | TASK-053 |
| NFR-005 | TASK-082 |
| NFR-006 | TASK-081 |
| NFR-007 | TASK-079, TASK-089 |
| NFR-008 | TASK-079 |
| NFR-009 | TASK-009, TASK-083 |
| NFR-011 | TASK-007, TASK-083 |
| NFR-012 | TASK-032, TASK-090 |
| NFR-013 | TASK-084 |
| NFR-014 | TASK-084 |
| NFR-018 | TASK-001, TASK-002 |
| NFR-019 | TASK-005 |
| NFR-021 | TASK-012, TASK-080 |
| NFR-022 | TASK-013, TASK-087 |
| NFR-023 | TASK-001 |
| NFR-025 | TASK-013, TASK-085 |
| NFR-026 | TASK-003, TASK-006 |
| TS-01 | TASK-069 |
| TS-05 | TASK-081, TASK-082, TASK-091 |

## 3. Coverage rules

- Every `MUST` business requirement must appear in section 2 with at least one task. A gap here is a planning defect, not a documentation defect.
- Every task must trace to at least one requirement; a task that traces to nothing is either unnecessary or a missing requirement.
- When a task is split, the children inherit the parent's traces and the parent is removed from this matrix.
- This matrix is regenerated from the task documents' `Traces to` fields; edit the task documents, not the matrix.
