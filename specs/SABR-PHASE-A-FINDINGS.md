# SABR Phase A — Findings & Enhancement Roadmap

**Date:** 2026-05-19
**Source:** SABR Phase A benchmark — 100 runs, 5 methodologies × 4 tasks × 5 runs
**Purpose:** Preserve Phase A learnings as a versioned reference before Phase B begins.
**Objective context:** Build the best ACPS extension for spec-kit — ready for any client constraint or environment.

---

## What Was Tested

| Task | Subject | Assertions | Type |
|------|---------|-----------|------|
| A | Node.js proxy socket leak fix | 3/3 | Bug fix |
| B | Prisma soft-delete feature | 18/18 | Feature with schema change |
| C | Bash god-script refactor | 12/12 | Refactoring |
| D | Billing slice from vague PM spec | 28/28 | Full workflow, ambiguous spec |

All 100 runs: PASS. The benchmark ran automated — same agent, same fix, all methods.
Discriminators are artifact quality and gap analysis, not pass rate.

---

## ACPS Strengths Confirmed

These behaviors are what ACPS does better than peers. Preserve them in all future changes.

### 1. grill-me + define-success as mandatory chain in acps.spec
This is ACPS's highest-value differentiator. No other methodology benchmarked enforces
requirement elaboration *before* planning begins. Bigpowers has `grill-me` and `define-success`
as optional pre-flight sub-skills — agents can skip them. ACPS's `acps.spec` command calls them
as mandatory sub-routines: you cannot exit spec without step→verify pairs.

**On Task D (vague billing spec):** ACPS produced Given/When/Then ACs with 7 explicit scenarios —
including the 409 conflict (user already subscribed), the 404 unknown plan, and the 10% tax formula.
GSD missed all three in its first pass. BMAD caught them via the Mary persona PRD. ACPS caught them
via the grill-me question sequence.

**Keep as-is. This is the structural advantage.**

### 2. Given/When/Then AC format creates unambiguous test targets
ACPS's ACs are test-isomorphic by design: every Given/When/Then maps directly to a test case.
On Task B, the "Given a user is deleted, Then the row STILL EXISTS with deletedAt set" AC
is the one that catches hard-delete implementations. Bigpowers' PLAN.md implies this but
doesn't state it as a testable assertion. The format forces precision.

**Keep as-is. Resist any temptation to loosen the AC format for "simplicity".**

### 3. BCP/FP/SNAP scope counting as a client-facing artifact
Not measured in SABR (single-feature, no delivery context), but architecturally important.
The counting module is ACPS's unique client-delivery value — it translates technical work
into scope language that clients and PMs understand. No other methodology benchmarked has this.

**Keep as-is. This is the enterprise/client-constraint differentiator.**

### 4. Baseline bridge (acps.plan Phase 2)
The traceability matrix (baseline item → technical approach → gaps) prevents scope creep and
delivery gaps from hiding in the plan. Bigpowers has no equivalent. BMAD has no equivalent.
Every professional delivery context (client projects, regulated industries) requires this.

**Keep as-is. Strengthen enforcement — see Gap 2 below.**

---

## Gaps Found — Ordered by Impact

### Gap 1: acps.spec's grill-me is not environment-aware
**Where it showed:** Task A (Node.js proxy) and Task C (bash script).

`grill-me` asks domain questions (what entities, what states, what error cases). For bug-fix tasks
and refactoring tasks, this question set is wrong — the agent should be asking about the platform,
the existing contract, and the blast radius, not about business domain entities.

On Task A, the grill-me pattern should ask: "What is the socket lifecycle? When does 'close' fire —
on stream EOF or on socket disconnect? What other connections share this socket pool?" instead of
"What entities does this feature create?"

**Fix:** See E-001 below.

### Gap 2: Bridge phase has no enforcement when baseline is absent
**Where it showed:** Tasks where there is no RELEASE_PLAN.md (new projects, small teams, quick fixes).

`acps.plan` Phase 2 (baseline bridge) reads `specs/RELEASE_PLAN.md`. When it doesn't exist,
the bridge phase is effectively skipped. The orphan-detection and gap-detection only run when
there is a formal baseline to compare against. In lightweight usage (solo developer, no formal
release plan), the bridge phase produces nothing.

