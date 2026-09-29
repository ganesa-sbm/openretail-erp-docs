# TASK-084 — Localization, formatting and accessibility framework

| Field | Value |
| --- | --- |
| Phase | 2 |
| Module(s) | `erp.platform.ui` |
| Traces to | NFR-013, NFR-014, BR-072 |
| Depends on | TASK-057 |
| Size | M |
| Primary role | UI engineer |

## Goal

Let the software speak the shop's language and be usable by the people working there.

## Scope

**In:** Message bundles and a key discipline, locale-aware number/date/currency formatting, per-user locale, script/font support for Indic scripts, pluralisation, RTL readiness, keyboard-only operation, contrast and focus checks, screen-reader labels.

**Out:** Full translation of all locales (community contribution).

## Implementation notes

- Every user-visible string is a key; a build check fails on hard-coded literals in UI code.
- Formatting is locale-aware but money persistence is never locale-dependent.
- Accessibility checks run at layout publication and in CI, not as a manual audit at the end.

## Acceptance criteria

1. A pseudo-locale run reveals no hard-coded strings and no truncation in core screens.
2. Indic-script rendering is correct in FX, in printed documents and in PDFs (thermal printer fonts included).
3. Every core screen is fully keyboard-operable and passes the automated accessibility check.

## Definition of done

Code + unit tests; integration tests on PostgreSQL and MySQL where persistence is touched; permissions and audit events wired; offline/sync behaviour stated and tested; spec and traceability matrix updated; no architecture-check violations.
