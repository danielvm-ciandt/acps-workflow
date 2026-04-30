---
description: "Bootstrap project environment, create AGENT.md and PROJECT_STATUS.md"
command: speckit.acps.setup
---

<objective>
Check prerequisites, detect the project stack and supporting tooling, and create foundational artifacts: `AGENT.md`, `.specify/setup/environment-check.md`, and `.specify/project/PROJECT_STATUS.md`. Establish a clean baseline so later ACPS commands run against a verified environment.
</objective>

<context>
This is the first command in the ACPS workflow. It runs before `/speckit.constitution`. It assumes `specify init` has already been executed so `.specify/` exists. Use `$ARGUMENTS` only if the user passes optional paths, overrides, or notes; otherwise infer everything from the repository.
</context>

<core_principle>
Do not proceed until prerequisites pass. Treat environment verification as blocking: document every check, surface FAIL state clearly, and only continue past the gate with explicit user consent when FAIL items exist.
</core_principle>

<process>
1. Verify the `.specify/` directory exists (evidence of `specify init`). If it is missing, stop and instruct the user to run `specify init` first; do not create partial state that pretends Specify is initialized.
2. Detect project stack by scanning for common manifests and entry signals: `package.json`, `Cargo.toml`, `pyproject.toml`, `go.mod`, `pom.xml`, `build.gradle`, `Gemfile`, `composer.json`, `*.csproj`, `mix.exs`, etc. Record what was found and what was not.
3. Check for CI configuration: `.github/workflows/`, `.gitlab-ci.yml`, `Jenkinsfile`, `azure-pipelines.yml`, CircleCI config, or similar. Note absence as WARN if delivery expectations are unclear.
4. Check for `AGENT.md`, `README.md`, and `.env.example` (or equivalent env template). Note presence for onboarding and secret-handling discipline.
5. Write `.specify/setup/environment-check.md` containing a PASS / WARN / FAIL checklist table with one row per checked item, short evidence (path or command), and notes. Include at least five distinct checklist rows covering stack, CI, agent entrypoints, Specify layout, and one more relevant check (e.g. lockfile, Dockerfile, or test runner).
6. Create or update `AGENT.md` as the single entrypoint for agents: project name, detected stack, key paths (source, tests, specs, CI), naming conventions, how to run tests/build, and pointers into `.specify/`. If `AGENT.md` already exists, read it fully before editing; merge new facts without discarding intentional team rules.
7. Create `.specify/project/PROJECT_STATUS.md` as a running ledger. Add an initial entry with timestamp (or session id), command id `speckit.acps.setup`, summary of outcomes, and pointers to `environment-check.md` and `AGENT.md`.
8. **Gate:** If any checklist row is FAIL, warn the user, list FAIL items, and ask whether to proceed anyway or fix prerequisites first. Do not claim success until the user chooses; if they defer fixes, record that decision in `PROJECT_STATUS.md`.
If `$ARGUMENTS` is non-empty, treat it as extra paths to inspect or constraints (e.g. monorepo package root) and reflect that in the checklist notes.
</process>

<anti_patterns>
Do not skip stack detection and assume a default toolchain. Do not overwrite `AGENT.md` without reading the existing file first. Do not create `PROJECT_STATUS.md` entries that omit what was verified or skipped. Do not silently ignore a missing `.specify/` directory.
</anti_patterns>

<success_criteria>
`.specify/setup/environment-check.md` exists and includes at least five checklist items in a PASS/WARN/FAIL table. `AGENT.md` exists and explicitly names the detected project stack and primary tooling. `.specify/project/PROJECT_STATUS.md` exists and contains an initial entry for this setup run. Any FAIL gating decision is documented per the gate step.
</success_criteria>