**Fix:** See E-002 below.

### Gap 3: acps.implement does not enforce that RED tests come from spec ACs
**Where it showed:** Task B (soft-delete row-persist invariant).

`acps.implement` calls `develop-tdd`, which mandates RED → GREEN → REFACTOR per behavior slice.
But the link between "behavior slice" and "AC from the spec" is implicit — it relies on the agent
reading the spec and connecting the dots. An agent that skips this mental step writes RED tests
for happy paths and misses the invariant ACs.

ACPS's `acps.spec` produces step→verify pairs via `define-success`. Those pairs should flow
into `acps.implement` as the explicit source list for RED tests — not as optional reading material.

**Fix:** See E-003 below.

### Gap 4: No client-constraint configuration for skipping heavyweight gates
**Where it showed:** All tasks in automated benchmark (no client context, no UAT needed).

ACPS has a full workflow: spec → plan → implement → test → UAT → release. For client delivery
projects this is exactly right. For internal tools, solo developer work, or benchmark-style tasks,
the UAT gate and scope counting are overhead. There is no documented "lightweight mode" that
skips non-applicable gates without losing the structural advantage of grill-me + ACs.

The risk: teams facing client resistance to "too much process" will skip ACPS entirely rather
than run it in a lighter configuration.

**Fix:** See E-004 below.

---

## Enhancement Proposals

### E-001: Task-type-aware grill-me routing
**Target:** `commands/spec.md` (acps.spec) and `commands/fix.md` (acps.fix)
**Change:** Add a task-type detection gate at the start of `acps.spec`:

> Classify the input:
> - **Feature:** new behavior added to the system → run full grill-me (domain entities, states, error cases, ACs)
> - **Bug fix:** existing behavior is wrong → run platform-aware grill-me (contract, blast radius, socket/lifecycle questions, platform pitfalls)
> - **Refactor:** structure changes, behavior must not change → run safety-aware grill-me (what must stay the same, invariants, callers, test coverage)

For bug fix tasks, grill-me should include a platform pitfalls check:
- Node.js: ask about event emitter close vs socket close, keep-alive pool semantics, pipe error propagation
- Bash: ask about pipefail, trap ERR, subshell variable scope
- Prisma/ORM: ask about hard-delete vs soft-delete, cascade behavior, migration rollback

**Expected impact:** High for bug-fix and refactor tasks. Currently the spec phase is weakest
on non-feature work. This makes ACPS equally strong across all task types.

### E-002: Lightweight baseline bridge for baseline-absent projects
**Target:** `commands/plan.md` (acps.plan) Phase 2
**Change:** When `specs/RELEASE_PLAN.md` is absent or empty:

> Run a self-baseline pass instead of a formal bridge:
> 1. List every item in the spec's acceptance criteria as a "baseline item"
> 2. For each task in the implementation plan, trace it to a spec AC
> 3. Flag orphaned tasks (no AC parent) as scope creep candidates
> 4. Flag uncovered ACs (no task child) as delivery gaps
>
> Write this as a lightweight traceability table in `specs/plan/plan.md` under a "Self-Baseline" section.
> Note at the top: "No formal RELEASE_PLAN.md found. Self-baseline used. Upgrade to formal baseline for client delivery."

**Why:** Preserves the traceability value of the bridge phase even in lightweight usage.
Gives solo developers the gap-detection benefit without requiring a formal release plan.

**Expected impact:** Medium. Unblocks ACPS use in quick-turnaround or internal projects
without degrading the structure for client delivery projects.

### E-003: AC-anchored RED phase in acps.implement
**Target:** `commands/implement.md` (acps.implement) Phase 2
**Change:** Add a mandatory step before the first RED test:

> Read the step→verify pairs from `specs/spec/<spec-slug>-count.md` (or from the spec's
> Verification Pairs section if counting was skipped). List them as the RED test inventory.
>
> Rule: every RED test written must have a parent AC from this list.
> If a RED test has no parent AC: it is either out of scope (remove it) or the AC is missing
> (add it to the spec before continuing — do not proceed past this gate until every RED test
> has a documented AC parent).

