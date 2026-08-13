---
name: project-feature015-gateB-vote
description: feature 015 fitness-harness Gate B (plan/tasks) architecture vote; sequencing sound, seam correctly tasked, 1 real sequencing gap (FC4 baseline ordering)
metadata:
  type: project
---

Feature 015 (suite fitness harness) Gate B — ARCHITECTURE lens: JOIN, 🟡 stakes. Reviewing TASK decomposition, not re-litigating design (Gate A cleared cycle 2, see [[project-feature015-gateA-cycle2]]).

Bar: internal-tooling fitness harness. Ranking sourced from spec FRs + Gate A incorporation. NOT inventing a production bar.

Spot-checks done (the load-bearing ones):
- chorus-sdlc/SKILL.md:263-268 live deny-default wording = "deny-filter runs on every candidate fact before any record write" + "dropped and flagged in the ledger". T007 now names THIS as its anchor — corrects the cycle-1 broken regex (`drop.*unless`) I confirmed false-RED at Gate A S1. Tasked right.
- Live script still `set -uo pipefail`, `2>/dev/null`, exit 0/1 only, no RESULT:/FCn: lines. Confirms Phase 2 additions are all net-new; no task asserts against already-built behavior. Sequencing premise holds.

Findings authored (RB1-RB5):
- RB1 🔴: T007 "verify GREEN on canon today" has no task that RUNS it before downstream T017 (two phases later). Exact Gate A S1 regression surface. Want explicit FC4-PASS-on-clean-tree acceptance INSIDE T007.
- RB2 🟡: T011 bundles FC4+FC6+exit contract into one clean-tree exit-0 assert; make it assert each FCn:PASS line individually (the discipline T006 exists for).
- RB3 🟢: FC7 verdict-deciding vs file-read split (T010/T024) correctly folds my cycle-2 NIT. Predicate ("pass/fail depends on regex result") lives only in prose; note it in T010.
- RB4 🟡: required-check-name ↔ job-name binding (twin of my Gate A D3) only DOCUMENTED in T016; branch protection is out-of-repo, no FF can assert it. Pin stable explicit job name in T015, quote it in T016 — make the manual step copy-paste.
- RB5 🟢: T030 is a guardrail phrased as a task; fold into T004/T028.

Sound, won't re-litigate: Phase2→3-6 sequencing correct; no TS check-logic (ADR-001 seam held); FC4-FC7 each own task + independent RED/GREEN; Gate-A-carries map (tasks.md:127-129) traces every finding to a task.
