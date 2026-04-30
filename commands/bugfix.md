---
description: "Record and fix failures in .specify/bugs/"
---

# speckit.acps.bugfix

User arguments: `$ARGUMENTS`

<objective>
When tests or UAT fail, record the bug, find root cause, fix it, and verify. Produce `.specify/bugs/BUG[NNN]-[slug].md` (allocate the next `NNN` and a short kebab-case `slug` from the failure). Return to `speckit.acps.test` after the fix path completes.
</objective>

<context>
**Entered from** GW_TestsOk (no) or GW_UATOk (no). **Returns to** `speckit.acps.test` for verification after fix.

**Bug states (workflow)**: `TDO` → `WIP` → `REV` → `TST` → `FIX` (adjust labels in the bug file header/table as the project standardizes them; preserve traceability).
</context>

<core_principle>
**ROOT CAUSE BEFORE FIX.** Do not write fix code until you understand why the behavior broke (not merely what failed). Symptoms are clues; the fix must address the actual failure mechanism.
</core_principle>

<process>
**Phase 1 — Triage**

1. Gather context: read `TEST_SUMMARY.md` and/or the UAT report for failure details, stack traces, and reproduction hints.
2. **Reproduce**: identify the **minimal** reproduction path (one command, one scenario, or one UI path).
3. **Root cause analysis**: trace to the actual root cause (not the symptom). Use `git blame`, history, and code reading as needed.
4. **Blast radius**: list what else could be affected (callers, configs, similar code paths).
5. Write `.specify/bugs/BUG[NNN]-[slug].md` including: root cause (current best understanding), reproduction steps, affected files, proposed fix approach. Set status: **TDO**.
6. **Gate**: present triage to the user for confirmation before writing fix code (unless the user has delegated full autonomy in `$ARGUMENTS`).

**Phase 2 — Fix**

7. Implement the fix. Update bug status to **WIP**.
8. Write or update tests that reproduce the original bug (test **fails** without the fix, **passes** with it).
9. Commit with message: `fix(<scope>): <description>`.

**Phase 3 — Verify**

10. Run the **full** test suite using the same discipline as `speckit.acps.test` (framework detection, capture output, `TEST_SUMMARY.md`).
11. If tests pass with evidence, update bug status to **FIX**.
12. If tests still fail, loop back to Phase 1 triage with new evidence (do not mark FIX).

**Phase 4 — Record**

13. Update `.specify/bugs/BUG[NNN]-[slug].md` with fix summary, links/commits, and verification evidence (quote key output lines).
14. Update `.specify/project/PROJECT_STATUS.md` with bug id, resolution state, and pointer to the bug file and latest `TEST_SUMMARY.md`.
</process>

<anti_patterns>
- Jumping to code changes before root cause is understood.
- Skipping verification or accepting "probably fine" without `speckit.acps.test`-level evidence.
- Opening a **second** bug file for the same underlying failure source while the first is still open — extend or supersede the existing record instead.
- Closing a bug as FIX without quoted verification evidence.
</anti_patterns>

<success_criteria>
- Bug file exists under `.specify/bugs/` with root cause, reproduction, fix notes, and **FIX** status when done.
- Tests pass after fix with **evidence quoted** (via refreshed `TEST_SUMMARY.md` or equivalent).
- Only **one** open bug record per failure source; duplicates reconciled.
- `.specify/project/PROJECT_STATUS.md` updated.
</success_criteria>
