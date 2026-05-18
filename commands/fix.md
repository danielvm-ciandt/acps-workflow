---
description: "Root-cause triage, fix, and re-verify failures from specs/bugs/"
command: acps.fix
---

<objective>
When tests or UAT fail, record the bug, find root cause, fix it, and verify. Produce `specs/bugs/BUG[NNN]-[slug].md` (allocate the next `NNN` and a short kebab-case `slug` from the failure). Return to `acps.test` after the fix path completes.
</objective>

<context>
**Entered from** `GW_TestsOk` (no) or `GW_UATOk` (no). **Returns to** `acps.test` for verification after fix.

**Bug states (workflow):** `TDO` → `WIP` → `REV` → `TST` → `FIX` (adjust labels in the bug file as the project standardizes them; preserve traceability).
</context>

<core_principle>
**ROOT CAUSE BEFORE FIX.** Do not write fix code until you understand why the behavior broke (not merely what failed). Symptoms are clues; the fix must address the actual failure mechanism.
</core_principle>

<bigpowers_skills>
Sub-routines invoked by this command:

- **`investigate-bug`** — Phase 1 triage: structured investigation → `specs/bugs/BUG[NNN].md` (maps to ACPS bug file)
- **`diagnose-root`** — 4-phase root cause: reproduce → isolate → hypothesize → verify
- **`validate-fix`** — Phase 3 verify: re-runs the originally failing test then the full suite; appends evidence to the bug file
</bigpowers_skills>

<process>
**Phase 1 — Triage**

1. Gather context: read `specs/TEST_SUMMARY.md` and/or the UAT report for failure details, stack traces, and reproduction hints.
2. Run **`investigate-bug`** — structured investigation pass; produces initial `specs/bugs/BUG[NNN]-[slug].md`.
3. Run **`diagnose-root`** — 4-phase root cause analysis (reproduce → isolate → hypothesize → verify). Update the bug file with confirmed root cause.
4. **Blast radius:** list what else could be affected (callers, configs, similar code paths).
5. Update `specs/bugs/BUG[NNN]-[slug].md` with: confirmed root cause, reproduction steps, affected files, proposed fix approach. Set status: **TDO**.
6. **Gate:** present triage to the user for confirmation before writing fix code (unless the user has delegated full autonomy in `$ARGUMENTS`).

**Phase 2 — Fix**

7. Implement the fix. Update bug status to **WIP**.
8. Write or update tests that reproduce the original bug (test **fails** without the fix, **passes** with it).
9. Commit with message: `fix(<scope>): <description>`.

**Phase 3 — Verify**

10. Run **`validate-fix`** — runs the originally failing test first (proves regression is fixed), then runs the full test suite + typecheck + lint with captured evidence. Updates bug file with verification output.
11. If tests pass with evidence, update bug status to **FIX**.
12. If tests still fail, loop back to Phase 1 triage with new evidence (do not mark FIX).

**Phase 4 — Record**

13. Update `specs/bugs/BUG[NNN]-[slug].md` with fix summary, commits, and verification evidence (quote key output lines).
14. Update `specs/project/PROJECT_STATUS.md` with bug id, resolution state, and pointer to the bug file and latest `specs/TEST_SUMMARY.md`.
</process>

<anti_patterns>
- Jumping to code changes before root cause is understood.
- Skipping verification or accepting "probably fine" without `acps.test`-level evidence.
- Opening a **second** bug file for the same underlying failure source while the first is still open — extend or supersede the existing record instead.
- Closing a bug as FIX without quoted verification evidence.
</anti_patterns>

<success_criteria>
- Bug file exists under `specs/bugs/` with root cause, reproduction, fix notes, and **FIX** status when done.
- Tests pass after fix with **evidence quoted** (via refreshed `specs/TEST_SUMMARY.md`).
- Only **one** open bug record per failure source; duplicates reconciled.
- `specs/project/PROJECT_STATUS.md` updated.
</success_criteria>
