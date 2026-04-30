---
description: "User acceptance testing with verdict in .specify/uat/"
---

# speckit.acps.uat

User arguments: `$ARGUMENTS`

<objective>
Define and execute UAT for the current spec or milestone. Produce `.specify/uat/[milestone]-uat.md` with structured test cases, per-check evidence, and an overall verdict. Align filename `[milestone]` with `BACKLOG.md`, `RELEASE_PLAN.md`, or the active spec identifier.
</objective>

<context>
**Entered after** `speckit.acps.test` passes (GW_TestsOk **yes**). **Feeds** GW_UATOk (**yes** → docs via `speckit.acps.docs`, **no** → `speckit.acps.bugfix`).
</context>

<core_principle>
**Prove the check honestly.** Do not collapse live or behavioral checks into cheap artifact-only checks just to obtain PASS. Choose the lightest mode that still constitutes a real proof of the acceptance criterion.
</core_principle>

<process>
1. **Determine UAT scope**: read the current spec and acceptance criteria from `BACKLOG.md`, `RELEASE_PLAN.md`, linked specs, or `$ARGUMENTS`.
2. **Select UAT mode** (pick the **lightest** that still proves the check honestly):
   - **artifact-driven** — build outputs, configs, generated files, API contracts
   - **live-runtime** — running services, jobs, integrations under realistic config
   - **browser-executable** — automated browser or scripted UI checks where applicable
   - **human-experience** — subjective UX, copy, trustworthiness of flows
   - **mixed** — combine modes per check
3. **Write the UAT plan** in `.specify/uat/[milestone]-uat.md` with sections:
   - **UAT Type** (mode summary)
   - **Preconditions** (data, env, accounts, flags)
   - **Smoke Test** (fast sanity path)
   - **Test Cases** (numbered steps + expected results)
   - **Edge Cases**
   - **Failure Signals** (what FAIL looks like)
4. **Execute each check**. For each, record:
   - Description
   - **Evidence mode** (artifact / runtime / human-follow-up)
   - Command or action taken
   - Actual result (facts, not vibes)
   - Verdict: **PASS** / **FAIL** / **NEEDS-HUMAN**
5. **Overall verdict**:
   - **PASS** — all automatable checks passed
   - **FAIL** — any automatable check failed
   - **PARTIAL** — inconclusive (e.g. blocking NEEDS-HUMAN or missing preconditions)
6. **Results table**: `Check | Mode | Result | Notes`
7. **Gate** — state explicitly:
   - `UAT accepted: YES → proceed to docs` **or**
   - `UAT accepted: NO → proceed to bugfix`
8. **Update** `.specify/project/PROJECT_STATUS.md` with milestone id, UAT file path, overall verdict, and date.
</process>

<anti_patterns>
- Inventing subjective PASS where judgment belongs to a human — use **NEEDS-HUMAN**.
- Skipping precondition checks.
- Marking human-judgment checks as PASS without actual human confirmation.
- Retrying the same failing check without new evidence or a changed hypothesis (if retrying, document **why** this attempt differs).
- PASS overall verdict when any automatable check failed.
</anti_patterns>

<success_criteria>
- UAT file exists under `.specify/uat/` with structured checks and the results table.
- Each check has recorded evidence (commands, outputs, screenshots, or explicit NEEDS-HUMAN).
- Overall verdict is clear; **NEEDS-HUMAN** items are separated and do not masquerade as PASS.
- `.specify/project/PROJECT_STATUS.md` updated.
- Gateway line spoken: YES → docs or NO → bugfix, consistent with the verdict rules above.
</success_criteria>
