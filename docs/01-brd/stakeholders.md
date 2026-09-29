# Stakeholders and personas

## Stakeholder register

| Stakeholder | Interest | Influence | Key concerns |
| --- | --- | --- | --- |
| Shop owner / proprietor | Profit visibility, cash control, low cost | Decision maker | Does it keep working when internet dies? Can I see today's profit? |
| Cashier | Fast, simple billing | User | Speed, few keystrokes, no blocking dialogs |
| Store manager | Stock accuracy, staff control | User/approver | Discount abuse, shrinkage, reorder timing |
| Accountant (often external) | Clean books, statutory exports | Approver | Trial balance integrity, period locking, tax returns |
| Auditor | Evidence trail | Reviewer | Immutable audit log, no hard deletes |
| IT support / implementer | Installation, upgrades, backups | Operator | Single-command install, remote diagnostics |
| Supplier | Accurate POs and payments | External | Correct documents |
| Customer | Correct bill, quick service | External | Readable invoice, returns handled fast |
| Regulator / tax authority | Compliant records | External | Retention, sequence integrity, tax computation |

## Personas

### P1 — Ravi, owner of a 4-counter departmental store
Checks sales and cash daily on his phone, distrusts cloud-only tools after a previous vendor lock-in. Needs day-close summary, margin by category, and confidence that a power cut does not stop billing.

### P2 — Lakshmi, cashier
Bills 300 customers a day. Keyboard + scanner only; mouse is too slow. Needs hold/recall, split tender, quick customer phone capture, and no popup she cannot dismiss with Enter/Esc.

### P3 — Suresh, textile shop manager
Sells by size/colour matrix and by metre. Needs matrix entry for purchase and quick variant selection at billing, plus season-end markdown pricing.

### P4 — Anita, external accountant
Visits weekly. Needs to lock periods, post adjustment journals, export GST summaries, and reconcile bank statements without touching POS data.

### P5 — Mohan, paint dealer
Sells base + tint mixes, loose quantities from 20 L packs, and issues credit to contractors. Needs mixing recording, unit conversion, and customer credit limits.

### P6 — Deepa, support engineer for the implementer
Installs at 30 shops. Needs unattended install, config export/import, remote health view, and log bundles she can collect in one click.

## Stakeholder-to-requirement map (primary drivers)

| Persona | Primary requirements |
| --- | --- |
| P1 | BR-042, BR-062, BR-075, BR-077 |
| P2 | BR-020..BR-027 |
| P3 | BR-002, BR-011, BR-053 |
| P4 | BR-040..BR-047 |
| P5 | BR-012, BR-028, BR-053 |
| P6 | BR-050, BR-072, BR-075, BR-077 |
