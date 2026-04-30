---
description: "Update documentation and refresh AGENT.md"
---

# speckit.acps.docs

User arguments: `$ARGUMENTS`

<objective>
Update project documentation to reflect the **current** true state. Refresh `AGENT.md` as the **single entrypoint** for agents and new contributors. Ensure docs work for a reader who has no prior context from this effort.
</objective>

<context>
**Runs after** UAT passes (GW_UATOk **yes**). **Before** scope review (**GW_ScopeTrigger**). Inputs: specs, implementation, `TEST_SUMMARY.md`, UAT artifacts, and `$ARGUMENTS` for emphasis (e.g. audience, milestone).
</context>

<core_principle>
**WRITE FOR THE FRESH READER.** The test: *Can someone who has never seen this codebase take the correct next actions after reading this?* Optimize for that, not for narrating what you built in chat.
</core_principle>

<process>
**Stage 1 — Context**

1. Identify **what changed**: recent specs, code, tests, and verification artifacts (`TEST_SUMMARY.md`, UAT file).
2. Determine **the reader**: developer onboarding, API consumer, operator, end user, or other — one primary reader per doc pass (note secondary readers if needed).
3. Determine **post-read action**: the single concrete outcome the reader should be able to perform (e.g. run locally, call an endpoint, deploy, extend a module).

**Stage 2 — Refine**

4. Update **`AGENT.md`** with current project state: stack, key modules, conventions, how to run/test, and **recent changes** (high level, durable facts).
5. Update or create relevant docs: `README` sections, API docs, architecture notes — only where they serve the post-read action.
6. **Draft outlines first**, not prose. Fix structure (headings, flow) before filling paragraphs.

**Stage 3 — Reader-test**

7. **Cold-read** each updated doc top to bottom. At every implicit *"they already know X"* — **fill the gap** or add a link to authoritative detail.
8. Verify the **named post-read action** is achievable **from the doc alone** (or doc + standard tool installs with versions noted).
9. **Cut** anything that does not serve the post-read action or the fresh reader (historical play-by-play, stale commands).

10. **Update** `.specify/project/PROJECT_STATUS.md` with doc refresh date and pointers to `AGENT.md` / key docs touched.
</process>

<anti_patterns>
- Writing a **summary of the journey** instead of instructions for the **destination**.
- Putting **file paths with line numbers** in long-lived "trunk" docs — they rot; point to modules and stable anchors.
- Skipping the **cold-read** pass.
- Claiming "docs updated" without reader-testing the post-read action.
- Duplicating large specs inside `AGENT.md` instead of linking + summarizing.
</anti_patterns>

<success_criteria>
- `AGENT.md` reflects current stack, entrypoints, and conventions.
- Docs name **one primary reader** and **one post-read action** per major updated surface; cold-read gaps closed.
- No **orphaned** docs (no broken links, no zombie sections that contradict code).
- `.specify/project/PROJECT_STATUS.md` updated.
</success_criteria>
