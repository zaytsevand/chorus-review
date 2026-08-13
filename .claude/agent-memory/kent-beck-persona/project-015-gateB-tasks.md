---
name: project-015-gateB-tasks
description: feature 015 Gate B (plan/tasks) Beck test-design lens; four 🟡 RED-realness tightenings, JOIN non-blocking
metadata:
  type: project
---

Feature 015 (suite-fitness-harness) Gate B, TDD/SIMPLE-DESIGN lens. Voted JOIN, 🟡 (non-blocking). Gate A had passed; this feature IS a test harness so test-shape is the dominant axis.

**The four findings (all "make RED real before GREEN"):**
- BB1/T014 — task offers "a test OR a documented quickstart step" for the FC1 RED→GREEN proof. The quickstart-step option IS the manual ritual Gate A B4 rejected; contradicts its own "automated, not a manual ritual" clause. Demand a Vitest case.
- BB2/T019 — FC6 meta-check negative can go GREEN by finding nothing (false green) if fixture lands outside FC6's grep scope or cue-phrasing drifts. Must show FC6 silent-without-fixture then RED-with; source fixture phrasing from T009's documented cue set, not hand-typed. (Links [[lesson-presence-test-vs-behavioral-proof]].)
- BB3/T013 — smears 3 failure modes (infra/env-error/violation) + 2 FRs + a distinguishability assertion into one task. Split into separate it() blocks. Note possible duplication with T027 (SC-008 re-tested in Phase 7).
- BB4/T022 — self-heal abort-on-ambiguity (both .gc5-bak AND live core exist) is the one destructive side-effect on the real tree, and no task commissions a test for the abort branch. The branch that can corrupt the working tree needs cornering hardest.

**What was already good (folded from Gate A correctly):** T021 truth-in-labeling ("never assert execution halts", precondition not wish); T010/T024 FC7 seam with the GC5-read carve-out + a gauge that bites; T017 GREEN-on-canon baseline as anti-vacuity guard. MVP cut US1+US2 is the simplest thing that delivers value.

**Verified live (don't re-check blindly):** T007's FC4 GREEN-on-canon claim is SOUND — the chorus-sdlc anchor is phrased "deny-filter runs on every candidate fact before any record write … dropped and flagged" (skill/chorus-sdlc/SKILL.md ~L263-270), not literal "deny-default" token; T007 already mandates wording-tolerant regex. No day-one RED there. Current script has only FC1-FC3, exit 0/1 (no exit 2 yet) — Phase 2 adds FC4-FC7 + exit 2.
