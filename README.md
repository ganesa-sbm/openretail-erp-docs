# OpenRetail ERP — Documentation Set

Inventory & Accounting ERP for small/medium retail (departmental stores, textiles, hardware, paint, light processing/manufacturing). Offline-first JavaFX desktop client, optional LAN/web access, hybrid sync, optional cloud.

Working product name used throughout these documents: **OpenRetail ERP** (rename freely; the name appears only in prose, not in identifiers).

## Document map

| Area | Path | Contents |
| --- | --- | --- |
| Overview | `docs/00-overview/` | Glossary, document conventions, traceability matrix |
| Business requirements | `docs/01-brd/` | BRD, stakeholders, business processes, non-functional requirements, compliance |
| Architecture | `docs/02-architecture/` | System, module, deployment, data, security, integration, sync architecture, ADRs |
| Design | `docs/03-design/` | Domain model, UI design, payload-driven UI, report designer, rules engine, plugin framework, notification service, analytics |
| Specifications | `docs/04-specifications/` | Functional specs per module, API spec, database spec, UI payload schema, plugin SPI, test spec |
| Skills | `docs/05-skills/` | Role/skill matrix, per-role skill profiles, onboarding and training plan, hiring guide, and practice cards for recurring engineering tasks |
| Tasks | `docs/06-tasks/` | 92 individual task documents (TASK-001…TASK-092) with scope, notes, acceptance criteria and traces; `docs/06-tasks/README.md` holds the index, critical path and MVP cut |

## Identifier conventions

- `BR-nnn` business requirement
- `FR-mmm` functional requirement
- `NFR-nnn` non-functional requirement
- `ADR-nnn` architecture decision record
- `TASK-nnn` implementable task
- `SKL-nnn` skill

Traceability: `docs/00-overview/traceability-matrix.md` links BR → FR → TASK.

## Reading order

1. `docs/01-brd/BRD.md`
2. `docs/02-architecture/system-architecture.md`
3. `docs/03-design/domain-model.md`
4. `docs/04-specifications/` (module you are implementing)
5. `docs/06-tasks/README.md`, then the individual tasks for the current phase

Before doing a recurring piece of work (new module, rule, report, plugin, migration, layout, sync conflict, support call), read the matching practice card in `docs/05-skills/how-to-*.md`.
