---
description: "UAT execution + docs update (Phase 3)"
command: acps.uat
---

<objective>
Define and execute UAT for the current spec or milestone. Produce `specs/uat/[milestone]-uat.md` with structured test cases, per-check evidence, and an overall verdict. Then (Phase 3) update project documentation to reflect the current true state. Align filename `[milestone]` with `specs/BACKLOG.md`, `specs/RELEASE_PLAN.md`, or the active spec identifier.
</objective>

<context>
**Entered after** `acps.test` passes (`GW_TestsOk` yes). **Feeds** `GW_UATOk` (yes → `acps.release`, no → `acps.fix`). Phase 3 (docs) runs only after UAT PASS.
</context>

<core_principle>
**Prove the check honestly.** Do not collapse live or behavioral checks into cheap artifact-only checks just to obtain PASS. Choose the lightest mode that still constitutes a real proof of the acceptance criterion. Write docs for the fresh reader, not as a journey narrative.
</core_principle>

<bigpowers_skills>
Sub-routines invoked by this command:

- **`define-success`** — re-reads step → verify pairs authored in `acps.spec`; writes new ones for edge cases not previously covered
- **`trace-requirement`** — each UAT check is traced back to its originating spec requirement; traceability links recorded in the UAT file
- **`edit-document`** — Phase 3 docs update: restructures `AGENT.md` and relevant docs after UAT passes
</bigpowers_skills>

<process>
**Phase 1 — UAT Plan**

1. **Determine UAT scope:** read the current spec and acceptance criteria from `specs/BACKLOG.md`, `specs/RELEASE_PLAN.md`, linked spec files, or `$ARGUMENTS`.
2. Run **`define-success`** — re-read step → verify pairs from the spec; write new ones for edge cases. These become the UAT test case table.
3. Run **`trace-requirement`** — confirm each check links to a spec requirement; record traceability references.
4. **Select UAT mode** per check (pick the **lightest** that proves the check honestly):
   - **artifact-driven** — build outputs, configs, generated files, API contracts
   - **live-runtime** — running services, jobs, integrations under realistic config
   - **browser-executable** — automated browser or scripted UI checks
   - **human-experience** — subjective UX, copy, trustworthiness of flows
   - **mixed** — combine modes per check
5. Write the UAT plan in `specs/uat/[milestone]-uat.md` with sections:
   - **UAT Type** (mode summary)
   - **Preconditions** (data, env, accounts, flags)
   - **Smoke Test** (fast sanity path)
   - **Test Cases** (numbered steps + expected results, with spec traceability)
   - **Edge Cases**
   - **Failure Signals** (what FAIL looks like)

**Phase 2 — Execute and Verdict**

6. **Execute each check.** For each, record:
   - Description
   - **Evidence mode** (artifact / runtime / human-follow-up)
   - Command or action taken
   - Actual result (facts, not vibes)
   - Verdict: **PASS** / **FAIL** / **NEEDS-HUMAN**
7. **Results table:** `Check | Mode | Result | Notes`
8. **Overall verdict:**
   - **PASS** — all automatable checks passed
   - **FAIL** — any automatable check failed
   - **PARTIAL** — inconclusive (e.g. blocking NEEDS-HUMAN or missing preconditions)
9. **Gate — state explicitly:**
   - If PASS: proceed to Phase 3 (docs).
   - If FAIL or PARTIAL: `UAT accepted: NO → proceed to acps.fix`. Do not run Phase 3.
10. Update `specs/project/PROJECT_STATUS.md` with milestone id, UAT file path, overall verdict, and date.

**Phase 3 — Docs (runs only after UAT PASS)**

11. Run **`edit-document`** to structure the docs update pass.
12. Identify **what changed:** recent specs, code, tests, and verification artifacts (`specs/TEST_SUMMARY.md`, UAT file).
13. Determine **the reader:** developer onboarding, API consumer, operator, end user. One primary reader per doc pass.
14. Determine **post-read action:** the single concrete outcome the reader should be able to perform after reading.
15. Update **`AGENT.md`** at repo root with current project state: stack, key modules, conventions, how to run/test, and recent changes (high level, durable facts).
16. Update or create relevant docs (`README` sections, API docs, architecture notes) only where they serve the post-read action.
17. **Draft outlines first**, not prose. Fix structure before filling paragraphs.
18. **Cold-read** each updated doc top to bottom. Fill every implicit "they already know X" gap.
19. Verify the **named post-read action** is achievable from the doc alone.
20. **Cut** anything that does not serve the post-read action or the fresh reader (historical play-by-play, stale commands).
21. Update `specs/project/PROJECT_STATUS.md` with doc refresh date and pointers to `AGENT.md` and key docs touched.
22. **State the gateway outcome:** `UAT accepted: YES → proceed to acps.release`.
</process>

<anti_patterns>
**UAT:**
- Inventing subjective PASS where judgment belongs to a human — use NEEDS-HUMAN.
- Skipping precondition checks.
- Running Phase 3 (docs) before UAT PASS.
- Retrying the same failing check without new evidence or a changed hypothesis.

**Docs:**
- Writing a **summary of the journey** instead of instructions for the **destination**.
- Putting file paths with line numbers in trunk docs — they rot.
- Skipping the cold-read pass.
- Claiming "docs updated" without reader-testing the post-read action.
</anti_patterns>

<success_criteria>
- UAT file exists under `specs/uat/` with structured checks, results table, traceability links, and a clear overall verdict.
- Each check has recorded evidence; NEEDS-HUMAN items are separated.
- Phase 3: `AGENT.md` reflects current stack and conventions; docs name one primary reader and one post-read action per surface; cold-read gaps closed.
- `specs/project/PROJECT_STATUS.md` updated for both UAT and docs phases.
- Gateway line spoken: YES → `acps.release` or NO → `acps.fix`.
</success_criteria>
