# Chorus per-phase escalation — design

**Date:** 2026-07-10
**Branch:** `chorus-per-phase-escalation`
**Status:** design approved, spec under review

## Problem

The chorus suite already specifies a disciplined way to put questions to the
operator — the **sessioned operator interview** (batched, ≤5 questions/session,
gates-first, re-entrant, degradation summary). But it is wired into exactly
**one** place: Phase 0.7, the exploratory phase, which runs once between RSVP and
Round 1.

Every phase *after* 0.7 — Round 1 findings, cross-evaluation, conflict framing,
ranking, synthesis (and, in the SDLC mode, gates A/B/C) — has **no defined path**
for a question that surfaces mid-procedure. When a lens discovers, while
authoring, that it needs an answer it did not have at 0.7, the current design
absorbs the question into an S10 "conditional-on-stated-assumption" finding, or
the conductor triages it away. In practice observed on PR #798 (spec 143), the
exploratory phase was skipped entirely and the run went RSVP → Round 1 directly,
so **no escalation reached the operator at all** — a live write-path ambiguity
(Evans' F3) surfaced as a cold-read Round-1 finding instead of a pre-authoring
interview question.

The conductor's own slippage table already forbids *"this question can wait"* as a
disguised decision — but only names it under 0.7 interview triage. The rule is
right; its reach is too narrow.

## Goal

Generalize the escalation from **one phase** to **every phase**, in **both
modes**, without changing the interview's behavior and without turning a review
into an interrogation: *if a question survives the existing filter at any phase,
it gets asked at that phase's gate.*

## Approved decisions

1. **Ask bar — same 0.7 filter, per phase.** A question surfacing mid-procedure
   runs the *exact* exploratory-phase discipline: repo-answers-first → classify
   `[gate]` (cannot review honestly without) vs gap-question → dedupe against
   everything already answered this run. What survives is asked. No new machinery,
   no firehose.
2. **Ask timing — at each phase gate.** Questions raised during phase *N* are
   collected and run as one sessioned interview at phase *N*'s gate, before phase
   *N+1* opens. Mirrors 0.7, which is itself a boundary interview before Round 1.
   A `[gate]` need discovered mid-phase is authored conditional-on-assumption
   (existing S10) and resolved at the boundary; if the answer differs, the
   dependent finding re-prices. So it is **always asked**, at the next gate, never
   swallowed.
3. **Mode scope — both modes, defined in core.** The mechanic lands in
   `chorus-core` as a phase-independent primitive; `chorus-review` wires it into
   Phases 0–5 and `chorus-sdlc` into gates A/B/C. Both siblings already compose
   the exploratory phase and conductor from core, so this matches the existing
   single-definition-in-core / siblings-reference architecture.
4. **Enforcement — full teeth** (invariant + ledger), per the recommendation the
   operator approved with "let's try."

## Design

### 1. Extract the interview into a phase-independent primitive

The sessioned-interview mechanic currently lives inside
`chorus-core/EXPLORATORY-PHASE.md` (its "Sessioned interview" section) and is
described as if it belongs to that phase. The mechanic is already phase-agnostic;
only its wiring is single-site.

**Change:** move the mechanic into a new single-source file
`chorus-core/OPERATOR-INTERVIEW.md` — the canonical definition of "how the
orchestrator puts a batch of surviving questions to the operator." It carries,
unchanged:

- gates-first ordering (unmet `[gate]` needs lead session 1);
- sessions of ≤5 questions, re-entrant (defer/resume, persisted session state);
- operator-paced budget control;
- educational preamble (full first session, brief reminder on resume);
- the **degradation summary** when the operator leaves before completion;
- the bounded-question-pool guarantee (repo-answers-first + dedupe run first).

`EXPLORATORY-PHASE.md` step 7 stops restating the mechanic and **references**
`OPERATOR-INTERVIEW.md` — exactly as the siblings reference `EXPLORATORY-PHASE.md`
today. This is the suite's own DRY rule applied one level up: one definition, all
callers point at it, the two modes cannot drift.

**No behavior change** to the interview itself. This section only separates *what
the interview is* from *when it fires*, so more callers can fire it.

### 2. The escalation checkpoint at every phase gate

A new phase-independent rule in the conductor discipline (`chorus-core/CONDUCTOR.md`,
so both modes inherit it): **every phase gate is an escalation checkpoint.**

Procedure at any phase *N*:

1. During phase *N*, a seated lens may surface a question (typically while
   authoring, reacting, or arbitrating).
2. The question runs through the **filter** (§ approved decision 1): repo-answers-
   first → classify `[gate]` / gap-question → dedupe against the run's
   already-answered set.
3. What survives is collected across all lenses active in the phase and run as
   **one `OPERATOR-INTERVIEW.md` session** at phase *N*'s gate, before phase *N+1*
   opens.
4. A `[gate]` need surfaced mid-phase → the lens authors
   **conditional-on-stated-assumption** (existing S10); the gate is resolved at the
   boundary interview; a differing answer re-prices the dependent finding.
5. **Deferral stays the operator's call.** If the operator defers, the phase's
   verdict carries the existing **degradation summary** — reused verbatim, now
   per-phase: how many gaps remain and which findings are affected.

**Coexistence with the decision primitive.** This checkpoint routes operator
**facts / gate answers** (information the lens needs to review honestly). The
decision primitive's `cycle==3` escalation routes operator **decisions**
(scope, waiver). They are distinct and coexist; this design does **not** merge
them. A phase gate may run both: the interview (facts) feeds the phase; the
decision primitive (decisions) bands the phase's operator-facing choices.

**Relationship to Phase 0.7.** Phase 0.7 is unchanged — it remains the canonical
first interview, run before Round 1. This design makes it **one checkpoint among
several**: every other phase gate gains the same escalation checkpoint, all
referencing the one `OPERATOR-INTERVIEW.md` mechanic. A later checkpoint that has
no surviving questions runs empty and advances; the ledger records `raised: 0`.

**Wiring.** `chorus-review/INTEGRATION-LAYER.md` marks Phases 0–5 as escalation
checkpoints in their per-phase pre/post-conditions (0.7 already is one).
`chorus-sdlc/SKILL.md` marks gates A/B/C likewise. Neither restates the mechanic;
both reference the core rule.

### 3. Enforcement teeth

Prose alone re-admits swallowing. Two mechanical guards:

1. **Invariant `I10`** (extend the single `I1–I9` catalog in
   `chorus-core/CONDUCTOR.md`):

   > **I10.** No phase advances while a question raised during it has survived the
   > filter (repo-answers-first → classify → dedupe) and is neither **asked** at
   > the phase gate nor **deferred-with-degradation** by the operator. Every phase
   > gate runs the escalation checkpoint. A surviving question silently carried
   > past a gate is a skipped gate — the cascade has been bypassed.

   And update the slippage-table row **"This question can wait"** so it binds at
   *every* phase gate, not only 0.7 interview triage. Its owner is unchanged: the
   operator, deferral recorded with its degradation.

2. **Escalation ledger.** The conductor records, per phase, an arithmetic line —
   its "you count" discipline:

   > `questions raised: R · asked: A · deferred: D · answered: N` (per phase)

   auditable exactly like the gate ledger. The invariant is the rule; the ledger
   is the evidence it held. A phase whose ledger shows `raised > asked + deferred`
   has swallowed a question and violated I10.

## Files touched

| File | Change |
|---|---|
| `chorus-core/OPERATOR-INTERVIEW.md` | **New.** Canonical sessioned-interview mechanic (moved from EXPLORATORY-PHASE §Sessioned interview), unchanged behavior. |
| `chorus-core/EXPLORATORY-PHASE.md` | Step 7 references `OPERATOR-INTERVIEW.md`; the "Sessioned interview" section body is replaced by a pointer. |
| `chorus-core/CONDUCTOR.md` | New I10 invariant; slippage-table "this question can wait" row widened to every phase gate; escalation-checkpoint rule + escalation-ledger line added to conductor discipline. |
| `chorus-review/INTEGRATION-LAYER.md` | Phases 0–5 pre/post-conditions mark each as an escalation checkpoint (reference core, no restatement). |
| `chorus-sdlc/SKILL.md` | Gates A/B/C marked as escalation checkpoints; note coexistence with the existing `cycle==3` decision-primitive escalation. |

## Non-goals (YAGNI)

- **No change to the interview's behavior** — sessions, gates-first, re-entrancy,
  degradation summary, educational preamble all carry over verbatim.
- **No merge** of the fact-interview and the decision-primitive escalation.
- **No new persona-facing surface** — lenses still raise questions the same way
  (gap-questions / `[gate]` marks); only the orchestrator's firing points widen.
- **No immediate-interrupt mode** — batching at the phase gate is the only timing.

## Provenance

Extends `specs/004-advisor-exploratory-phase/` (the exploratory phase and its
sessioned interview). Motivated by the PR #798 / spec 143 chorus run, which
skipped Phase 0.7 and surfaced a write-path gate as a cold-read finding instead
of an interview question.
