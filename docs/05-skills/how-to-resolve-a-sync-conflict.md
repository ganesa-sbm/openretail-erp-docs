# Practice card — Diagnose and resolve a sync conflict

**When**: a conflict record appears, a node stops converging, or two nodes disagree about a value.
**Prerequisites**: SKL-018/2, SKL-019/2. Read `sync-architecture.md`.

## Ground rules

Append-only facts (stock movements, journal lines, invoices, audit) **never** conflict — they merge. A conflict therefore always concerns **mutable master data** (products, prices, parties, settings, layouts, rules) or a **uniqueness** violation. If you see a "conflict" on a fact table, that is a bug: escalate rather than editing data.

## Triage

1. Open Admin → Sync → Conflicts. Each record carries: entity, key, both versions with node and timestamp, the policy that was applied, and the resulting winner.
2. Classify:
   | Symptom | Likely cause | Action |
   | --- | --- | --- |
   | Master row edited on two nodes | Normal concurrent edit | Apply policy; confirm or override with the correct value |
   | Same natural key created twice (barcode, party code) | Two nodes created it offline | Merge: keep one, remap references, retire the other with a redirect |
   | Node not converging, backlog growing | Poison record or clock skew | Inspect the failing change id in the outbox; check NTP |
   | Duplicate document numbers | Numbering misconfiguration | Check counter prefix uniqueness (ADR-006) — this is a configuration bug |
   | Balances differ between node and server | Missing movement batch, not a conflict | Run the reconciliation job and compare hashes |

## Resolution

3. For a normal master conflict, decide from the **business** truth, not the newer timestamp: the store's price list owner wins on price; the accountant wins on tax mapping; the node that physically holds the stock wins on location attributes.
4. Record the decision in the conflict record (mandatory note). The decision is audited and replicates to all nodes.
5. For natural-key duplicates, use the merge tool: it remaps foreign references inside one transaction and leaves an alias so old barcodes/documents still resolve. Never delete the loser row.
6. Re-run the reconciliation job for the affected entity and confirm hash equality across nodes.

## Prevention

- Keep master-data creation centralised where practical; counters create parties and items offline only when the workflow demands it.
- Use `requiredNode` policies for entities that should only be edited in one place (tax mapping, chart of accounts, rules).
- Keep clocks synced; nodes warn above the configured skew threshold.
- Any new replicated table must declare its conflict policy at creation (`how-to-add-a-module.md` step 9).

## Escalation

Escalate to the architect when: a fact table shows a conflict, a reconciliation hash mismatch persists after replay, or convergence stalls with no poison record identified. Capture a diagnostic bundle (outbox tail, sync log, node ids, clock offsets) before mutating anything.
