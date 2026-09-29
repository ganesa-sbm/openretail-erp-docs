# FS-03 — Purchasing functional specification

Traces: BR-030..BR-034

## 1. Supplier master

**FR-301** Supplier is a `Party` with the SUPPLIER role: code, name, tax registration, addresses, contacts, payment terms (net days, early-payment discount), default price list, lead time days, MOQ, pack size, preferred transport, GL control account override, active flag.

**FR-302** Supplier item mapping: supplier's item code, pack size, last purchase price, agreed price with validity — used for PO defaults and price-variance detection.

## 2. Purchase order

**FR-310** PO with header (supplier, expected date, delivery location, terms, currency) and lines (variant, qty in purchase UOM, rate, tax, discount, expected batch attributes). Rates default from the supplier price list; manual rates flag a price variance if beyond tolerance.

**FR-311** PO statuses: `DRAFT → APPROVED → PARTIALLY_RECEIVED → RECEIVED → CLOSED | CANCELLED`. Approval threshold by value is a rule (`APPROVAL_ROUTING`).

**FR-312** PO output: printable/emailable document; optional reorder-suggestion seeding (FR-160).

**FR-313** Amendments before receipt create a new PO version with an audit trail; after partial receipt, only open quantities may be amended.

## 3. Goods receipt

**FR-320** GRN against one or more POs, or without a PO (direct receipt, permission-gated). Captures per line: received qty, rejected qty + reason, batch code, manufacture/expiry dates, MRP, unit cost.

**FR-321** Over-receipt beyond tolerance (default 0%) is blocked; within tolerance it is allowed and flagged. Short receipts leave the PO partially received.

**FR-322** Posting a GRN creates `PURCHASE` movements and accrues the goods-received-not-invoiced (GRNI) liability. Rejected goods are not stocked; they produce a return-to-supplier record.

**FR-323** Barcode assistance: scanning a supplier barcode maps to the variant via the supplier item mapping; unmapped barcodes prompt to create the mapping inline.

## 4. Purchase invoice and matching

**FR-330** Supplier invoice entry references GRNs (three-way match: PO ↔ GRN ↔ invoice) or is entered standalone for services.

**FR-331** Match tolerances per tenant: quantity (units/%), price (%), total (amount). Within tolerance → auto-post; outside → hold with a variance report and approval by `purchasing.invoice.approveVariance`.

**FR-332** Posting clears GRNI, debits inventory/expense and input tax, credits accounts payable.

**FR-333** Duplicate detection on (supplier, supplier invoice number, financial year).

## 5. Landed cost

**FR-340** Landed-cost documents allocate freight, duty, insurance, handling across receipt lines by value, quantity, weight or volume. Allocation at receipt time updates batch cost directly; later allocation creates `REVALUE` movements plus GL adjustment (FR-131).

## 6. Returns and debit notes

**FR-350** Purchase return references the original GRN/batch, removes stock (`PURCHASE_RETURN`), and issues a debit note reversing inventory and input tax against payables. Reasons: damaged, expired, wrong item, quality, excess.

## 7. Payments

**FR-360** Payment against one or more supplier invoices with allocation (FIFO or manual), part payments, and advances (unallocated credit). Methods: cash, bank transfer, cheque, UPI.

**FR-361** Supplier ledger and payables ageing (0–30/31–60/61–90/90+) with drill-through to documents; payment scheduling list by due date with early-payment discount hints.

## 8. Imports

**FR-370** Import supplier price lists and purchase invoices from CSV/XLSX using a per-supplier mapping profile, with dry-run validation, row-level error reporting and an audited commit (integration architecture §4).

## 9. Screens

`Supplier list/edit`, `Purchase order`, `Goods receipt`, `Purchase invoice`, `Landed cost`, `Purchase return`, `Payment`, `Payables ageing`, `Reorder suggestions`, `Price variance`.

## 10. Acceptance criteria (samples)

| ID | Given | When | Then |
| --- | --- | --- | --- |
| AC-301 | PO for 100 @ ₹50 | GRN of 100 @ ₹50, invoice at ₹52 with 2% price tolerance | Within tolerance: auto-posted; batch cost ₹52; variance logged |
| AC-302 | Same, invoice at ₹60 | Posting attempted | Held as variance; approval required; audit records approver |
| AC-303 | Freight ₹1,000 on a receipt of two lines worth ₹6,000 and ₹4,000, allocated by value | Landed cost posted | Costs increase by ₹600 and ₹400; inventory GL increases by ₹1,000 |
| AC-304 | Expired batch received in error | Purchase return posted | Stock removed from that batch; debit note reverses inventory and input tax |
| AC-305 | Supplier invoice number already exists for this FY | Entry attempted | Duplicate warning with a link to the existing invoice; override requires permission |
