# Training and onboarding plan

## 1. Engineer onboarding (first two weeks)

| Day | Activity | Output |
| --- | --- | --- |
| 1 | Read BRD + system architecture; install the stack; run the app standalone | Working local install, first invoice created |
| 2 | Walk the domain model and the posting-rule matrix with the accounting specialist | Hand-derived journal entries for 3 scenarios, checked |
| 3 | Practice card: add a field to a screen via layout payload | Merged PR changing a layout with a passing validation |
| 4 | Practice card: write a migration + repository + use case | Merged PR with integration test |
| 5 | Practice card: author and simulate a Drools rule | Rule activated in a sandbox tenant with a simulation diff |
| 6–7 | Practice card: add a report to the designer | Report template with a named data source and a performance check |
| 8 | Sync deep dive: run the simulator, break it deliberately | Explanation of a conflict record they produced |
| 9 | Security: RBAC, audit, encryption; add a permission end-to-end | Permission enforced and audited, with a test |
| 10 | Shadow a support triage; read three real log bundles | Triage notes on a real issue |

Completion requires a merged change in each of: domain, persistence, UI payload, rules, reporting, tests.

## 2. Skill-gap programme

For each critical skill with fewer than two people at the target level (skill matrix §4), the plan is: nominate a primary and a secondary, allocate 4 hours/week, use the pairing and kata list below, and verify with a demonstrable artefact — not a certificate.

| Skill | Kata / exercise | Verification artefact |
| --- | --- | --- |
| SKL-018 Distributed | Implement a two-node replicated counter with idempotent delivery and a conflict policy | Simulator scenario + write-up |
| SKL-014 Drools | Convert three real discount policies into rules with fixtures | Simulation diff report |
| SKL-015 Accounting | Close a synthetic financial year end to end | Balanced statements + snapshot hash |
| SKL-009 JavaFX | Build a virtualised 10k-row editable grid meeting the latency budget | TestFX benchmark |
| SKL-007 PostgreSQL | Tune three slow report queries with EXPLAIN | Before/after plans |
| SKL-020 Crypto | Implement per-tenant key wrapping with rotation | Design note + rotation test |
| SKL-023 Peripherals | Drive an ESC/POS printer and a serial scale from raw bytes | Working adapter + device log |

## 3. Domain training for engineers (mandatory, 6 sessions × 90 min)

1. Double entry from first principles; why the ledger is append-only here.
2. Retail inventory: batches, FEFO, valuation methods, why on-hand is derived.
3. Tax mechanics and why the trail matters in disputes.
4. How a shop actually works: a day at a counter (field visit or recording).
5. Offline reality: what breaks in a shop and what the software must do about it.
6. Fraud patterns and the controls that catch them.

## 4. Implementation-consultant certification

Three levels, each requiring supervised practice:

| Level | Requirements |
| --- | --- |
| Associate | Install and configure a single-counter shop; migrate items and opening balances; train a cashier; supervised go-live |
| Consultant | Multi-counter LAN go-live; chart and tax configuration from scratch; layout and report tailoring; rule authoring from a policy statement; two unsupervised go-lives with a clean reconciliation |
| Senior | Vertical rollout (textile/paint/processing) including plugin configuration; data migration from a legacy system with a reconciliation report; trains other consultants |

## 5. End-user training (delivered to shops)

| Audience | Duration | Content | Materials |
| --- | --- | --- | --- |
| Cashier | 1 h + 1 h practice | Login, billing, holds, tenders, returns, drawer open/close, printer jams, local mode | One-page keyboard card, 5 short videos |
| Storekeeper | 2 h | Receipts, batches/expiry, transfers, stock-take, labels | Checklist + practice dataset |
| Manager | 3 h | Approvals, pricing and promotions, reports, exceptions, day close | Exception-report walkthrough |
| Accountant | 3 h | Chart, journals, party ledgers, reconciliation, period and year close, tax exports | Close checklist |
| Owner | 1 h | Dashboards, alerts, backups, what to check daily/weekly/monthly | Daily 5-minute routine card |

Training effectiveness is measured at 30 days post-go-live: bills per hour, discount-override rate, stock variance at first stock-take, support tickets per week.

## 6. Knowledge retention

Every practice card is a living document updated by whoever last hit a gap in it. Quarterly: rotate secondary owners for critical skills; run a "restore drill" and a "shop day" rehearsal with rotating participants so the knowledge is not concentrated.
