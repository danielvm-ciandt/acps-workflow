---
description: "Create ordered epic backlog in BACKLOG.md"
command: speckit.acps.create-epic-backlog
---

<objective>
Break product requirements into an ordered list of epics and features captured in `BACKLOG.md`. Each item must include a concise description, a summary of acceptance criteria, priority, and an estimated complexity hint so downstream specify work stays traceable.
</objective>

<context>
Runs after `/speckit.constitution` and before the baseline specify loop (`GW_BacklogLista`). Output feeds the `/speckit.specify` loop. Use `$ARGUMENTS` for explicit paths to requirements, PRD excerpts, or stakeholder notes; otherwise discover inputs under `.specify/` and repository docs.
</context>

<core_principle>
Vertical slices, not horizontal layers. Every backlog item must deliver end-to-end user or stakeholder value across the stack that slice needs—avoid layer-only work items that cannot be demonstrated or validated independently.
</core_principle>

<process>
1. Read `.specify/memory/constitution.md` for principles, quality gates, and non-negotiables that bound backlog shaping.
2. Read existing requirements from `$ARGUMENTS` (paths or pasted references) and from `.specify/` artifacts (memory, specs-in-progress, PRDs, user stories) plus `README.md` / product docs when relevant.
3. Decompose requirements into an ordered backlog. For each item capture: title; one–two sentence description; bullet acceptance criteria; priority (`must` / `should` / `could`); estimated complexity hint (`small` / `medium` / `large`).
4. Present the draft backlog to the user and ask: "Is the granularity right? Any items to merge, split, or reorder?"
5. Perform one feedback iteration round: apply merges/splits/reorders and priority tweaks the user confirms; do not silently reshuffle after approval.
6. Write `BACKLOG.md` as a numbered list. Each entry must include title, description, acceptance criteria (bullets), and priority (and retain the complexity hint inline or in a short subfield).
7. Update `.specify/project/PROJECT_STATUS.md` with a backlog-creation entry: date/command, count of items, link to `BACKLOG.md`, and note any open questions left for specify.
</process>

<anti_patterns>
Do not create horizontal slices such as "all database schemas" or "all APIs" without an end-to-end outcome. Do not skip the user review and iteration step. Do not add items that lack acceptance criteria. Do not bury constitution conflicts—surface them as backlog risks or explicit spikes.
</anti_patterns>

<success_criteria>
`BACKLOG.md` exists with numbered items. Each item has a title, description, and explicit acceptance criteria. The user has approved the list after the review prompt. `.specify/project/PROJECT_STATUS.md` includes an updated entry recording backlog creation.
</success_criteria>
