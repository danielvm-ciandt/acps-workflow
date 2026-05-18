---
description: "TDD implementation: red → green → refactor per behavior slice"
command: acps.implement
---

<objective>
Implement the current spec's tasks from `specs/plan/tasks.md` using strict TDD discipline: red → green → refactor per behavior slice. Each behavior slice must pass a self-review gate before the full test suite is handed to `acps.test`.
</objective>

<context>
Runs after `acps.plan`. Feeds `acps.test`. Reads `specs/plan/tasks.md` for the ordered task list and the current spec for acceptance criteria. Use `$ARGUMENTS` for task identifiers, implementation flags, or parallel execution hints.
</context>

<core_principle>
**Red → Green → Refactor, no shortcuts.** Write the failing test first. Make it pass with the minimum correct code. Then refactor to meet `CONVENTIONS.md` standards. Never skip the red phase — a green test written after the implementation is not TDD.
</core_principle>

<bigpowers_skills>
Sub-routines invoked by this command:

- **`develop-tdd`** — mandates red → green → refactor discipline per behavior slice; reports TDD compliance per task
- **`enforce-first`** — F.I.R.S.T rubric check (Fast, Isolated, Repeatable, Self-validating, Timely) runs automatically after each green cycle
- **`delegate-task`** — single complex task with two-stage review before the test gate
- **`dispatch-agents`** — independent tasks from `specs/plan/tasks.md` run in parallel subagents
- **`execute-plan`** — reads `specs/plan/tasks.md` and executes step by step with checkpoints
- **`wire-observability`** — optional harden step: adds structured JSON logging before the test gate (when spec requires observability NFR)
</bigpowers_skills>

<process>
**Phase 1 — Setup**

1. Read `specs/plan/tasks.md` for the ordered task list.
2. Read the current spec for acceptance criteria and step → verify pairs.
3. Read `AGENT.md` and `CONVENTIONS.md` for project conventions, naming, and code standards.
4. Run **`execute-plan`** — reads `specs/plan/tasks.md` and manages step-by-step execution with checkpoints.

**Phase 2 — Per-task TDD loop**

For each task (or batch of independent tasks via **`dispatch-agents`**):

5. Run **`develop-tdd`** — enforce red → green → refactor:
   - **Red:** write the failing test for the behavior slice first.
   - **Green:** implement the minimum correct code to make it pass.
   - **Refactor:** clean up to meet `CONVENTIONS.md` without breaking the test.
6. Run **`enforce-first`** — F.I.R.S.T rubric check after each green cycle. Address any violations before moving to the next task.
7. For complex tasks where cross-task review is valuable, run **`delegate-task`** — two-stage review (self-review + fresh-agent review) before continuing.

**Phase 3 — Harden (optional)**

8. If the spec specifies observability NFRs (structured logging, tracing), run **`wire-observability`** — adds structured JSON logging to key paths before the test gate.

**Phase 4 — Pre-test self-review**

9. Run a final self-review against `CONVENTIONS.md` and the spec's acceptance criteria. Verify all step → verify pairs from the spec can be demonstrated.
10. Commit all changes: `feat(<scope>): <description>` (or appropriate conventional commit type per task).

**Record**

11. Update `specs/project/PROJECT_STATUS.md` with implementation summary: tasks completed, TDD cycles, commit references, and pointer to `specs/plan/tasks.md`.
</process>

<anti_patterns>
- Writing tests after the implementation and calling it TDD.
- Skipping the refactor phase after green.
- Skipping F.I.R.S.T checks — a test that is slow, order-dependent, or non-deterministic will cause `acps.test` to be unreliable.
- Implementing beyond the spec's acceptance criteria (gold-plating).
- Committing without a conventional commit message.
</anti_patterns>

<success_criteria>
All tasks in `specs/plan/tasks.md` are implemented. Every behavior slice has a test that was written before the implementation (or documented exception). F.I.R.S.T rubric passed per task. Code meets `CONVENTIONS.md` standards. All step → verify pairs from the spec are demonstrable. `specs/project/PROJECT_STATUS.md` updated with implementation summary.
</success_criteria>
