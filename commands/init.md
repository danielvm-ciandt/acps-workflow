---
description: "Bootstrap project environment, governance, and specs/ structure"
command: acps.init
---

<objective>
Check prerequisites, detect the project stack and supporting tooling, establish project governance (principles, quality gates, non-negotiables), and create foundational artifacts: `AGENT.md`, `specs/setup/environment-check.md`, `specs/CONSTITUTION.md`, and `specs/project/PROJECT_STATUS.md`. Establish a clean, visible baseline so all subsequent ACPS commands run against a verified environment with agreed-upon rules.
</objective>

<context>
This is the first command in the ACPS workflow. It runs before `acps.backlog`. It assumes a repository exists. Use `$ARGUMENTS` for optional paths, overrides, monorepo package roots, or explicit governance constraints; otherwise infer everything from the repository.
</context>

<core_principle>
Do not proceed until prerequisites pass and governance is agreed. Environment verification and constitution are both blocking: document every check, surface FAIL state clearly, and only continue with explicit user consent when FAIL items exist.
</core_principle>

<bigpowers_skills>
Sub-routines invoked by this command (skills produce artifacts; this command owns gateway decisions):

- **`map-codebase`** — produces `specs/CONTEXT.md`: richer codebase context than environment-check alone
- **`survey-context`** — reads `specs/` at session start to orient the agent (also runs as session preamble on every command)
- **`seed-conventions`** — generates `AGENT.md` + `CONVENTIONS.md` and creates the `specs/` directory structure
- **`model-domain`** — optional: produces domain model and `specs/adr/` entries for greenfield projects
- **`define-language`** — optional: produces `specs/UBIQUITOUS_LANGUAGE.md` for ambiguous or domain-rich projects
- **`hook-commits`** — installs pre-commit hooks (lint/format/typecheck/test) during project init
- **`guard-git`** — installs destructive-command guard hook during project init
- **`session-state`** — initializes the workflow state snapshot in `specs/project/PROJECT_STATUS.md`; updated by every subsequent ACPS command
</bigpowers_skills>

<process>
**Phase 1 — Environment check**

1. Detect project stack by scanning for common manifests: `package.json`, `Cargo.toml`, `pyproject.toml`, `go.mod`, `pom.xml`, `build.gradle`, `Gemfile`, `composer.json`, `*.csproj`, `mix.exs`, etc. Record what was found and what was not.
2. Check for CI configuration: `.github/workflows/`, `.gitlab-ci.yml`, `Jenkinsfile`, `azure-pipelines.yml`, CircleCI config, or similar. Note absence as WARN if delivery expectations are unclear.
3. Check for `AGENT.md`, `README.md`, and `.env.example` (or equivalent env template). Note presence for onboarding and secret-handling discipline.
4. Run **`map-codebase`** → produces `specs/CONTEXT.md`.
5. Run **`seed-conventions`** → creates `specs/` directory structure, generates initial `AGENT.md` and `CONVENTIONS.md` draft.
6. Write `specs/setup/environment-check.md` with a PASS / WARN / FAIL checklist table: at least five rows covering stack, CI, agent entrypoints, specs/ layout, and one more relevant check (e.g. lockfile, Dockerfile, test runner). Include short evidence (path or command) and notes per row.
7. **Gate:** If any checklist row is FAIL, warn the user, list FAIL items, and ask whether to proceed anyway or fix prerequisites first. Record the decision in `specs/project/PROJECT_STATUS.md`.

**Phase 2 — Governance (Constitution)**

8. Read existing product docs, README, and `$ARGUMENTS` for principles, goals, and constraints.
9. Present a draft constitution to the user covering: project purpose, core principles, quality gates, non-negotiables (security, compliance, performance), and definition-of-done. Ask for confirmation or amendments.
10. Write `specs/CONSTITUTION.md` after user confirmation.
11. **Optional:** Run **`model-domain`** for greenfield projects to produce a domain model and seed `specs/adr/`.
12. **Optional:** Run **`define-language`** for domain-rich projects to produce `specs/UBIQUITOUS_LANGUAGE.md`.

**Phase 3 — Hooks and session state**

13. Run **`hook-commits`** → installs pre-commit lint/format/typecheck/test hooks.
14. Run **`guard-git`** → installs destructive-command guard hook.
15. Run **`session-state`** → writes initial workflow state snapshot to `specs/project/PROJECT_STATUS.md` with timestamp, command `acps.init`, outcomes summary, and pointers to all created artifacts.
16. Create or finalize `AGENT.md` at repo root (merged from `seed-conventions` draft + detected stack details). If `AGENT.md` already exists, read it fully before editing; merge new facts without discarding intentional team rules.
</process>

<anti_patterns>
Do not skip stack detection and assume a default toolchain. Do not overwrite `AGENT.md` without reading the existing file first. Do not skip constitution — governance must be agreed before backlog work starts. Do not silently proceed past FAIL environment checks. Do not embed governance notes inside `AGENT.md`; they belong in `specs/CONSTITUTION.md`.
</anti_patterns>

<success_criteria>
`specs/setup/environment-check.md` exists with at least five PASS/WARN/FAIL rows. `specs/CONSTITUTION.md` exists and user confirmed the content. `AGENT.md` at repo root names the detected stack and primary tooling. `CONVENTIONS.md` exists. `specs/project/PROJECT_STATUS.md` has an initial entry for this init run. Pre-commit and guard hooks are installed. Any FAIL gating decision is documented.
</success_criteria>
