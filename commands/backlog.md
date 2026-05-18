---
description: "Create ordered epic backlog in specs/BACKLOG.md"
command: acps.backlog
---

<objective>
Break product requirements into an ordered list of epics and features captured in `specs/BACKLOG.md`. Each item must include a concise description, a summary of acceptance criteria, priority, and an estimated complexity hint so downstream spec work stays traceable.
</objective>

<context>
Runs after `acps.init` and before the spec loop (`GW_SpecsRemaining`). Output feeds `acps.spec`. Use `$ARGUMENTS` for explicit paths to requirements, PRD excerpts, or stakeholder notes; otherwise discover inputs under `specs/` and repository docs.
</context>

<core_principle>
Vertical slices, not horizontal layers. Every backlog item must deliver end-to-end user or stakeholder value across the stack that slice needs — avoid layer-only work items that cannot be demonstrated or validated independently.
</core_principle>

<bigpowers_skills>
Sub-routines invoked by this command:

- **`grill-me`** — before decomposing requirements, challenges assumptions one question at a time to surface gaps and hidden constraints
- **`elaborate-spec`** — refines vague product ideas via structured dialogue before writing backlog items
</bigpowers_skills>

<process>
1. Read `specs/CONSTITUTION.md` for principles, quality gates, and non-negotiables that bound backlog shaping.
2. Run **`grill-me`** — surface assumptions and hidden constraints before proceeding.
3. Read requirements from `$ARGUMENTS` (paths or pasted references) and from `specs/` artifacts (memory, specs-in-progress, PRDs, user stories) plus `README.md` / product docs when relevant.
4. Run **`elaborate-spec`** for any requirements that are vague or thin — resolve them via dialogue before decomposition.
5. Decompose requirements into an ordered backlog. For each item capture: title; one–two sentence description; bullet acceptance criteria; priority (`must` / `should` / `could`); estimated complexity hint (`small` / `medium` / `large`).
6. Present the draft backlog to the user and ask: "Is the granularity right? Any items to merge, split, or reorder?"
7. Perform one feedback iteration round: apply merges/splits/reorders and priority tweaks the user confirms; do not silently reshuffle after approval.
8. Write `specs/BACKLOG.md` as a numbered list. Each entry must include title, description, acceptance criteria (bullets), priority, and complexity hint.
9. Update `specs/project/PROJECT_STATUS.md` with a backlog-creation entry: date/command, count of items, link to `specs/BACKLOG.md`, and note any open questions left for spec.
</process>

<anti_patterns>
Do not create horizontal slices such as "all database schemas" or "all APIs" without an end-to-end outcome. Do not skip `grill-me` when requirements are vague. Do not skip the user review and iteration step. Do not add items that lack acceptance criteria. Do not bury constitution conflicts — surface them as backlog risks or explicit spikes.
</anti_patterns>

<success_criteria>
`specs/BACKLOG.md` exists with numbered items. Each item has a title, description, and explicit acceptance criteria. The user approved the list after the review prompt. `specs/project/PROJECT_STATUS.md` includes an updated entry recording backlog creation.
</success_criteria>
