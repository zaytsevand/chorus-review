---
name: project-chorus-015-first-ci-gate
description: feature 015 (suite fitness harness) is the repo's FIRST CI entry point + first binding merge gate; Gate A D&O findings
metadata:
  type: project
---

Feature 015 establishes chorus-review's **first-ever CI** (`.github/workflows/fitness.yml`, GitHub Actions, push/PR, `npm ci && npm test`, secret-free/network-free) and its **first automated test harness** (Vitest/TS under `tests/harness/`). It wraps the 014 shell script (`scripts/check-suite-integrity.sh`, FC1-FC3, gains FC4 secret-filter congruence) and adds GC3 (secret-filter congruence) + GC5 (substrate-guard behavioral emit) tests. Shell stays source of truth; TS owns only subprocess orchestration + the GC5 move/restore behavioral run.

**Why:** discharges 014's deferred FR-019 (un-invoked checks rot) — the binding constraint is "something must pull the net taut on every change."

**How to apply (D&O Gate A verdicts, 2026-06-21):**
- The two 🔴 load-bearing gaps the YAML-centric contract omits: (D1) `npm ci` needs a **committed package-lock.json** named as a deliverable, else the gate fails day one; (D3) push/PR triggers alone are **advisory** — the `fitness` check must be a **required status check via branch protection** (config, not version-controlled — record who sets it + where) or a passing gate doesn't bind.
- 🟡 operability: pin Node to a major (`'22'`), not floating "current LTS" (D2); make TS-path (GC5) failures legible in the **job log** w/ failure-class distinction, not just reporter diff (D4); GC5 rename/restore blast radius is **local runs**, not CI (CI is ephemeral) — self-heal must be unconditional pre-test + failure msg must name the exact `mv` recovery command (D5).
- 🟢 polish: also run `bash scripts/check-suite-integrity.sh` standalone in CI to keep FR-011 honest (D6); the "cache is warm" phrasing in ci-contract has no cache mechanism — offline-reproducibility actually rides the lockfile (D7); `pull_request` (not `_target`) is the deliberate safe fork trigger — record it (D8, Security owns the trust edge).
- Design is correctly **proportional** otherwise (Vitest over Jest on dep-surface, one job no matrix, shell keeps greps, <=3 test files). Did NOT prescribe more machinery — that would be the over-engineering US4 guards against.

**Gate A cycle 2 verdict (2026-06-21): CLEARS from D&O lens.** D1 cleared (FR-011a names committed package-lock.json + npm ci, fails on lock drift). D2 cleared (Node pinned to major in CI engines + local prereq). D3 cleared at design gate (FR-003 required-to-merge; branch protection is a repo setting, not committable, so "documented" is the most a design gate can assert). D6 cleared (fitness.yml runs standalone bash script alongside npm test). TS-over-shell (ADR-001) accepted: run cost bounded — one pinned dev dep, npm ci, lockfile, no SAST at this scale, FC7 seam keeps greppable authority in shell. No new 🔴.

**One residual 🟡 (non-blocking) carried forward:** required-status-check binding is unverifiable until branch protection is configured — whoever enables it MUST confirm the required check NAME matches the workflow job name, or the gate silently doesn't bind. Belongs in quickstart, not a 🔴.

Recurring pattern worth carrying: a CI design specified as "a YAML file" reliably forgets the **non-file deliverables** that make a gate real — lockfile, version pin, branch-protection requirement. Always check those three on a first-CI feature. And the check-name/job-name match is the last silent failure mode after branch protection is on.

**Gate B verdict (2026-06-21): JOINED, 🟡.** All five carries (D1→T003, D3→T016, D2→T001, D6→T015, S5→T015) landed faithfully — none weakened in translation. Five Gate-B findings (DB1-DB5), none blocking:
- DB1 🟡 — T015 has no `concurrency:` group; first-CI workflows reliably forget it and stack duplicate runs per push. One stanza, zero/negative run-cost. The cheapest day-two fix.
- DB2 🟢 — "warm cache" claim (ci-contract, SC-002, my Gate-A D7) still has NO task wiring it; either add `setup-node cache: npm` keyed on lockfile or strike the phrase. Dangling contract claim.
- DB3 🟡 — no task proves the gate goes RED *in CI* (C-CI3 has no task); T014/T029 prove RED→GREEN locally only. Commission-in-CI once: push a deliberate violation, confirm Actions run red + locator in log.
- DB4 🟢 — non-finding retired on record: `fetch-depth` is NOT load-bearing (checks grep working tree, not history). Default shallow checkout correct.
- DB5 🟡 — make the job `name:` an explicit literal so T016's required-check string is copy-paste, not a guessed default. Moves the name-match trap from "documented" toward "hard to get wrong."

New recurring pattern for first-CI features (beyond the three non-file deliverables): also check the **four day-two operability stanzas** — concurrency group, dep cache (if the contract promises one), commission-in-CI proof, and explicit job `name:`. The first three are forgotten as reliably as the lockfile.
