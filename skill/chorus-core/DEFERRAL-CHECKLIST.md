# Deferral checklist (chorus-core)

Canonical checklist for **trust / reuse deferrals** in specs and Gate A corpora.
Consuming projects mirror this in their spec-template and project CHORUS-PROJECT
addendum (e.g. `.specify/templates/spec-template.md` and `docs/reviews/CHORUS-PROJECT.md`).

## When required

Complete **one row per deferred capability** when any of:

- An FR is marked **DEFERRED** or parked in a follow-up spec
- Cross-user reuse, trust promotion, or shared learnings are out of v1 scope
- Conscious trade-offs table defers machinery that would otherwise need schema or API surface

Gate A **blocks** incorporation when a DEFERRED item lacks a complete row.

## Columns (all required)

| Column | Question it answers |
|--------|---------------------|
| **ID** | Stable id (`D-001`, `FR-NNN`, …) |
| **Deferred capability** | What is explicitly not built in v1 |
| **Distinct actors** | Who is in scope vs out (per-user vs cross-user) |
| **Completeness predicate** | What must already ship for the deferral to be honest |
| **Trust boundary** | Where enforcement lives (server vs edge; isolation vs shared) |
| **Falsifiable un-defer instrument** | Metric/event that proves un-defer is earned — not operator judgment alone |
| **Follow-up spec** | Where deferred scope is preserved (follow-up spec NNN, BACKLOG, …) |
| **Beneficiary of deferral** | Who benefits from waiting: user, operator, or **build team** |

## Anti-theater rule

Do **not** add schema columns, OpenAPI fields, or entity tables whose **only**
purpose is reserved space for a **DEFERRED** parent FR. If deferred, use this
checklist and a follow-up spec — not dormant trust machinery in v1.

## User-value strip (F-UV)

When any deferral row or DEFERRED FR is present, Gate A requires a **user-value
strip** comparing the spec's named primary outcome to what v1 delivers after
deferrals:

| Field | Purpose |
|-------|---------|
| Named outcome | Primary outcome sentence from `## Outcome & Stage-1 proof` |
| v1 outcome | Honest user-visible outcome after deferrals |
| Dimensions | 3–5 user-visible benefits rated **Full / Partial / None** in v1 |
| Estimated value ratio | Approximate % of named outcome delivered in v1 |
| Threshold | Default **20%** for reuse / trust / network-effect outcomes |

If estimated value ratio **&lt; threshold** and the primary outcome sentence still
promises deferred value → **delivery theater** (🟠) unless the same PR: (a) revises
the primary outcome to match v1, (b) scopes the minimum viable slice into v1, or
(c) records an operator override with rationale. Cooper authors the F-UV finding
(`agents/alan-cooper-advisor.md`); see `DECISION-PRIMITIVE.md` catalog row 13.

## Seating (cross-user / reuse)

When the deferral row involves cross-user or reuse value:

- **Cooper** is **mandatory** at Gate A (no ABSTAIN)
- **Security-and-trust** is mandatory when trust enforcement is server-bound
- **Goldratt** must cite checklist columns; deferral without falsifiable instrument is 🔴-potential

## Incorporation

Gate A incorporation for a deferral finding MUST:

1. Add or complete the checklist row in `spec.md` (source of truth)
2. Remove trust-schema theater from `plan.md` / `data-model.md` if parent FR stays DEFERRED
3. Re-run Gate A with Cooper seated if cross-user/reuse

## Provenance

Value-first spec gate recommendations (2026-07).
