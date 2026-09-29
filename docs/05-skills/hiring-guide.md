# Hiring guide

## 1. Principles

Hire for the two scarce things on this programme: **domain judgement** (accounting/inventory correctness) and **distributed-systems discipline** (offline convergence). Framework familiarity is trainable; these are not, quickly.

## 2. Screening signals

| Positive | Negative |
| --- | --- |
| Has shipped software that runs on customer premises with no internet | Only ever built stateless cloud CRUD |
| Uses `BigDecimal`/decimal types instinctively for money and can explain rounding | Reaches for `double`, or shrugs at rounding |
| Talks about invariants and tests them | Talks only about frameworks |
| Has debugged a production data-corruption incident and can describe the forensics | Never had to reconcile data |
| Reads EXPLAIN plans | Treats the database as a black box |
| Can explain a business rule to a shopkeeper | Cannot simplify |

## 3. Interview loop (engineers)

1. **Domain modelling (60 min)** — "Design the data and operations for a sale that can be partially returned, with batch-tracked stock and split tenders, on a counter that may be offline." Look for: append-only thinking, idempotency, numbering strategy, where the invariants live.
2. **Coding (90 min, take-home or pair)** — Implement a small inventory issue/receipt service with FEFO and a balanced posting, with tests. Grade on tests and invariants, not cleverness.
3. **Debugging (45 min)** — Given a failing sync simulation seed and logs, find the bug. Grade on method, not speed.
4. **Correctness conversation (45 min)** — Money rounding, concurrency on document numbering, migration safety on a long-offline node, what they would refuse to implement and why.
5. **Collaboration (30 min)** — Walk through a design document they wrote; explain a decision they later regretted.

## 4. Interview loop (domain specialist — accounting)

- Derive journal entries live for: split-tender sale with round-off; purchase with landed cost; sales return of one line from a multi-line invoice; year close with stock revaluation.
- Review a deliberately wrong posting-rule matrix and find the three errors.
- Explain to a "shopkeeper" (role-played) why their trial balance does not match their cash.

## 5. Interview loop (implementation consultant)

- Role-play requirement elicitation with a textile shop owner who "just wants billing".
- Plan a data migration from a legacy system with dirty item data and unbalanced opening balances; state what you would refuse to import.
- Train a "cashier" on returns in 10 minutes.

## 6. Scorecard

Rate 1–4 against the role's required skills (skill matrix §2). Hire when: no required skill below target −1, no red flags on correctness, and at least one skill at expert level that the team currently lacks. Two "meets, does not exceed anywhere" hires in a row is a signal to raise the bar rather than fill seats.

## 7. Onboarding commitment

Every hire gets the two-week onboarding in `training-plan.md` §1 with a named buddy; a hire who cannot complete it is a hiring-process problem, not a performance problem — review the loop.

## 8. Contracting and open-source considerations

Contributors must be able to license their work under the project's open-source licence; contractor agreements need explicit IP assignment or a CLA. Anyone touching cryptography, tax logic or the ledger must go through the correctness conversation regardless of seniority.
