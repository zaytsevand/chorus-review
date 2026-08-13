---
name: project-feature015-gateA-cycle2
description: feature 015 fitness-harness Gate A cycle-2 incorporation verdict; all 6 architecture 🔴 cleared, FC7-vs-FR006 NIT
metadata:
  type: project
---

Feature 015 (suite fitness harness) Gate A cycle 2 — ARCHITECTURE lens verdict: CLEARS.

All six gating 🔴 resolved at design-stage level (spec mandates runtime behavior; not built yet — defer to Gate C):
- R1 (env-vs-violation): CLEARED. FR-004 reserves `exit 2` for env-error, removes `2>/dev/null` discard, surfaces stderr; FR-002 maps 0/1/2. Distinction now sourced in the script, not inferred by wrapper.
- R2 (banner coupling): CLEARED. FR-012 stable `FCn: PASS|VIOLATION` machine lines decoupled from `=== FCn ===` banner.
- R3 (stale home list): CLEARED. FR-013 FC6 meta-check flags unregistered secret-filter/sibling-guard homes.
- ADR-001 (R4 seam + R6 runner): ADEQUATE. Capability fault line greppable→shell / assert+report+teardown→TS; Vitest over Jest (zero transform deps); shell-only alternative priced & rejected on DevEx. FC7 guards the boundary.

**Why cleared despite live script still ships old behavior:** Verified runtime — `scripts/check-suite-integrity.sh` still has `set -uo pipefail` (no -e), `2>/dev/null`, `|| true`, only exit 0/1, no RESULT:/FCn: lines. That is EXPECTED at design-stage; R1 demanded a real *source* for the distinction, and the spec now mandates it. The script edits are a Gate C verification item.

NIT (non-blocking, flag at impl): FC7 "no grep/regex over canon in tests/**" must not forbid GC5 reading sibling SKILL.md guard text in TS (FR-006 sources guard text from the live file). Scope FC7 to *check-logic grep*, not file reads, or FC7 contradicts FR-006.
