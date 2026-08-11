# Addendum: Round 2 becomes a derive step, not a vote

Status: shipped. This document is the rationale, the measurements, and the
revert criteria; the behaviour itself lives in the skill files below and is loaded
from there. It is kept because a rule whose evidence is not written down cannot be
falsified later — the same bar the suite puts on every finding it registers.

| change | implemented in |
|---|---|
| triage + derive replace the five reaction questions | `skill/chorus-review/SKILL.md`, Phase 2, dispatch item 3 |
| severity call restricted to under-threshold findings | `skill/chorus-review/SKILL.md`, Phase 2, dispatch item 5 |
| W≥3 corroboration threshold, auto-CONFIRM | `skill/chorus-core/GATE-PRIMITIVE.md`, Stage 3 |
| territory-provenance rule on the memory write | `skill/chorus-core/CONDUCTOR.md`, findings→memory contract |

## Why

Measured over 50 rounds and 2,998 findings from one project's accumulated review
corpus. The corpus and the analysis scripts named below live outside this repo, in
the author's separate analysis workspace; the numbers are reproduced here because
they are the whole argument:

| observation | number | consequence |
|---|--:|---|
| finding ids first appearing in Round 2 | 6.4% | Round 2 almost never discovers |
| Round-2 text with no close Round-1 match | 76% | but it is not restatement either |
| change rate, findings with 4+ independent witnesses | **0%** | voting on corroborated findings yields nothing |
| share of all outcome changes on single-witness findings | **71%** | adjudication pays only where nothing corroborates |
| rounds recording no band change at all | 66% | the phase runs whether or not it is needed |
| Round-2 share of everything the panel writes | 53% | at the highest cost of any phase |
| code lines cited by exactly one lens | 79% | every lens holds ground the others never examined |

Round 2 currently spends the majority of the panel's output generating genuinely
new reasoning that is bounded, by construction, to the finding set Round 1
produced. The five reaction questions all point backwards at other lenses' work.
Nothing asks a lens to look somewhere new.

The 79% partition is the asset this wastes. When Security reports a defect in the
auth path, that fact may imply something in Cooper's territory that Cooper had no
reason to examine. Today the panel asks Cooper whether he agrees with Security.
It never asks what Security's finding means for the ground Cooper owns.

## What changes

Round 2 keeps its cost and changes its output. Two tasks replace the five
questions, and the vote narrows to where votes demonstrably move outcomes.

### Task 1: Triage (cheap, bounded)

For each finding you did not author, one call:

- **USABLE**: the finding is real and actionable as stated.
- **NOT USABLE**: the finding is wrong, out of scope for this change, or too
  vague to act on. Requires a `file:line` or a one-line reason.

That is the whole vote for corroborated findings. Severity adjudication
(PRIORITIZE / OVER-RATE) is requested **only** for findings with fewer than three
independent Round-1 witnesses, because findings above that threshold changed
band 0% of the time across the whole corpus. Findings at or above the threshold
are auto-CONFIRMED at their authored severity and skip adjudication entirely.

### Task 2: Derive (the new primary output)

> Given the findings other lenses reported, what does that imply **in your own
> territory** that you have not yet examined? Go and look. Report what you find.

Constraints, which are what keep this from collapsing the partition:

- A derived finding must be **in the deriving lens's own territory**, not a
  second opinion on someone else's. "Security found an unauthenticated write
  path, so I checked whether the same handler leaks the identifier into the UI
  copy" is a derive. "I agree with Security" is not.
- It must cite `file:line` for ground the lens actually examined this round. The
  Round-1 evidence rule applies unchanged.
- It enters the register as a new finding with a `D` prefix (`D1`, `D2`, …),
  carries its own severity, and is votable next round like any other finding.
- The lens states which finding it derived from, so the chain is auditable.

Word budget shifts accordingly: triage is a list, derive gets the prose.

## What does not change

These are the parts the measurements say are working. Do not touch them.

