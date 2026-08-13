---
name: project-feature015-gateA-vote
description: Feature 015 (suite-fitness-harness) Gate A architecture cross-vote; verified S1 regex break, B2 halt-inference, G4 shell-vs-TS proportionality
metadata:
  type: project
---

Feature 015 = suite-fitness-harness (NOT suite-decomposition; that was 014). Discharges 014's deferred FR-019: wraps FC1-FC3 (scripts/check-suite-integrity.sh, the source of truth) in the repo's FIRST automated harness (Vitest/TS) + FIRST CI entry point (.github/workflows/fitness.yml), adds GC3 (secret-filter congruence, lands as shell FC4 per FR-005a) and GC5 (substrate-guard behavioral emit).

Gate A architecture-lens vote, verified against runtime not just labels:

- **S1 CONFIRM 🔴 (verified)**: research.md R4's proposed deny-default regex `/(drop|exclud).*unless|unless.*(pass|clear)|default.*drop|deny.default/i` does NOT match live chorus-sdlc/SKILL.md:263 ("deny-filter runs on every candidate fact before any record write"). False-RED on unmodified canon day-one. Schneier caught a real doc-vs-runtime gap. research hedges "tuned in implementation" but authored regex set is broken as specced.
- **B2 CONFIRM 🟡 (verified)**: GC5 RED branch asserts existsSync(core)===false as the "halt." Halt is INFERRED from precondition, not observed (no loader enforces REQUIRED:, FR-008). SC-004's "execution halts" claim != the mechanic. Honest limit but the claim/mechanic gap is real (N3 same).
- **G4 PRIORITIZE 🟡**: TS runtime introduced for GC5 = one `mv` + grep SKILL.md + existsSync + restore. Shell `trap ... EXIT` gives identical crash-safe teardown (FR-007). FC4 already proved shell can host this. Introducing the suite's first non-markdown runtime for this strains FR-009/US4 proportionality. My R4 seam holds but line should sit further toward shell; want ADR on why TS earns keep for GC5 or drop GC5 to shell too.

Most load-bearing: **D1 (🔴)** — no committed lockfile → `npm ci` fails first CI run + every fork PR → harness never executes → feature delivers zero binding (voids Principle IX constraint it exists to serve). D3 (check not required-to-merge = dashboard not gate) is the twin.

N1/D1/D3 all correctly 🔴 (echo R1). N1: FR-004 asserts violation/env distinction the script doesn't yet implement.
