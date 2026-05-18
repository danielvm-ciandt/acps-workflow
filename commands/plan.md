---
description: "Technical plan → baseline bridge → task breakdown → optional analysis"
command: acps.plan
---

<objective>
Produce a complete per-spec delivery plan in four mandatory phases: (1) technical plan, (2) bridge to baseline scope, (3) task breakdown, and (4) optional pre-implement analysis. Every in-scope baseline item maps to technical work; orphans and gaps are flagged before implementation begins.
</objective>

<context>
Runs after `acps.baseline` (or re-enters from `GW_EpicComplete` no). Precedes `acps.implement`. Use `$ARGUMENTS` for spec identifiers, alternate plan paths, or explicit baseline tags when multiple plans exist.
</context>

<core_principle>
Synthesize from existing artifacts; do not re-gather requirements from scratch. Read, plan, reconcile, flag gaps for human decision. The bridge and task breakdown are mandatory — they are not optional cleanup steps.
</core_principle>

<bigpowers_skills>
Sub-routines invoked by this command:

- **`deepen-architecture`** — architecture depth pass before writing the technical plan; updates `specs/CONTEXT.md`
- **`design-interface`** — API shape proposals via parallel subagents when the spec involves new interfaces
- **`spike-prototype`** — optional: throw-away spike for uncertain domains; produces `specs/SPIKE-<name>.md` before planning proceeds
- **`scope-work`** — feeds feature scope into the plan's `specs/SCOPE.md` section
- **`plan-work`** — generates the implementation plan with step → verify pairs
</bigpowers_skills>

<process>
**Phase 1 — Technical Plan**

1. Read the current spec and its acceptance criteria. Read `specs/CONTEXT.md` for codebase context.
2. Run **`deepen-architecture`** — deepen architecture understanding relevant to this spec; updates `specs/CONTEXT.md`.
3. **Optional:** Run **`spike-prototype`** if a domain is technically uncertain; produces `specs/SPIKE-<name>.md`.
4. Run **`design-interface`** if the spec involves new APIs or service boundaries — parallel subagents propose interface shapes.
5. Run **`scope-work`** — produce `specs/SCOPE.md` scoping section for this plan.
6. Run **`plan-work`** — generate the technical plan with step → verify pairs.
7. Write or update `specs/plan/plan.md` covering: architecture decisions, components, migrations, test strategy, and risks.

**Phase 2 — Baseline Bridge**

8. Read `specs/RELEASE_PLAN.md` and extract in-scope baseline items (and milestone grouping if present).
9. For each in-scope baseline item, find the best-matching technical plan sections (phases, components, migrations, test strategy). Build a traceability table: **Baseline Item** → **Technical Approach** → **Gaps**.
10. Identify **orphans**: technical plan items with no baseline parent (potential scope creep) and baseline items lacking technical coverage (delivery gaps).
11. Present the traceability table, orphan list, and gap notes to the user.
12. When gaps exist, recommend whether to update the technical plan (preferred) or adjust scope via `acps.cr` — never silently rewrite baseline scope here.
13. Write bridge traceability into a **Bridge** section of `specs/plan/plan.md` (or `specs/plan/plan-bridge.md` if preferred, referenced from `plan.md`): full traceability matrix, orphan inventory, and decisions pending user action.

**Phase 3 — Task Breakdown**

14. Decompose the technical plan into discrete, ordered tasks. Each task: title, description, inputs, outputs, estimated effort band (S/M/L), and dependency chain.
15. Write `specs/plan/tasks.md` with the ordered task list.
16. Confirm task list with user before exiting this phase.

**Phase 4 — Analysis (optional)**

17. If requested via `$ARGUMENTS` or based on complexity, run a pre-implement analysis pass: identify high-risk tasks, review for SOLID violations, check for missing test coverage strategy, flag external dependencies.
18. Append analysis findings to `specs/plan/plan.md` under an **Analysis** section.

**Record**

19. Update `specs/project/PROJECT_STATUS.md` with command `acps.plan`, links to `specs/plan/plan.md` and `specs/plan/tasks.md`, counts of mapped items / gaps / orphans, and follow-ups.
</process>

<anti_patterns>
Do not modify `specs/RELEASE_PLAN.md` in this command. Do not skip the bridge phase — traceability is mandatory. Do not invent technical approaches to hide gaps — flag them. Do not skip task breakdown — `acps.implement` reads `specs/plan/tasks.md`. Do not drop orphan analysis when the plan is large; summarize but keep the inventory.
</anti_patterns>

<success_criteria>
`specs/plan/plan.md` exists with architecture decisions and test strategy. Bridge traceability matrix maps all baseline items to technical approaches; orphans are listed. `specs/plan/tasks.md` exists with ordered task list confirmed by user. `specs/project/PROJECT_STATUS.md` includes a plan entry with next actions.
</success_criteria>
