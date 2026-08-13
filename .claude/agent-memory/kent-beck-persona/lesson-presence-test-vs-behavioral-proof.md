---
name: lesson-presence-test-vs-behavioral-proof
description: When a test reads authored prose then asserts that prose is present, it pins the file against deletion, not the system against misbehavior — don't let it be framed as "behavioral"/"proven to fire"
metadata:
  type: feedback
---

A test that reads source text X and then asserts X is present can only fail if you
sabotage it mid-flight. It is a **presence/regex guard on authored prose** (same
shape as a residence grep), NOT a behavioral proof. It has real value — catches a
maintainer deleting/weakening the authored text — but the framing must match.

**Why:** Surfaced at feature 015 Gate A (chorus-suite-fitness-harness). The GC5
"substrate guard" test (`harness-contract.md` C-H4, `research.md` R3) renames
`skill/chorus-core/` aside, then asserts the guard's message anchors are present in
the sibling `SKILL.md`. But hiding core doesn't touch SKILL.md, so the anchor
assertion passes identically core-present or core-hidden — the move-core ceremony
buys nothing for that half. The guard is prose to an agent; no loader reads it and
halts. SC-004 / US3 titled it "proven to fire, not merely written" — an over-claim
the contract can't cash.

**How to apply:** When you see a test that (a) reads a file and (b) asserts that
file's content is present, separate the two real intents: "authored-text-present"
(static, needs no environment manipulation) and any actual precondition/behavior
(test that directly). Reject GREEN/RED framing where only a boolean
(`fs.existsSync`) flips and nothing executes the guarded path — "execution halts
before Gate A" is unprovable if nothing in the test runs Gate A. Push to reword
success criteria to what's actually asserted. This is distinct from
[[lesson-parity-proofs-observable-behavior]] (relocated-code parity) but same root:
assert observable behavior, show a RED that can fail for the claimed reason.
