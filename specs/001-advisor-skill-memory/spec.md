# Feature Specification: Advisor Skill Memory

**Feature Branch**: `001-advisor-skill-memory`

**Created**: 2026-05-31

**Status**: Draft

**Input**: User description: "create a skill-based chorus setup for every advisor. Each advisor may request a beforehand knowledge about the system it's going to review. This knowdledge must be preserverd in a project-dependant memory for this particular project and reused by the advisor when performing its primary duties. Example: Mark Richards advisor should ask about system -illities (system charateristics) and account for them when analyzing the architecture."

## User Scenarios & Testing *(mandatory)*

### User Story 1 — First-time advisor preflight on a new project (Priority: P1)

A user invokes a chorus advisor (e.g. the architecture advisor) for the
first time on a project they have not yet onboarded. Before producing
its primary review output, the advisor recognises that it has no
project-specific knowledge of the kind it needs to do its job well
(for the architecture advisor: which architectural characteristics the
team optimises for — evolvability, performance, security, etc.). The
advisor asks the user a small, lens-specific set of questions, stores
the answers in a place that belongs to *this project* and *this
advisor*, then proceeds with the review using those answers as context.

The next time the same advisor runs on the same project — whether
invoked solo or as part of a full chorus round — it loads its stored
knowledge silently and uses it without re-interviewing.

**Why this priority**: Without this, every chorus round either pays
the interview cost again, or the advisor reasons from defaults that
may not match the project's actual priorities. The first review on
any project is the load-bearing one; it is also where the most
re-derivation happens today.

**Independent Test**: With only this story implemented for one
advisor (e.g. the architecture advisor), a user can invoke that
advisor on a project that has never used it, answer the preflight
questions, receive a review informed by those answers, re-invoke the
same advisor without being re-interviewed, and observe that the
second review still reflects the captured knowledge.

**Acceptance Scenarios**:

1. **Given** a project that has no stored architecture-advisor knowledge, **When** the user invokes the architecture advisor, **Then** the advisor asks a small, bounded set of questions (e.g. ranked architectural characteristics, integration topology, change-rate expectations) before producing its review.
2. **Given** a project that already has stored architecture-advisor knowledge, **When** the user invokes the architecture advisor, **Then** the advisor proceeds directly to the review without re-asking, and the review prose explicitly draws on the stored knowledge (e.g. "evaluating against your top-three characteristics: evolvability, observability, security").
3. **Given** a chorus round that includes multiple advisors who each have stored knowledge, **When** the round runs, **Then** no advisor re-interviews the user; each draws on its own stored knowledge silently and in parallel.

---

### User Story 2 — Updating advisor knowledge as the project evolves (Priority: P2)

The project evolves and a previously-captured answer no longer holds
(e.g. the team's top architectural characteristic shifts from
performance to evolvability as they prepare to scale the team rather
than the load). The user needs a way to update an advisor's stored
knowledge without forcing a full re-interview of every other advisor,
and without manually editing files.

**Why this priority**: Stored knowledge that cannot be updated
quickly becomes stale, which is worse than no stored knowledge — the
advisor confidently reasons from outdated assumptions and the team
stops trusting its output. This is a maintenance pattern, not a
launch-blocker, hence P2.

**Independent Test**: With this story implemented, a user with
existing stored knowledge for one advisor can request that advisor's
preflight refresh, answer the same (or evolved) questions, and
observe that subsequent invocations of that advisor draw on the
updated answers — without any other advisor being affected.

**Acceptance Scenarios**:

1. **Given** stored knowledge for the architecture advisor on a project, **When** the user explicitly requests an architecture-advisor knowledge refresh, **Then** the advisor re-interviews the user (showing the prior answers as defaults the user can keep or change) and persists the updated answers.
2. **Given** a refresh of one advisor's stored knowledge, **When** another advisor runs on the same project, **Then** the other advisor's stored knowledge is unaffected.
3. **Given** stored knowledge older than a project-configurable freshness window, **When** the advisor next runs, **Then** the advisor surfaces the staleness in its review output (one sentence: "your stored knowledge here is N months old; consider a refresh") without forcing an interview.

---

### User Story 3 — Sharing advisor knowledge with a team via version control (Priority: P3)