**Why this matters:** prevents happy-path TDD where engineers write the tests they can think of
rather than the tests the spec requires. The step→verify pairs from `acps.spec` already exist —
they just need to be the explicit source list for the RED phase, not optional background reading.

**Expected impact:** High on schema-change and feature tasks. Low risk (additive gate only).

### E-004: Configuration-driven workflow tiers
**Target:** `config/acps-config.template.yml`
**Change:** Add a `workflow_tier` configuration key with three values:

```yaml
# Workflow tier controls which gates are enforced
# full       — all gates: spec → baseline → plan → implement → test → UAT → release
# standard   — core gates: spec (no counting) → plan (self-baseline) → implement → test → release
# lightweight — minimal gates: spec (grill-me + ACs only) → plan → implement → test
workflow_tier: full
```

**Tier behavior:**

| Gate | full | standard | lightweight |
|---|---|---|---|
| grill-me in acps.spec | mandatory | mandatory | mandatory |
| BCP/FP/SNAP counting | mandatory | skipped | skipped |
| Formal baseline bridge | mandatory | self-baseline | skipped |
| UAT phase | mandatory | optional | skipped |
| Release artifacts | full | changelog only | skipped |
| Scope review (acps.scope) | mandatory | optional | skipped |

**The invariant across all tiers:** grill-me + Given/When/Then ACs + RED anchored to ACs.
These are never optional — they are the core quality mechanism.

**Why:** The biggest adoption blocker for ACPS in client-constrained environments is
"too much process." A team that can't do UAT shouldn't have to fork the extension or
comment out commands — they should set `workflow_tier: lightweight` and get the structural
quality benefits without the delivery ceremony. The full tier stays intact for enterprise
client delivery.

**Expected impact:** High for adoption. Medium implementation effort. This is the single
change that makes ACPS viable across the full range of client constraints from "startup, ship fast"
to "enterprise, formal delivery with counting."

---

## What the Benchmark Cannot Measure (Phase B Design Notes)

Phase A was automated — same agent, same fix applied per run. Real methodology discrimination
requires:

1. **Truly blind runs:** one methodology at a time, fresh agent context, no cross-run memory
2. **Artifact quality scoring:** evaluate `specs/` output before any code is written — AC coverage,
   edge-case enumeration, schema correctness, traceability completeness
3. **Multi-iteration tasks:** 1 initial implementation + 2–3 follow-on change requests.
   ACPS's AC traceability should make each change request cheaper than GSD's no-artifact approach.
4. **Client-constraint simulation:** one run with full ACPS tier, one run with lightweight tier.
   Both should produce correct code; the full tier should produce better-scoped change requests.

The highest-signal scenario for Phase B: Task D variant with follow-on changes.
"The tax rate changed to 15%." "Add a trial tier." "Implement proration." Each change request
hits the existing ACs — ACPS's traceability table should surface exactly what needs to change.

---

## Relationship to ACPS Roadmap

Suggested release sequence:

| Release | Proposal | Effort | Priority |
|---|---|---|---|
| v1.1.0 | E-003: AC-anchored RED phase in acps.implement | S | High |
| v1.1.0 | E-002: Self-baseline for baseline-absent projects | S | High |
| v1.2.0 | E-001: Task-type-aware grill-me routing | M | High |
| v1.3.0 | E-004: workflow_tier configuration | M | Strategic |

E-003 and E-002 are additive and low-risk — ship together in v1.1.0.
E-001 requires routing logic in `acps.spec` — own release.
E-004 is a cross-cutting change (affects every command) — own release after E-001 stabilizes.

---

## The North Star

The goal is not to make ACPS more heavyweight. It is the opposite:

**ACPS should be the methodology that works correctly at every tier —
from a 2-hour internal feature to a 6-month enterprise delivery —
without requiring the team to choose between "process" and "speed".**

The invariant (grill-me + ACs + AC-anchored TDD) is non-negotiable.
Everything else is configuration.

The platform-pitfall awareness (E-001) makes ACPS equally strong on bug fixes and refactors,
not just feature work. That is the gap that closes the last distance between ACPS and bigpowers
on Task-A-type problems — where the issue is a non-obvious platform behavior, not a missing AC.
