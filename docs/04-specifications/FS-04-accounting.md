# FS-04 — Accounting functional specification

Traces: BR-040..BR-047, CMP-01..CMP-07

## 1. Chart of accounts

**FR-401** Hierarchical chart with account types `ASSET, LIABILITY, EQUITY, INCOME, EXPENSE` and subtypes (bank, cash, receivable, payable, inventory, tax, COGS, …). Shipped templates: retail-generic, textile, hardware, with the ability to import/export a chart as CSV.

**FR-402** Accounts carry: code, name, type, parent, control-account flag (receivable/payable are subsidiary-controlled and cannot be posted to directly except via party documents), cost-centre-required flag, active flag, opening balance per financial year.

**FR-403** An account with postings cannot be deleted or have its type changed; it can be deactivated and renamed (audited).

## 2. Vouchers and posting

**FR-410** Voucher types: `SALE, SALE_RETURN, PURCHASE, PURCHASE_RETURN, RECEIPT, PAYMENT, CONTRA, JOURNAL, STOCK_ADJUSTMENT, PRODUCTION, OPENING, CLOSING`. Every voucher is balanced (Σ debit = Σ credit) and immutable after posting.

**FR-411** Subsidiary documents post automatically through `posting_rule` mappings (domain model §5). The mapping is editable by an accountant with `accounting.postingRule.edit`, validated against the chart, versioned and audited.

**FR-412** Manual journals require `accounting.journal.post`, a narration, and (for reversal journals) a link to the original voucher. Journal templates and recurring journals (rent, depreciation) are supported with a scheduler.

**FR-413** Corrections: a posted voucher is reversed, never edited (CMP-05). Reversal copies mirrored lines, carries a reason and links both directions.

**FR-414** Cost-centre / branch tagging is optional per account and can be made mandatory; segment P&L reports group by it.

## 3. Tax

**FR-420** Tax model is configurable: components (CGST/SGST/IGST/CESS or a single VAT), rate tables by tax class and effective date, place-of-supply determination (intra/inter-state), reverse charge, exemptions by customer type or item.

**FR-421** Tax is computed per line by the `TAX_DETERMINATION` decision and stored per line and per component (never recomputed at report time), with the rule trail retained (rules design §5).

**FR-422** Tax ledgers: output tax, input tax, tax payable, reverse-charge liability; a period tax summary computes net liability with an input-credit breakdown.

**FR-423** Statutory exports: HSN/SAC summary, B2B/B2C document summary, rate-wise turnover, in CSV/XLSX/JSON formats defined as data templates so a jurisdiction change does not need code (CMP-03, CMP-07).

**FR-424** Rounding: tax computed per line at 4 decimals, rounded to currency scale per invoice component, with the difference in a round-off line; the method is a tenant setting and is stated on the invoice.

## 4. Party ledgers

**FR-430** Customer and supplier sub-ledgers reconcile to their control accounts. Statements show opening, documents, payments, allocations and closing per period; ageing buckets are configurable.

**FR-431** Allocation of receipts/payments to documents: automatic FIFO or manual; unallocated amounts sit as advances and are visible in ageing.

**FR-432** Credit notes, debit notes, write-offs (bad debt, permission-gated) and adjustments between parties (with approval) are supported.

## 5. Banking

**FR-440** Bank and cash accounts with opening balances; contra entries for cash ↔ bank movement; cheque register with status (issued, presented, bounced, cancelled).

**FR-441** Bank reconciliation: import a statement (CSV/MT940), auto-match by amount+date+reference within tolerance, manual match UI, unreconciled report, and a reconciliation statement per period (BR-045).

## 6. Periods and close

**FR-450** Financial year and periods (monthly by default) with statuses `OPEN, SOFT_CLOSED (warn), LOCKED`. Posting into a LOCKED period is rejected at the domain layer.

**FR-451** Month-end checklist: unposted documents, unallocated receipts, GRNI ageing, stock vs inventory-account reconciliation, tax summary review, bank reconciliation.

**FR-452** Year close: computes P&L, transfers to retained earnings, generates opening balances for the next year, produces a signed snapshot (hash of trial balance + document counts) stored with the backup (CMP-06). Re-open requires owner + accountant approval and is audited.

## 7. Statements

**FR-460** Day book, ledger statement, trial balance (summary and detailed, any date range), profit & loss, balance sheet, cash flow (indirect), cash and bank books, journal register, tax summary, HSN summary, receivables/payables ageing, cost-centre P&L.

**FR-461** Every statement drills down to vouchers and then to the source document (invoice, GRN, adjustment).

**FR-462** Comparative columns (previous period / previous year) and export to PDF/CSV/XLSX.

## 8. Controls

**FR-470** Four-eyes options for: manual journals above a threshold, posting-rule changes, period unlock, write-offs, and year re-open.

**FR-471** Exception reports: documents dated outside the current period, back-dated postings, manual journals touching inventory or tax accounts, round-off beyond tolerance, unbalanced-by-rounding attempts, control accounts posted directly.

## 9. Acceptance criteria (samples)

| ID | Given | When | Then |
| --- | --- | --- | --- |
| AC-401 | A cash sale of ₹1,180 (₹1,000 + 18% tax) | Invoice finalised | Voucher debits cash 1,180; credits sales 1,000, output tax 180; COGS/inventory posted per cost |
| AC-402 | March is LOCKED | A 20-March journal is attempted | Rejected with `PERIOD_LOCKED`; posting into April with a reversal is offered |
| AC-403 | A month of POS activity exists | Trial balance is run | Debits equal credits exactly; inventory account equals stock valuation report |
| AC-404 | A posted purchase invoice has a wrong supplier | Correction requested | Reversal voucher created and a new invoice posted; both linked; audit shows actor and reason |
| AC-405 | Year close is run for FY 2026-27 | Completed | P&L transferred to retained earnings; FY 2027-28 opening balances match FY 2026-27 closing; snapshot hash stored |
