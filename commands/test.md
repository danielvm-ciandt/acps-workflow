---
description: "Run tests with evidence; quality gate before UAT or after acps.fix"
command: acps.test
---

<objective>
Run the project's test suite, capture results with evidence, and produce `specs/TEST_SUMMARY.md`. This command is the quality gate — its output feeds `GW_TestsOk` (yes → `acps.uat`, no → `acps.fix`). When called after `acps.fix`, it also validates the fix specifically before the full suite.
</objective>

<context>
Invoke after `acps.implement` or after `acps.fix`. Downstream consumer: `acps.uat` expects a PASS verdict here. If this command was run against stale code, re-run before any gateway decision.
</context>

<core_principle>
**EVIDENCE BEFORE CLAIMS, ALWAYS.** "Tests passed earlier" is not evidence. Run them now, read the output, quote the results. No PASS verdict without fresh command output captured in this session.
</core_principle>

<bigpowers_skills>
Sub-routines invoked by this command:

- **`audit-code`** — self-review against `CONVENTIONS.md` + SOLID principles; runs before the formal test gate
- **`request-review`** — optional: fresh-agent code review gate (recommended for complex or risky changes)
- **`respond-review`** — applies must-fix findings from `request-review` before the formal test run
- **`validate-fix`** — when called after `acps.fix`: runs the originally failing test first (fix-specific verification), then the full suite + typecheck + lint with evidence
</bigpowers_skills>

<process>
**Phase 1 — Pre-test gates**

1. Run **`audit-code`** — self-review against `CONVENTIONS.md` + SOLID. Address any must-fix findings before running tests.
2. **Optional:** Run **`request-review`** (recommended for complex changes) — fresh-agent code review. If findings exist, run **`respond-review`** to apply must-fix items before the test run.

**Phase 2 — Test run**

3. **Detect test framework:** inspect the repo — e.g. `package.json` `scripts.test`, `pytest.ini` / `pyproject.toml`, `Cargo.toml`, Jest/Vitest configs, etc. Choose the canonical test entrypoint.
4. **If called after `acps.fix`:** Run **`validate-fix`** first — runs the originally failing test to confirm the regression is resolved, then proceeds to the full suite.
5. **Run the test command** from the correct working directory. Capture **full** stdout/stderr.
6. **Run the build command** if required for a truthful test run. Capture output.
7. **Run the linter** on changed files or project default scope. Use the project's configured linter/formatter check.

**Phase 3 — Analyze and record**

8. **Analyze results:** totals for passed, failed, skipped. For **each** failure: test name, file path, expected vs actual, short diagnosis.
9. **Staleness check:** if code changed after the test run finished, **re-run** tests before finalizing the summary.
10. **Write `specs/TEST_SUMMARY.md`** with sections:
    - **Test Framework** (detected + why)
    - **Command Run** (exact commands, cwd)
    - **Results** (passed / failed / skipped counts)
    - **Failures** (detailed, per failure)
    - **Build Status** (command + outcome)
    - **Lint Status** (scope + outcome)
    - **Verdict** — `PASS` or `FAIL`
    - **Evidence** — quoted output lines proving the verdict
11. Update `specs/project/PROJECT_STATUS.md` with test run timestamp, verdict, and pointer to `specs/TEST_SUMMARY.md`.
12. **State the gateway outcome** explicitly:
    - `Tests OK: YES → proceed to acps.uat` when verdict is PASS with fresh evidence, **or**
    - `Tests OK: NO → proceed to acps.fix` when verdict is FAIL or evidence is insufficient.
</process>

<anti_patterns>
- Saying "tests pass" without running them in this flow.
- Skipping or minimizing failures ("80/84 is fine").
- Using `tsc --noEmit` (or similar typecheck-only) when the project's real quality bar is a full build/test pipeline.
- Claiming build works without running the project's actual build command.
- Declaring PASS when lint or required build failed.
- Writing the summary from memory instead of from captured output.
</anti_patterns>

<success_criteria>
`specs/TEST_SUMMARY.md` exists and includes quoted **Evidence** tied to the verdict. Test command ran **after** the last relevant code change. Failures include **file** references and usable diagnosis; verdict is unambiguous **PASS** or **FAIL**. `specs/project/PROJECT_STATUS.md` updated. Gateway line spoken: YES → `acps.uat` or NO → `acps.fix`, consistent with the verdict.
</success_criteria>
