# Practice card — Author, simulate and activate a business rule

**When**: a pricing, discount, tax, approval, loyalty or reorder policy changes.
**Prerequisites**: SKL-014/2. Read `rules-engine-design.md`.
**Never**: put ledger-affecting logic (posting, balancing, numbering, period locks) in a rule.

## Steps

1. **Write the policy in one sentence** in business language and get the owner/accountant to confirm it. Ambiguity here becomes a wrong invoice later.
2. **Pick the decision point** (`PRICE_RESOLUTION`, `DISCOUNT_ELIGIBILITY`, `TAX_DETERMINATION`, `APPROVAL_ROUTING`, `LOYALTY_ACCRUAL`, `REORDER_SUGGESTION`, `NEAR_EXPIRY_ACTION`, `VALIDATION_EXTRA`). If none fits, stop — a new decision point is a code change plus an ADR.
3. **Check the fact model** for that decision point in the rule editor's fact browser. If the data you need is not a fact, you cannot fetch it from a rule (rules are pure) — request a fact-model extension instead.
4. **Prefer a template.** The template library covers percent-off-by-category-and-date, tiered-by-amount, customer-group pricing, quantity slabs, exemption-by-customer-type, threshold approvals. Free DRL is for cases templates cannot express.
5. **Write the rule** in a new draft version of the package:
   ```
   rule "PAINT-CONTRACTOR-SLAB"
     agenda-group "DISCOUNT_ELIGIBILITY"
     salience 40
   when
     $f : DiscountFacts( customer.groupCode == "CONTRACTOR", cartNet >= 25000 )
     $d : DiscountDecision()
   then
     $d.applyBillPercent(7, "Contractor slab ≥ 25k");
   end
   ```
   Rules must: set a reason string, avoid `update()` loops (`no-loop true`), keep salience inside the documented range, and never call services.
6. **Add fixtures**: at least one case that fires, one that does not, and one boundary case (exactly 25,000 above).
7. **Simulate** against the fixture set *and* the recorded real-transaction corpus. Read the diff: how many invoices change, total revenue/tax/discount impact, worst-case per-invoice change. Unexpected rows mean the condition is too broad.
8. **Submit for approval**. A second person activates (four-eyes). Attach the simulation diff to the approval.
9. **Activate**, then verify on a real counter within the hour: bill one representative item and check the price trail shows your rule.
10. **Rollback** is one click to the previous version if the trail or the numbers look wrong — do that first, investigate second.

## Common mistakes

| Mistake | Consequence | Avoidance |
| --- | --- | --- |
| Overlapping rules without salience discipline | Non-deterministic discount | Assign salience deliberately; simulation diff shows the surprise |
| Conditions on mutable objects | Loops, timeouts | Facts are immutable records; `no-loop true` |
| Forgetting the date window | Promotion runs forever | Templates force validity dates |
| Rule needs data not in facts | Temptation to call a service | Request a fact-model change through the rules engineer |
| Testing only the happy case | Boundary bugs at exactly the threshold | Mandatory boundary fixture |
