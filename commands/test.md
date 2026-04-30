---
description: "Run tests and produce TEST_SUMMARY.md with evidence"
---

# speckit.acps.test

User arguments: `$ARGUMENTS`

<objective>
Run the project's test suite, capture results with evidence, and produce `TEST_SUMMARY.md` at the repository root (or path agreed in project conventions). This command is the quality gate — its output feeds **GW_TestsOk** (yes → UAT, no → bugfix).
</objective>

<context>
Invoke after implementation changes or before promoting work through the ACPS gateway chain. Downstream consumers: `speckit.acps.uat` expects a PASS verdict here. If this command was run against stale code, re-run before any gateway decision.
</context>

<core_principle>
**EVIDENCE BEFORE CLAIMS, ALWAYS.** "Tests passed earlier" is not evidence. Run them now, read the output, quote the results. No PASS verdict without fresh command output captured in this session (or explicitly re-validated if staleness rules apply).
</core_principle>

<process>
1. **Detect test framework**: Inspect the repo — e.g. `package.json` `scripts.test`, `pytest.ini` / `pyproject.toml`, `Cargo.toml`, `go.mod`, Jest/Vitest configs, `pom.xml`, etc. Choose the canonical test entrypoint for this project.
2. **Run the test command** from the correct working directory. Capture **full** stdout/stderr (or the tool’s full log output).
3. **Run the build command** if the project defines one and it is required for a truthful test run (e.g. compile step, bundle). Capture output.
4. **Run the linter** on changed files (or project default scope) if applicable — use the project’s configured linter/formatter check, not a guessed one.
5. **Analyze results**: Totals for passed, failed, skipped. For **each** failure: test name, file path, expected vs actual (from output), short diagnosis (hypothesis level is OK if root cause not yet proven — that is bugfix’s job).
6. **Staleness check**: If code changed after the test run finished, **re-run** tests (and build/lint if applicable) before finalizing the summary.
7. **Write `TEST_SUMMARY.md`** with sections:
   - **Test Framework** (detected + why)
   - **Command Run** (exact commands, cwd)
   - **Results** (passed / failed / skipped counts)
   - **Failures** (detailed, per failure)
   - **Build Status** (command + outcome)
   - **Lint Status** (scope + outcome)
   - **Verdict** — `PASS` or `FAIL`
   - **Evidence** — quoted output lines (or fenced blocks) proving the verdict
8. **Update** `.specify/project/PROJECT_STATUS.md` with test run timestamp, verdict, and pointer to `TEST_SUMMARY.md`.
9. **State the gateway outcome** explicitly:
   - `Tests OK: YES → proceed to UAT` when verdict is PASS with fresh evidence, **or**
   - `Tests OK: NO → proceed to bugfix` when verdict is FAIL or evidence is insufficient.
</process>

<anti_patterns>
- Saying "tests pass" without running them in this flow.
- Skipping or minimizing failures ("80/84 is fine").
- Using `tsc --noEmit` (or similar typecheck-only) when the project’s real quality bar is a full build/test pipeline.
- Claiming build works without running the project’s actual build command.
- Declaring PASS when lint or required build failed.
- Writing the summary from memory instead of from captured output.
</anti_patterns>

<success_criteria>
- `TEST_SUMMARY.md` exists and includes quoted **Evidence** tied to the verdict.
- Test command ran **after** the last relevant code change (or re-ran after detecting staleness).
- Failures include **file** references and usable diagnosis; verdict is unambiguous **PASS** or **FAIL**.
- `.specify/project/PROJECT_STATUS.md` updated.
- Gateway line spoken: YES → UAT or NO → bugfix, consistent with the verdict.
</success_criteria>
