---
name: project-spec015-fc4-regex-canon-gap
description: Feature 015 FC4 secret-filter congruence gate — RESOLVED Gate A cycle 2; R4 deny-default regex broadened to match live chorus-sdlc canon + pinned GREEN-on-canon baseline
metadata:
  type: project
---

Feature 015 (suite-fitness-harness) adds FC4 — a congruence gate asserting the
findings→memory secret pre-filter's load-bearing anchors stay congruent across
three homes: `skill/chorus-core/CONDUCTOR.md`, `templates/CHORUS-PROJECT.template.md`,
`skill/chorus-sdlc/SKILL.md`. FC4 is the FR-010a hard precondition guarding
token/PII exfil into persona memory.

**Finding (Gate A, security lens):** research.md R4 proposes regexes. Tested against
live canon (2026-06-21):
- detector-class (credential cue + structured-private-facts cue): binds in ALL three homes. OK.
- audit-on-drop regex: binds in all three. OK.
- **deny-default regex** `/(drop|exclud).*unless|unless.*(pass|clear)|default.*drop|deny.default/i`:
  matches CONDUCTOR.md ("dropped unless it passes") and template ("dropped unless it passes")
  but produces ZERO match in chorus-sdlc/SKILL.md. chorus-sdlc expresses the same
  obligation as "deny-filter runs on every candidate fact before any record write" +
  "Matches are dropped and flagged" — semantically deny-default, but lexically absent
  the "unless"/"default-drop" phrasing the regex keys on.

**Why it matters:** the gate would fail RED on UNMODIFIED canon — a false positive on
the very text it blesses. R4 says "final regexes tuned in implementation against live
text; negative case validates they bind." But the negative case only proves a deleted
anchor goes RED; it does NOT prove GREEN holds on current text. Implementation must add
a GREEN-on-current-canon assertion, and either broaden the deny-default regex to cover
sdlc's "before any write"/"runs on every candidate" phrasing OR normalize the sdlc copy.

**Deeper risk:** congruence-by-regex measures lexical presence, not semantic congruence.
The three copies already diverge in wording (Clarif Q2 acknowledges). A regex loose
enough to match all three is loose enough to pass a defanged copy that keeps a matching
phrase. This is the "anchor present-but-defanged" hole the spec records as a known limit
— acceptable at small-team scale IF the limit is honestly stated AND the GREEN baseline
is pinned so drift from today's text is what trips it.

**RESOLUTION (Gate A cycle 2, 2026-06-21):** research.md R4 §"Gate A S1 correction"
broadened the deny-default regex with `/runs? on every candidate|before any (record )?write|dropped and flagged/i`,
verified against live chorus-sdlc SKILL.md §"Secret pre-filter first" (lines ~263-272).
spec.md Clarif (Session 2026-06-21 Gate A) + FR-005/SC-003 now mandate a pinned
GREEN-on-current-canon baseline so a future "tune until it passes" cannot loosen the
regex into vacuity. S1 CLEARED. The deeper lexical-not-semantic limit (S2) stands as a
recorded known limit (spec Edge Cases "anchor present-but-defanged") — accepted at scale.
FC6 meta-check (FR-013) closes the unregistered-4th-copy hole (was S3). Gate A cleared
from the security lens cycle 2.

**GATE B (2026-06-21):** S1's authoring-time guard carried correctly into tasks.md.
T007 ends "verify GREEN on unmodified canon today" + pins the live-sdlc wording the
deny-default regex must match; T011 re-confirms exit 0 on clean tree; T017 adds the
test-layer GREEN-on-canon regression lock (defense in depth). Richards' RB1 (🔴) claimed
the PASS-on-clean-tree acceptance was missing until T017 two phases later — VOTED
OVER-RATE: the acceptance is already T007's closing clause, S1 is guarded at authoring
time, RB1 is a 🟡 wording-tightening not a block. SB4 watch-item (exit 2 = non-pass)
already load-bearing in T012 (pass iff exit 0) / T013 / T015-16. No new 🔴 from security.

See [[feedback-contract-now-control-later-trap]].
