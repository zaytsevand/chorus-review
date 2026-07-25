# Round 2 becomes a derive step

Design record for the Phase-2 change in `chorus-review` and the Stage-3
corroboration threshold in `chorus-core/GATE-PRIMITIVE.md`. The skill files carry
the rules; this file carries the reasoning and the evidence.

## Evidence

Measured on a production chorus corpus: 50 rounds, 2,998 findings, ten lenses,
nine weeks. Embeddings with mean-centering, a within-round permutation null, and
cluster bootstrap over rounds. Diversity by Vendi score.

| observation | number |
|---|--:|
| finding ids first appearing in Round 2 | 6.4% |
| Round-2 text with no close Round-1 match | 76% |
| change rate, findings with 4+ independent witnesses | 0% |
| share of all outcome changes on single-witness findings | 71% |
| rounds recording no band change at all | 66% |
| Round-2 share of everything the panel writes | 53% |
| code lines cited by exactly one lens | 79% |
| effective distinct findings, ten lenses vs ten undifferentiated reviewers | 75% vs 45% |

## What the numbers say

**Round 2 does not discover.** Only 6.4% of its finding ids are new. But it is
not restatement either: 76% of Round-2 text has no close Round-1 match, and its
nearest-neighbour similarity to Round 1 (0.30) is below Round 1's own internal
baseline (0.40). The phase generates real reasoning, bounded by construction to
the finding set Round 1 produced. Every one of the five reaction questions
pointed backwards.

**Voting on corroborated findings buys nothing.** Findings with four or more
independent witnesses changed band 0% of the time. 71% of all outcome changes
landed on single-witness findings. Adjudication pays only where nothing
corroborates, which is what the Stage-3 corroboration threshold encodes.

**The partition is the asset.** 79% of cited lines are touched by exactly one
lens, and the panel produces 75% effective distinct findings against 45% for ten
undifferentiated reviewers on the same material. When one lens reports a defect,
that fact often implies something in another lens's territory that the other lens
had no reason to examine. The old Phase 2 asked whether lenses agreed. It never
asked what a finding meant for the ground another lens owns.

## Why the memory rule, and not a rule on Round 2

Cross-lens coupling is driven by context contents
([arXiv:2607.01600](https://arxiv.org/abs/2607.01600)), so it dies with the
context window unless it is written down. Round 2 has no discovery value to
protect, so contamination inside the round costs nothing. The exposure is Round 1
of later rounds, reached only through persisted memory.

Hence the territory-provenance obligation sits on the findings-to-memory write
surface in `CONDUCTOR.md`, beside the secret pre-filter, and Round 2 reads
without restriction.

## Watch list

The change pushes on the partition, so it has to be observable. Per round:

| metric | adopt if | revert if |
|---|---|---|
| derive yield (new `D` findings) | > 0.5x Round-1 count | near zero |
| partition, Round-1 output only | holds near 79% | below 70% |
| cross-lens similarity, Round-1 output only | flat or falling | rises materially |
| memory leakage (entries naming another lens's finding) | near zero | rises |
| adjudication load | falls sharply | unchanged |

One signal already present in the source corpus: absolute cross-lens similarity
rose over the observed period (t ≈ −4 on separation) while each lens grew more
internally coherent. That may be topical concentration in later work rather than
drift, but it is the axis this change touches.

## Limits

Every measurement describes findings that were produced. A miss leaves no
artifact, so there is no false-negative measurement in the corpus and none of
this establishes whether the derive step improves coverage of ground the panel
never examined.

The source corpus also ran all ten lenses on one base model, the weakest
configuration for aggregation
([arXiv:2605.29800](https://arxiv.org/abs/2605.29800): nine judges across seven
model families supply roughly two votes of real independence). This change
reduces reliance on aggregation; it does not fix that.
