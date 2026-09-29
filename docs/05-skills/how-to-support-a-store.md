# Practice card — Support and operate a live store

**When**: a shop reports a problem, or during routine operations.
**Prerequisites**: SKL-034/2, SKL-027/2. Read `deployment-architecture.md` §§7–10.
**First principle**: billing must continue. Restore the ability to sell first; diagnose second.

## Triage ladder (in order)

1. **Can they bill?** If not: switch the counter to local mode, or fall back to the standalone/terminal client. Confirm sales are being recorded locally before investigating anything.
2. **Collect the diagnostic bundle** (Help → Generate diagnostic bundle): version, node id/role, last sync token and time, recent logs, health metrics, DB size, pending outbox count, clock offset. Never ask a shopkeeper to read logs aloud.
3. **Classify**:
   | Symptom | Check | Typical fix |
   | --- | --- | --- |
   | Slow POS | Local DB size, projection lag, disk free | Rebuild projections, run vacuum/analyse, free disk |
   | Nothing prints | Printer profile, spooler queue, cable/power | Reset queue, reprint from the print history (audited) |
   | Scanner types wrong characters | Keyboard layout / scanner suffix config | Reconfigure scanner suffix |
   | "Local mode" stuck | Network, server health, certificate expiry | Fix network/cert; sync drains automatically |
   | Sync backlog growing | Poison record, clock skew, server disk | See `how-to-resolve-a-sync-conflict.md` |
   | Totals look wrong | Rule activation, price trail, period lock | Read the price/posting trail before touching data |
   | Disk full | Backups, logs, old projections | Rotate logs, move backups, never delete transaction data |
4. **Never edit the database directly** to fix business data. Use reversals and the supported correction workflows; direct edits break hash chains and node convergence, and destroy the audit's value.

## Routine operations

| Cadence | Task |
| --- | --- |
| Daily | Verify backup completed and its checksum; check day-close was done at every counter; review failed notifications |
| Weekly | Review sync backlog and conflicts; disk and DB growth; failed logins and override rates |
| Monthly | Restore drill on a scratch machine from a real backup; certificate expiry check; patch level review |
| Quarterly | Upgrade rehearsal on a clone of a real shop database; review slowest reports; review open conflict records |

## Escalation

Escalate with the diagnostic bundle attached when: data integrity is in question, a reconciliation mismatch persists, an upgrade fails mid-migration, or a security event is suspected. For a suspected security event, preserve the audit log and the bundle before any remediation.

## Communication

Tell the shop three things: whether they can keep selling, what you are doing, and when you will next update them. Shopkeepers tolerate faults; they do not tolerate silence during business hours.
