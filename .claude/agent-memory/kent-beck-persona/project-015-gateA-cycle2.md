---
name: project-015-gateA-cycle2
description: Feature 015 suite-fitness-harness Gate A cycle 2 (Beck lens) — B4/ADR-001 cleared, B1/B2 narrative overclaim still 🟡
metadata:
  type: project
---

Feature 015 (suite-fitness-harness), Gate A re-run cycle 2, TDD/SIMPLE-DESIGN lens. Dated 2026-06-21.

Outcome from Beck lens:
- B1/B2 STILL-🟡 (editorial, not structural): FR-006/SC-004/acceptance-scenario-2/research R3,R8 are correctly truth-labeled ("guard-text + halt-precondition", never "execution halts"). But narrative wrappers still oversell — spec.md US3 title "proven to fire" (L159), L44, L163-165, L175, L296 ("behavioral"/"halts"/"fires"). Reader meets overclaim before correction. Cheap fix: strike those words from the four narrative lines.
- B4 CLEARED: RED proof now an automated negative case (rename-hide core, assert anchors+precondition, try/finally restore + crash-safe pre-run self-heal). No longer manual inject-revert.
- ADR-001 CLEARED: withdrew PRIORITIZE vote on G3/G4. Operator's DevEx argument priced a real cost I under-weighted — GC5 is setup+multi-assertion+crash-safe teardown, not "one mv". TS thin wrapper, checks stay greppable in shell, proportionality now measurable ("any check logic in TS? must be no", enforced by FC7).
- No new 🔴.

**Why:** records that the standing critique (overclaim) survives only in prose, and that the shell-vs-TS argument resolved on DevEx grounds the lens accepted.
**How to apply:** if revisited, the only open Beck item is the narrative-prose overclaim; do not re-litigate ADR-001 or B4. See [[lesson-presence-test-vs-behavioral-proof]] — same overclaim pattern as 015 GC5.