- Severity bands. Red findings are fixed 71% of the time, green 24%. The band
  sorts real outcomes and stays exactly as it is.
- Verbatim persona pull-quotes and the I6 refusal. The register keeps speaking in
  each lens's own words.
- The author never grades their own finding (S8).
- The evidence rule and the post-round evidence check.
- The deterministic tally and board-scaled threshold for the findings that still
  get adjudicated. Severity remains arithmetic over real votes, never the
  orchestrator's judgment (S9).
- RSVP self-selection. Abstention stays a first-class, recorded outcome.

## The risk this introduces, and where the guard actually belongs

The obvious worry is that deriving from peers means reading peers, and coupling
in multi-agent systems is driven by whatever enters the context window
([arXiv:2607.01600](https://arxiv.org/abs/2607.01600)). The panel's measured
strength is its partition: ten lenses produce roughly 75% effective distinct
findings against 45% for ten undifferentiated reviewers on the same specs.

But that worry does not apply to Round 2 itself. Round 2 contributes 6.4% of new
finding ids, so there is no discovery value there to protect. If contamination
were confined to the round in which it happened, it would die with the context
window and the trade would be free.

**The exposure is Round 1 of subsequent rounds, and the channel is memory.** Each
lens decides after a round what to persist, and that memory pre-loads its next
Round 1. A lens that writes "Security flagged an unauthenticated write path"
into its own memory has permanently imported another lens's territory. Repeated
weekly, the partition erodes in the phase where discovery actually happens.

So the constraint belongs on the **memory write**, not on the derive step:

- A lens MAY persist conclusions about its own territory, including conclusions
  it reached by deriving from another lens's finding.
- A lens MAY persist that a cross-lens implication exists, as a pointer.
- A lens MUST NOT persist another lens's findings as its own knowledge.

Round 2 can then read freely. The boundary holds at the point where the effect
would otherwise become permanent.

Two things make this more than theoretical. Absolute cross-lens similarity has
already been **rising** (time coefficient t ≈ −4 on separation) even as each lens
grew more internally coherent. And during the entire measured period memory was
nearly absent: a mean of 1.4 files existed at round time. There are 334 now, so
the contamination channel is far stronger going forward than in any period these
numbers describe.

## Instrumentation (required, not optional)

The addendum is only adoptable if it can be reverted on evidence. Each round
records four numbers in the artifact:

| metric | definition | adopt if | revert if |
|---|---|---|---|
| derive yield | new `D` findings per round | > 0.5x Round-1 finding count | near zero |
| partition, Round 1 only | share of cited lines touched by exactly one lens | holds at ~79% | falls below 70% |
| cross-lens similarity, Round 1 only | mean cosine between lenses' findings, same round | flat or falling | rises materially |
| memory leakage | share of a lens's new memory entries naming another lens's finding | near zero | rises |
| adjudication load | findings sent to PRIORITIZE/OVER-RATE | falls sharply | unchanged |

Partition and similarity are measured on **Round 1 output only**. Round-2 text is
expected to reference other lenses and would swamp the signal; the question is
whether next week's independent discovery has narrowed.

Re-run the partition and similarity analysis against the review corpus after ten
rounds under the new scheme and compare with the pre-change baselines. Any project
running the suite can compute these from its own artifacts: every number above is
derived from the finding registers and their `file:line` citations, which the
artifact format already carries.

## Open question this does not answer

Every measurement behind this addendum describes findings that were produced. A
miss leaves no artifact, so there is no false-negative measurement anywhere in
the corpus. Whether the derive step improves *coverage of what the panel never
looked at* cannot be established from review logs alone, and would need seeded
defects or post-hoc incident attribution.

Separately, all ten lenses run on one base model, which is the weakest available
configuration for the aggregation step
([arXiv:2605.29800](https://arxiv.org/abs/2605.29800): nine judges across seven
model families supply roughly two votes of real independence). This addendum
reduces how much the panel leans on aggregation, but it does not fix that.