A team-of-multiple uses the chorus on a shared project. One person
answers an advisor's preflight; later, a colleague pulls the repo
and invokes the same advisor. The colleague's review must benefit
from the original answers without their having to repeat the
interview, and updates one person makes must propagate to teammates
through their normal source-control workflow.

**Why this priority**: Single-user projects work without this; the
P1 story already delivers value there. Multi-user teams need
shared storage for the captured knowledge to be a project artefact
rather than a private cache. This is a sharing pattern on top of the
core capture mechanism, hence P3.

**Independent Test**: With this story implemented, two users on
different machines, working on a clone of the same project, will
both see the same stored advisor knowledge once it has been
committed and pulled. Edits by one user, once committed and pulled,
are visible to the other.

**Acceptance Scenarios**:

1. **Given** stored knowledge committed to the project repository, **When** a second team member pulls the repository and invokes the same advisor, **Then** the second team member's invocation uses the stored answers without re-interviewing.
2. **Given** an advisor whose stored knowledge is updated and committed, **When** teammates pull the change, **Then** their subsequent invocations of that advisor draw on the updated answers.
3. **Given** a project with stored advisor knowledge, **When** a teammate inspects the project repository, **Then** they can read the stored knowledge directly in human-friendly form (not opaque or binary).

---

### Edge Cases

- **Advisor invoked outside any project context (no repository, no addendum).** The advisor must either skip preflight entirely and fall back to its current default behaviour, or surface that it lacks project context before proceeding — but it must not write knowledge to a location that would leak across projects.
- **User declines or skips the preflight interview.** The advisor must proceed with default behaviour and must not block the review. A record that preflight was declined should be kept so the user is not re-asked the same questions every session; an explicit refresh re-opens the question.
- **Partial preflight answers.** If the user answers some questions and leaves others, the advisor stores what it has and proceeds with defaults for the rest. Missing answers may be re-asked at next invocation; answered ones are stable.
- **Advisor's preflight schema changes (advisor file updated).** Stored answers may be partially incompatible with a new schema. The advisor must detect this, use what is still valid, and ask only about the new or changed questions — never silently discard prior answers and not silently apply a new schema to a stale answer.
- **Multiple chorus projects sharing one user account.** Advisor knowledge for project A must not bleed into project B; storage location must be unambiguously per-project.
- **CHORUS-PROJECT.md addendum disagreement.** When the project's `docs/reviews/CHORUS-PROJECT.md` already names some of the same facts (e.g. constitutional principles, anchor surface), the advisor's preflight must defer to the addendum rather than ask redundantly, or surface the conflict if it finds one.
- **Concurrent invocations of the same advisor.** If two sessions invoke the same advisor on the same project simultaneously, neither should corrupt the stored knowledge file; the simpler concurrency model the system supports is acceptable so long as data loss is impossible.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: Each advisor MUST declare its own preflight knowledge schema — the specific questions it needs answered before it can do its job well in a project-specific way.
- **FR-002**: An advisor's preflight schema MUST be lens-specific: the architecture advisor asks about architectural characteristics; the DDD advisor asks about bounded contexts and core/supporting subdomains; the security advisor asks about trust boundaries, attacker profile, and the team's effective security poverty line; and so on for every advisor on the chorus roster.
- **FR-003**: When an advisor is first invoked in a project that has no stored knowledge for it, the advisor MUST initiate a preflight interview of the user before producing its primary review output.
- **FR-004**: The system MUST persist preflight answers in a location scoped to BOTH (a) the project being reviewed and (b) the specific advisor that owns the knowledge — so that two different advisors' knowledge for the same project, or the same advisor's knowledge for two different projects, never collide.
- **FR-005**: Stored advisor knowledge MUST live alongside the project's other chorus artefacts (e.g. near `docs/reviews/CHORUS-PROJECT.md`) so it is committable and shareable through normal version control — not in a user-scoped or machine-scoped cache.
- **FR-006**: When an advisor is invoked in a project where its stored knowledge already exists, the advisor MUST load that knowledge silently and use it as context — without re-interviewing the user.
- **FR-007**: The system MUST provide a way to refresh a single advisor's stored knowledge on demand, re-running its preflight interview while preserving prior answers as defaults the user can keep or change.
- **FR-008**: A refresh of one advisor's stored knowledge MUST NOT affect any other advisor's stored knowledge.
- **FR-009**: Stored knowledge MUST be human-readable so that a teammate inspecting the repository can understand what was captured and why.
- **FR-010**: When the user explicitly declines or skips a preflight interview, the system MUST record the decline and proceed with the advisor's default behaviour, and MUST NOT re-prompt the user every session — but an explicit refresh request MUST re-open the interview.
- **FR-011**: When stored knowledge exists but is older than a project-configurable freshness window, the advisor MUST surface its age in its review output as a one-line note without forcing a re-interview.
- **FR-012**: When the project's chorus addendum (`docs/reviews/CHORUS-PROJECT.md`) already names a fact the advisor's preflight would ask about, the advisor MUST defer to the addendum and not ask redundantly.
- **FR-013**: When an advisor's preflight schema changes after knowledge has already been stored, the system MUST preserve still-valid prior answers and prompt only for new or changed questions.
- **FR-014**: When an advisor is invoked outside any project context (no repository present), the advisor MUST NOT write stored knowledge to a global or user-scoped location, and MUST either skip preflight entirely or warn the user explicitly that project-scoped storage is unavailable.
- **FR-015**: Each advisor's preflight schema MUST be bounded — small enough for a user to answer in a single sitting (target: under 5 minutes per advisor) — so that onboarding a project to the full chorus does not feel like onboarding a CRM.

