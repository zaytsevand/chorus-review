---
name: project-015-fitness-harness-gatea
description: Norman-lens Gate A findings on feature 015 suite-fitness-harness — the FR-004 violation-vs-environmental distinction is contradicted by the source-of-truth script's exit contract
metadata:
  type: project
---

Feature 015 (suite-fitness-harness) Gate A, HCD seat held on FR-004 (distinguish check VIOLATION from ENVIRONMENTAL error).

**Load-bearing finding (N1, proposed 🔴):** The harness contract (`contracts/harness-contract.md:18-25`, C-H2) and data-model (`data-model.md:15`, three states PASS/VIOLATION/ENV-ERROR) promise the violation-vs-environmental distinction, but `scripts/check-suite-integrity.sh` cannot supply it: `set -uo pipefail` (no -e, line 12), only `exit 1`/`exit 0` (lines 202-207), and grep errors suppressed via `2>/dev/null` (lines 43,53). No reserved exit code for "could not run," and the one named env error (grep missing) is silenced. Locators print to **stdout** via printf, not stderr — so C-H2's "surface the script's stderr" (N2) has no source.

**Why:** A caller cannot perceive a state the callee never emits. FR-002 forbids reimplementing the distinction in TS, so the fix must live in the script: reserved non-{0,1} exit code (e.g. exit 2) for env failure + stop suppressing grep stderr. Otherwise FR-004 is a false promise discovered the day grep is missing.
**How to apply:** If 015 reaches Gate C / implementation, verify the script gained an env-error exit code and unsuppressed stderr before accepting C-H2 as honored. If not done, FR-004 must be downgraded to best-effort in the spec.

Other findings: N3 (GC5 RED branch proves guard-text-presence + dir-absence, NOT halt — claim outruns mechanic; FR-008 discloses honestly but SC-004/C-H4 do not); N4 (failures name what broke, never what to do — recovery-path asymmetry vs the substrate guard's own required recovery action); N6 (FR-004's distinction stops at the script; harness/CI-infra failure has no distinct vocabulary); N5 (🟢 keep — `npm test` is the right affordance).

**Cycle-2 re-run (2026-06-21): Gate A CLEARS from HCD lens.** All five findings resolved by the revised spec/research/ADR-001:
- N1 CLEARED — FR-004 reserves exit 2 (env), exit 1 (violation), exit 0 (pass); FR-002 maps all three; stderr surfaced.
- N2 CLEARED — FR-004 + R7 stop discarding errors to /dev/null, surface as ENV-ERROR.
- N3 CLEARED — SC-004/FR-006/US3#2 reworded to "halt precondition holds" + truth-labeled guard-text+precondition, never "execution halts."
- N4/N6 CLEARED — FR-014 mandates recovery-action messages + distinct infra/violation/env vocabulary (3-way maps onto 3 exit codes).
- No NEW 🔴 from incorporation. Three voices (spec / script exit-contract + FR-012 result lines / FR-014 failure text) now tell one story.

**Carry to Gate C (non-blocking):** FR-014 promises "distinct vocabulary" but pins no exact wording and no acceptance scenario asserts a maintainer distinguishes the three from output alone — unasserted behavioral promise; wants a co-located test when FR-014 lands so the distinction is disciplined into runtime.

Related: [[understanding-chorus-review-007]], [[feedback-fail-loud-guard-placement-and-message]], [[project-014-substrate-guard-gatec]] (015 GC5 makes 014's substrate guard live-proof repeatable).
