---
description: "Bridge baseline plan with technical plan from /speckit.plan"
command: speckit.acps.plan-bridge
---

<objective>
Cross-reference `RELEASE_PLAN.md` (baseline business scope) with the technical plan produced by `/speckit.plan` (`plan.md` and `.specify/plan/` artifacts). Ensure every in-scope baseline item maps to technical work, identify orphaned technical items, and record a traceability matrix so `/speckit.tasks` builds on aligned scope.
</objective>

<context>
Runs immediately after `/speckit.plan` and before `/speckit.tasks`. Optional hook: `after_tasks` for reconciliation if your integration runs bridging later—still reconcile artifacts, do not re-interview from scratch. Use `$ARGUMENTS` only for alternate plan paths or explicit baseline tags if multiple plans exist.
</context>

<core_principle>
Synthesize from existing artifacts; do not re-run `/speckit.plan` or re-gather requirements in the abstract. Read, reconcile, flag gaps for human decision.
</core_principle>

<process>
1. Read `RELEASE_PLAN.md` and extract in-scope baseline items (and milestone grouping if present).
2. Read the latest technical plan: primary `plan.md` plus `.specify/plan/` outputs from `/speckit.plan`. Resolve newest vs named variant using `$ARGUMENTS` when provided.
3. For each in-scope baseline item, find the best-matching technical plan sections (phases, components, migrations, test strategy). Build a traceability table with columns: **Baseline Item** → **Technical Approach** → **Gaps** (empty when covered).
4. Identify **orphans**: technical plan items with no baseline parent (potential scope creep) and baseline items lacking technical coverage (delivery gaps).
5. Present the traceability table, orphan list, and gap notes to the user; keep the tone factual—classification, not blame.
6. When gaps exist, recommend whether to update the technical plan (preferred for missing engineering detail) or adjust scope via the change-request path—never silently rewrite baseline scope here.
7. Write or update a **Bridge** section inside `plan.md` or create `.specify/plan-bridge.md` (choose one canonical location and reference the other) containing the full traceability matrix, orphan inventory, and decisions pending user action.
8. Update `.specify/project/PROJECT_STATUS.md` with command `speckit.acps.plan-bridge`, links to the matrix artifact, counts of mapped items / gaps / orphans, and follow-ups.
</process>

<anti_patterns>
Do not modify `RELEASE_PLAN.md` in this command. Do not re-invoke `/speckit.plan`. Do not invent technical approaches to hide gaps—flag them. Do not drop orphan analysis when the plan is large; summarize but keep the inventory.
</anti_patterns>

<success_criteria>
A traceability matrix exists (in `plan.md` bridge section or `.specify/plan-bridge.md`) mapping baseline items to technical approaches. Orphans are explicitly listed. The user has reviewed gaps and orphans. `.specify/project/PROJECT_STATUS.md` includes a bridge entry with next actions.
</success_criteria>
