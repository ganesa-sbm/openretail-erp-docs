# Compliance, audit and data governance requirements

## Statutory / fiscal

| ID | Requirement | Note |
| --- | --- | --- |
| CMP-01 | Invoice numbering must be gapless per series and per financial year; voided invoices remain in sequence marked void | Counter-prefixed series (e.g. `C1/2026-27/000123`) |
| CMP-02 | Tax breakup (rate, taxable value, CGST/SGST/IGST or VAT) printed on every invoice and stored per line | Configurable tax model |
| CMP-03 | HSN/SAC summary per period exportable in the tax authority's expected layout | Export templates are data, not code |
| CMP-04 | Retention of financial records for 8 years including audit log | Archive tier allowed but must be restorable |
| CMP-05 | No hard delete of any posted financial document; corrections via reversal with reason | Enforced in domain layer, not only UI |
| CMP-06 | Financial-year close produces immutable opening balances and a signed snapshot hash | Snapshot stored with backup |
| CMP-07 | Where e-invoicing/e-way bills apply, produce the payload for upload; actual transmission is a plugin | Keeps core jurisdiction-neutral |

## Audit log

Events that MUST be logged with actor, node, timestamp (UTC + local), before/after values and reason where applicable:

- Authentication success/failure, lockout, password/PIN change, session elevation.
- Role and permission changes; user creation/deactivation.
- Rule (Drools) create/edit/activate/rollback; layout payload changes; report template changes.
- Price, MRP and tax-class changes on products.
- Discount override and any approval grant/denial.
- Invoice void/cancel, sales return, credit/debit note.
- Stock adjustment, write-off, transfer approval, cost correction.
- Journal posting to a closed-adjacent period, period lock/unlock.
- Backup run, restore run, database export, configuration import.
- Sync conflict resolution decisions.

Properties: append-only table, per-node hash chain (`hash(prev_hash || payload)`), replicated to server/cloud, not editable through any application path, exportable as CSV/JSON for auditors.

## Data protection

| ID | Requirement |
| --- | --- |
| DP-01 | Customer PII (name, phone, email, address) encrypted at rest with per-tenant keys |
| DP-02 | Key material stored outside the database (OS keystore / file with restricted permissions / KMS in cloud mode) |
| DP-03 | PII minimisation: phone-only capture is valid; no PII required to complete a sale |
| DP-04 | Right-to-erasure handling: anonymise customer master while retaining financial documents with a pseudonymous reference |
| DP-05 | Exports containing PII require an explicit permission and are audit-logged |
| DP-06 | Backups encrypted; cloud copies encrypted client-side before upload |

## Internal control

- Segregation of duties enforced by roles: the cashier cannot alter prices; the person posting adjustments cannot approve them; rule authors cannot activate their own rule without a second approver (configurable four-eyes).
- Mandatory reason codes for voids, returns above threshold, adjustments and manual journals.
- Exception reports: discounts above X%, voids per cashier, negative stock events, after-hours logins, out-of-sequence document dates.
