---
description: "Size backlog and create baseline scope in RELEASE_PLAN.md"
command: speckit.acps.release-plan
---

<objective>
Take the approved `BACKLOG.md`, size each item using BCP and related counts from `.specify/counting/` when available, classify work types, define baseline scope (in / out / deferred), and produce `RELEASE_PLAN.md` with totals and optional milestones. Establish the contract between "what we commit to ship" and later technical planning.
</objective>

<context>
Runs after the baseline specify loop completes (every backlog item has been specified via `/speckit.specify`). It precedes the pipeline phase (`plan` → `tasks` → `implement`). This is the gate between "what" and "how". Use `$ARGUMENTS` for release name, target date, or manual sizing overrides if the user provides them.
</context>

<core_principle>
Baseline scope is a contract. After `RELEASE_PLAN.md` is accepted, scope changes flow through `/speckit.acps.change-request`, not ad-hoc edits to committed baseline intent.
</core_principle>

<process>
1. Read `BACKLOG.md` for the authoritative ordered item list and identifiers/titles.
2. Read `.specify/counting/*-count.md` files when present; extract BCP, FP, SNAP, or analogous totals per spec/item. If counting files are missing for an item, record an explicit estimate with rationale.
3. For each backlog item, record size (from counting or estimate), classify as **PF** (Product Feature), **BCP** (Business Complexity), **SNAP** (non-functional sizing where applicable), or **NFR** (non-functional requirement narrative).
4. Propose **In Scope**, **Out of Scope**, and **Deferred** sets. Present the classification and boundaries to the user and obtain explicit confirmation before writing the release baseline.
5. Calculate totals: total BCP (and other agreed metrics), total FP if tracked, and count of in-scope items. Surface any double-counting or shared-foundation assumptions.
6. If the in-scope set is large, group items into release milestones with ordering rationale (risk reduction, dependency order, or cut-line).
7. Write `RELEASE_PLAN.md` with sections: **Baseline Scope** (table: item, type, size, notes), **Out of Scope**, **Deferred**, **Sizing Summary**, **Milestones**, **Assumptions**, **Risks**.
8. Update `.specify/project/PROJECT_STATUS.md` with a release-planning entry: confirmed scope snapshot, totals, milestone names if any, and pointer to `RELEASE_PLAN.md`.
Reflect `$ARGUMENTS` in Assumptions or a short **Overrides** subsection when the user supplies targeting constraints.
</process>

<anti_patterns>
Do not mark items in scope without user confirmation. Do not skip sizing—every in-scope row needs a number or an explicit "TBD with reason" that the user accepts. Do not edit `BACKLOG.md` here; it remains the ordered backlog—`RELEASE_PLAN.md` filters and frames delivery. Do not silently drop counting data when files exist.
</anti_patterns>

<success_criteria>
`RELEASE_PLAN.md` exists. Every in-scope item lists a size and classification. Totals are computed and shown. The user confirmed scope boundaries. `.specify/project/PROJECT_STATUS.md` records this planning step. Deferred and out-of-scope items are explicit, not implied.
</success_criteria>