### Key Entities

- **Advisor preflight schema**: The set of questions a specific advisor wants answered about a project before it does its work. Owned by the advisor; declared in the advisor's definition.
- **Advisor knowledge record**: The stored answers to one advisor's preflight schema for one project. Has identity (project + advisor), content (answers), and metadata (when captured, when last refreshed, whether the user declined).
- **Project chorus context**: The collection of all advisor knowledge records for a single project, plus the existing chorus addendum (`docs/reviews/CHORUS-PROJECT.md`). The advisor reads from both during a review.
- **Preflight interview**: The interaction in which an advisor asks the user its schema's questions and the answers are persisted. Triggered on first invocation in a project, or on explicit refresh.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: A user invoking the same advisor a second time on the same project answers zero preflight questions during that second invocation (down from N on the first).
- **SC-002**: A chorus round on a project that has been onboarded surfaces project-specific reasoning in at least 80% of its individual advisor outputs (review prose explicitly cites stored knowledge — e.g. "given your stated top-three characteristics…").
- **SC-003**: First-time onboarding of a single advisor on a new project completes in under 5 minutes of user time.
- **SC-004**: First-time onboarding of the full chorus roster (all eight advisors) on a new project completes in under 30 minutes of user time, split across as many sessions as the user wants.
- **SC-005**: When a teammate pulls a project for the first time and invokes any advisor that has already been onboarded by a colleague, they answer zero preflight questions.
- **SC-006**: When an advisor's review reasons from project knowledge that has gone stale (older than the configured freshness window), the staleness is surfaced in the review output in 100% of cases.
- **SC-007**: An update to one advisor's stored knowledge does not require re-onboarding any other advisor; the user's update time is bounded by that one advisor's schema size.

## Assumptions

- Each advisor is responsible for defining its own preflight schema; there is no single chorus-wide schema that all advisors share. (Mark Richards asks about -ilities; Eric Evans asks about bounded contexts; Cooper asks about named user personas; etc.)
- The project being reviewed is a software project tracked in a version-control repository. Stored advisor knowledge is committed with that repository.
- The stored-knowledge artefacts are committed by default but the team may choose to gitignore them on a per-project basis; the system does not enforce committing.
- The existing chorus addendum mechanism (`docs/reviews/CHORUS-PROJECT.md`) remains the single place for *project-wide* facts (exclusions, anchors, governance) and is authoritative when it overlaps with an advisor's preflight; the per-advisor stored knowledge is for *lens-specific* facts the addendum does not name.
- Each advisor's preflight is short — handfuls of questions, not surveys. The chorus already has the addendum for project-wide facts; the advisor preflight covers what the addendum does not.
- The default freshness window for stored knowledge is six months unless the project addendum names a different value. (Architecture characteristics and trust boundaries do drift over multi-quarter horizons.)
- Out of scope for v1: cross-project knowledge transfer (e.g. "import the same advisor knowledge from project X to project Y"). Each project's knowledge is independent.
- Out of scope for v1: GUI or CLI tooling for editing stored knowledge outside an advisor invocation. Manual file editing is acceptable for power users; the supported path is via advisor preflight or refresh.
