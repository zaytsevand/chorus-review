# Constitution preview patterns (chorus-core)

Generic patterns the chorus uses when previewing constitution alignment **before**
implementation lands. Project constitutions remain authoritative; this file names
cross-project gate habits.

## Principle XI — queue / poll / GET side-effects

When a spec or plan introduces:

- Queue polling (`GET …/queue`, tray tick → poll loop)
- Scheduler / fill / background materialization jobs
- Any read path that may write (lazy enqueue, audit touch, cache warm)

…the **plan** MUST include a **Principle XI side-effect inventory** table:

| Surface | Trigger | Declared effects | Named exception? | Consumer-visible? |

**Analyze severity:** missing inventory on such specs → **CRITICAL**.

**Theater variant:** documenting a write on GET as a "named exception" without a
constitution bullet or a superseding spec that removes the write is a 🔴 finding
(Uncle Bob: `getQueue` must not mean `createQueue`).

## Deferred trust machinery (anti-theater)

Trust/promotion schema (`trust_status`, promotion counters, cross-user reuse
columns) MUST NOT ship when the parent FR is **DEFERRED**. Gate B flags
data-model fields that lack a non-deferred consuming FR.

## Value-first spec ordering

Gate A expects `spec.md` to contain **Outcome & Stage-1 proof** (primary outcome,
Stage-1 proof slice, stage boundary) **before** functional-requirement expansion.
Goldratt may accuse FR-before-verdict when outcome sections are missing or vague.

## Deferral checklist

See `DEFERRAL-CHECKLIST.md`. Gate A blocks DEFERRED items without a complete row.
