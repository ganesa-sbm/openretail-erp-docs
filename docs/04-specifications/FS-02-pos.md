# FS-02 — Point of sale functional specification

Traces: BR-020..BR-028, BR-027 (offline)

## 1. Sessions

**FR-201** A cashier opens a **drawer session** on a counter with a counted opening float. Only one open session per counter; a session belongs to one cashier but supervisors may take over with an audited handover.

**FR-202** Mid-session cash pickups ("safe drops") and cash additions are recorded as documents affecting expected drawer cash.

**FR-203** Closing requires a denomination-wise count; the system computes expected vs counted, records the variance, posts cash short/over to the GL, and prints the drawer report. Variance beyond a threshold requires manager approval and a reason.

## 2. Billing

**FR-210** Item entry by barcode scan, code, name (prefix + fuzzy), or hotkey grid for fast-moving items. Lookup returns within 150 ms p95 (NFR-001).

**FR-211** Per line: variant, batch (auto per FEFO, overridable with permission), quantity (UOM-aware, decimal for loose goods), rate (from price resolution), discount (amount or %), tax components, amount. Rate override requires `pos.line.rateOverride` and is audited.

**FR-212** Bill-level discount, round-off (configurable nearest 1/0.50/0.10), and optional charges (delivery, packing) as configured charge lines mapped to GL accounts.

**FR-213** Hold and recall: unlimited parked bills per counter with a label (customer name/token); parked bills are local-only and survive an application restart; a parked bill older than a configurable age raises a reminder at day close.

**FR-214** Multiple simultaneous bills per counter (tabs) for queue-busting.

**FR-215** Quantity, line removal and bill cancellation before finalisation are free actions for the cashier; after finalisation only void (with permission and reason) or a sales return applies.

## 3. Customer and loyalty

**FR-220** Customer lookup by phone (primary), name or loyalty card; creating a customer at the counter needs only phone + name. Duplicate detection by phone HMAC.

**FR-221** Credit sale requires the customer to have a credit account; the system enforces credit limit and overdue rules (block / warn / allow-with-approval, configurable).

**FR-222** Loyalty: accrual per the `LOYALTY_ACCRUAL` decision; redemption as a tender type with min-balance and max-percent-of-bill rules; points ledger visible to the cashier.

## 4. Tenders

**FR-230** Tender types: cash, card, UPI/QR, wallet, cheque, credit (on account), gift voucher, loyalty points, store credit (from returns). Multiple tenders per bill; the bill cannot finalise until tendered = grand total.

**FR-231** Cash tender computes change and suggests denominations; card/UPI capture an optional reference and (with a payment plugin) an authorisation result.

**FR-232** Tender types are configurable per store with GL account mapping, allowed roles, and whether they open the cash drawer.

## 5. Returns and exchanges

**FR-240** Return with original bill: scan/enter the invoice number, select lines and quantities (≤ sold minus previously returned), choose restock or scrap per line, capture a reason.

**FR-241** Return without a bill requires `pos.return.withoutInvoice`, is limited by a value threshold, and always produces store credit rather than cash (configurable).

**FR-242** Exchange = return + new sale in one transaction, settling only the difference.

**FR-243** Outputs: credit note document, reversing stock movements (restocked items re-enter their original batch), reversing GL entries, refund tender (original method, cash, or store credit).

## 6. Offline behaviour

**FR-250** All of §§1–5 function with no server. The client shows "Local mode" within 2 s of connectivity loss and returns to "Online" automatically.

**FR-251** Invoice numbers come from the counter's own series (ADR-006); no server round-trip is needed.

**FR-252** Data unavailable locally is degraded gracefully: cross-store stock shows "unknown", loyalty balance uses the last synced value with a staleness marker and a configurable cap on offline redemption, credit-limit checks use the last synced exposure and may be configured to block credit sales offline.

**FR-253** On reconnect, documents push automatically; the pending-change count is visible; nothing is deleted locally until the server acknowledges.

## 7. Printing

**FR-260** Invoice prints to the counter's receipt profile; reprint requires `pos.invoice.reprint` and is audited; duplicates are marked "DUPLICATE". Email/SMS of the invoice is queued via the notification service. Printing failure never blocks finalisation (BR-024, design §5).

## 8. Performance and resilience

**FR-270** Finalisation (persist + post + queue print) ≤ 1.5 s p95 (NFR-002). Printing and notification are post-commit and asynchronous.

**FR-271** Abrupt power loss at any point must leave either a fully finalised invoice or no invoice — never a partial one (NFR-008). Parked carts may be lost only if they were never saved; saved parks survive.

## 9. Acceptance criteria (samples)

| ID | Given | When | Then |
| --- | --- | --- | --- |
| AC-201 | Cashier has a 10% discount limit | A 15% line discount is entered | Approval strip appears; manager credentials authorise it; approval is audited with both user ids |
| AC-202 | Server is unplugged mid-bill | The bill is finalised | Invoice is created with the counter series; "Local mode" is shown; after reconnect the invoice appears on the server exactly once |
| AC-203 | A bill was paid 500 cash + 300 UPI | The customer returns one item worth 200 | Credit note issues a 200 refund in the chosen method; GL and stock reverse correctly |
| AC-204 | Two counters bill concurrently offline for 8 hours | Both reconnect | No duplicate invoice numbers; trial balance balances; stock matches movement replay |
| AC-205 | Printer is out of paper | A bill is finalised | Finalisation succeeds; print job shows as failed with retry; reprint after fixing paper produces the same invoice |
| AC-206 | Drawer expected 12,500, counted 12,300 | Session closes | Variance −200 posted to cash short; manager approval required; day-close SMS sent to owner |
