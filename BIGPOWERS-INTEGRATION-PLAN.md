# ACPS Workflow — Redesign & bigpowers Integration Plan

> **Scope of this plan:**
> 1. Migrate artifact repository from `.specify/` to `specs/`
> 2. Collapse the dual-namespace command set into a single lean `acps.*` namespace
> 3. Absorb bigpowers skills as sub-routines inside ACPS commands
>
> The result: **11 commands**, one namespace, one folder, fully integrated with bigpowers.

---

## Part 1 — Repository: `.specify/` → `specs/`

### Why migrate

The current `.specify/` directory is a hidden folder, which makes spec artifacts invisible in most file browsers and IDEs. bigpowers already uses `specs/` as its canonical output root. Aligning on `specs/` gives three immediate benefits:

- All project artifacts are visible without hidden-file toggles
- bigpowers skills write directly into the same tree with no path translation
- New contributors find the project context without knowing to look for hidden folders

### New directory layout

```
specs/
├── CONTEXT.md                  ← from map-codebase (bigpowers)
├── UBIQUITOUS_LANGUAGE.md      ← from define-language (bigpowers)
├── adr/                        ← architecture decisions
│
├── BACKLOG.md                  ← epic / feature list (was .specify/)
├── RELEASE_PLAN.md             ← baseline scope contract (was .specify/)
│
├── project/
│   ├── PROJECT_STATUS.md       ← running ledger across sessions
│   └── change-requests/        ← CR-001.md, CR-002.md, …
│
├── counting/                   ← BCP / FP / SNAP output files
├── bugs/                       ← BUG001-slug.md files
├── uat/                        ← milestone-uat.md files
├── scope/                      ← scope-impact-draft-DATE.md files
└── setup/                      ← environment-check.md
```

### Artifact path changes (reference)

| Old path | New path |
|----------|----------|
| `.specify/` | `specs/` |
| `.specify/project/PROJECT_STATUS.md` | `specs/project/PROJECT_STATUS.md` |
| `.specify/project/change-requests/` | `specs/project/change-requests/` |
| `.specify/bugs/` | `specs/bugs/` |
| `.specify/uat/` | `specs/uat/` |
| `.specify/scope/` | `specs/scope/` |
| `.specify/counting/` | `specs/counting/` |
| `.specify/setup/` | `specs/setup/` |
| `.specify/memory/constitution.md` | `specs/CONSTITUTION.md` |
| `.specify/plan/` | `specs/plan/` |
| `BACKLOG.md` (repo root) | `specs/BACKLOG.md` |
| `RELEASE_PLAN.md` (repo root) | `specs/RELEASE_PLAN.md` |
| `TEST_SUMMARY.md` (repo root) | `specs/TEST_SUMMARY.md` |
| `CHANGELOG.md` | stays at repo root |

> `AGENT.md` stays at repo root — it is the agent entrypoint and should be the first file an agent reads when entering the repo, not buried in a subdirectory.

### `acps-config.yml` update

```yaml
artifacts:
  specs_root: "specs"
  backlog_path: "specs/BACKLOG.md"
  release_plan_path: "specs/RELEASE_PLAN.md"
  test_summary_path: "specs/TEST_SUMMARY.md"
  project_status_path: "specs/project/PROJECT_STATUS.md"
```

---

## Part 2 — Command Names: Unified `acps.*` Namespace

### Why collapse namespaces

The current dual-namespace (`speckit.*` for core, `speckit.acps.*` for extension) made sense when ACPS was a thin layer on top of Spec Kit. Since all Spec Kit core commands are now absorbed into ACPS, the distinction is artificial. One product, one prefix.

### The old command inventory (19 commands across two namespaces)

```
speckit.constitution         speckit.acps.setup
speckit.specify              speckit.acps.create-epic-backlog
speckit.clarify              speckit.acps.release-plan
speckit.plan                 speckit.acps.plan-bridge
speckit.tasks                speckit.acps.test
speckit.analyze              speckit.acps.bugfix
speckit.implement            speckit.acps.uat
                             speckit.acps.docs
                             speckit.acps.scope
                             speckit.acps.release
                             speckit.acps.change-request
                             speckit.acps.count
```

### The new command inventory (11 commands, single namespace)

| New command | Absorbs | What changed |
|-------------|---------|-------------|
| `acps.init` | `setup` + `speckit.constitution` | Merged bootstrap + governance into one command |
| `acps.backlog` | `create-epic-backlog` | Renamed; same role |
| `acps.spec` | `speckit.specify` + `speckit.clarify` + `count` | Merged specify/clarify loop; count runs automatically after spec |
| `acps.baseline` | `release-plan` | Renamed; absorbs count aggregation from `acps.spec` outputs |
| `acps.plan` | `speckit.plan` + `plan-bridge` + `speckit.tasks` + `speckit.analyze` | Full planning in one command: tech plan → bridge → tasks → optional analysis |
| `acps.implement` | `speckit.implement` | Renamed; TDD discipline added |
| `acps.test` | `test` | Renamed; gains post-bugfix validation mode |
| `acps.fix` | `bugfix` | Renamed; structured root-cause protocol added |
| `acps.uat` | `uat` + `docs` | Merged: docs update follows immediately after UAT passes |
| `acps.release` | `scope` + `release` | Merged: scope review is the first phase of release packaging |
| `acps.cr` | `change-request` | Renamed; structured impact assessment added |

### Slash-command mapping (tooling reference)

```
/acps.init          → was /workflow.setup + /speckit.constitution
/acps.backlog       → was /workflow.create-epic-backlog
/acps.spec          → was /speckit.specify + /speckit.clarify
/acps.baseline      → was /workflow.release-plan
/acps.plan          → was /speckit.plan + /workflow.plan-bridge + /speckit.tasks + /speckit.analyze
/acps.implement     → was /speckit.implement
/acps.test          → was /workflow.test
/acps.fix           → was /workflow.bugfix
/acps.uat           → was /workflow.uat + /workflow.docs
/acps.release       → was /workflow.scope + /workflow.release
/acps.cr            → was /workflow.change-request
```

> **`acps.count` is removed as a standalone command.** Counting now runs automatically as the final step of `acps.spec` (hook: `after_spec`). Results land in `specs/counting/` as before and are read by `acps.baseline` for sizing. Users can still call `acps.spec --count-only` to recount without re-specifying.

---

## Part 3 — Updated Workflow State Machine

The linearized state machine now uses the new command names. The parallel change-request track is unchanged except for renaming.

| State | Command | Transitions |
|-------|---------|-------------|
| `Start` | — | → `Task_Init` |
| `Task_Init` | `acps.init` | → `Task_Backlog` |
| `Task_Backlog` | `acps.backlog` | → `GW_SpecsRemaining` |
| `GW_SpecsRemaining` | — | **yes** → `Task_Spec` · **no** → `Task_Baseline` |
| `Task_Spec` | `acps.spec` | → `GW_SpecsRemaining` |
| `Task_Baseline` | `acps.baseline` | → `GW_EnterPipeline` |
| `GW_EnterPipeline` | — | **pipeline** → `Task_Plan` |
| `Task_Plan` | `acps.plan` | → `Task_Implement` |
| `Task_Implement` | `acps.implement` | → `Task_Test` |
| `Task_Test` | `acps.test` | → `GW_TestsOk` |
| `GW_TestsOk` | — | **yes** → `Task_UAT` · **no** → `Task_Fix` |
| `Task_Fix` | `acps.fix` | → `Task_Test` |
| `Task_UAT` | `acps.uat` | → `GW_UATOk` |
| `GW_UATOk` | — | **yes** → `Task_Release` · **no** → `Task_Fix` |
| `Task_Release` | `acps.release` | → `GW_EpicComplete` |
| `GW_EpicComplete` | — | **yes** → `GW_MoreWork` · **no** → `Task_Plan` |
| `GW_MoreWork` | — | **yes** → `Task_Backlog` · **no** → `End` |
| `End` | — | (terminal) |
| `Task_CR` *(parallel)* | `acps.cr` | — |

> **Note:** `acps.uat` now covers both UAT execution and docs update. The `GW_ScopeTrigger` gateway is removed — scope review is always the first phase of `acps.release`, not a separate optional branch.

---

## Part 4 — bigpowers Integration Map

Each bigpowers skill is now assigned to a specific ACPS command. Skills are sub-routines: they produce evidence or artifacts; ACPS commands own the gateway decisions and `specs/project/PROJECT_STATUS.md` updates.

### `acps.init`
*Absorbs: `setup` + `constitution`*

| bigpowers skill | Where used |
|----------------|-----------|
| `map-codebase` | Produces `specs/CONTEXT.md` — richer context than current environment-check alone |
| `survey-context` | Reads `specs/` on every session start to orient the agent (not just init — runs as session preamble) |
| `seed-conventions` | Generates `AGENT.md` + `CONVENTIONS.md` + creates `specs/` directory structure |
| `model-domain` | Optional: produces domain model + `specs/adr/` for greenfield projects |
| `define-language` | Optional: produces `specs/UBIQUITOUS_LANGUAGE.md` for ambiguous domains |
| `hook-commits` | Installs pre-commit hooks (lint/format/typecheck/test) during project init |
| `guard-git` | Installs destructive-command guard hook during project init |
| `session-state` | Initializes workflow state snapshot; updated by every subsequent command |

---

### `acps.backlog`
*Absorbs: `create-epic-backlog`*

| bigpowers skill | Where used |
|----------------|-----------|
| `grill-me` | Before decomposing requirements, challenges assumptions one question at a time |
| `elaborate-spec` | Refines vague product ideas via dialogue before writing backlog items |

---

### `acps.spec`
*Absorbs: `specify` + `clarify` + `count`*

| bigpowers skill | Where used |
|----------------|-----------|
| `grill-me` | Surfaces hidden assumptions before the spec is written |
| `elaborate-spec` | Structured refinement dialogue for unclear or thin specs |
| `define-success` | Converts acceptance criteria into step → verify pairs; these become the test cases for `acps.uat` |
| `trace-requirement` | Traces the spec back to its backlog item; records traceability in the spec file |
| *(count)* | BCP / FP / SNAP counting runs automatically after the spec is written (`after_spec` hook); outputs to `specs/counting/` |

---

### `acps.baseline`
*Absorbs: `release-plan`*

| bigpowers skill | Where used |
|----------------|-----------|
| `plan-release` | Milestone structuring and risk framing; supplements ACPS sizing with bigpowers release planning lens |

---

### `acps.plan`
*Absorbs: `plan` + `plan-bridge` + `tasks` + `analyze`*

| bigpowers skill | Where used |
|----------------|-----------|
| `deepen-architecture` | Architecture depth before writing the technical plan; updates `specs/CONTEXT.md` |
| `design-interface` | API shape proposals via parallel subagents when the spec involves new interfaces |
| `spike-prototype` | Optional: throw-away spike for uncertain domains; produces `specs/SPIKE-<name>.md` before planning |
| `scope-work` | Feeds feature scope into the plan's `specs/SCOPE.md` section |
| `plan-work` | Generates the implementation plan with step → verify pairs |

---

### `acps.implement`
*Absorbs: `implement`*

| bigpowers skill | Where used |
|----------------|-----------|
| `develop-tdd` | Mandates red → green → refactor discipline per behavior slice |
| `enforce-first` | F.I.R.S.T rubric check runs automatically after each green cycle |
| `delegate-task` | Single complex task with two-stage review before the test gate |
| `dispatch-agents` | Independent tasks from the plan run in parallel |
| `execute-plan` | Reads `specs/PLAN.md` and executes step by step with checkpoints |
| `wire-observability` | Optional harden step: structured JSON logging added before test gate |

---

### `acps.test`
*Absorbs: `test`*

| bigpowers skill | Where used |
|----------------|-----------|
| `audit-code` | Self-review against `CONVENTIONS.md` + SOLID runs before the formal test gate |
| `request-review` | Fresh-agent code review gate (optional, recommended for complex changes) |
| `respond-review` | Applies must-fix findings from review before formal test run |
| `validate-fix` | When called after `acps.fix`: runs failing test first, then full suite + typecheck + lint with fix-verification evidence |

---

### `acps.fix`
*Absorbs: `bugfix`*

| bigpowers skill | Where used |
|----------------|-----------|
| `investigate-bug` | Phase 1 triage: structured investigation → `specs/bugs/BUG[NNN].md` (maps to ACPS bug file) |
| `diagnose-root` | 4-phase root cause: reproduce → isolate → hypothesize → verify |
| `validate-fix` | Phase 3 verify: re-runs failing test + full suite; appends evidence to bug file |

---

### `acps.uat`
*Absorbs: `uat` + `docs`*

| bigpowers skill | Where used |
|----------------|-----------|
| `define-success` | The step → verify pairs authored in `acps.spec` are re-read here; new ones written for edge cases |
| `trace-requirement` | Each UAT check is traced back to its originating spec requirement |
| `edit-document` | Docs update phase: restructures `AGENT.md` and relevant docs after UAT passes |

---

### `acps.release`
*Absorbs: `scope` + `release`*

| bigpowers skill | Where used |
|----------------|-----------|
| `assess-impact` | Phase 1 scope review: structured impact matrix before committing to release packaging |
| `inspect-quality` | Structured audit → `specs/BUG-LOG.md` before tagging |
| `commit-message` | Derives Conventional Commits-based semver bump from git log |
| `release-branch` | Creates PR with coverage gates, semver tag, and worktree cleanup |

---

### `acps.cr`
*Absorbs: `change-request`*

| bigpowers skill | Where used |
|----------------|-----------|
| `assess-impact` | Replaces the informal "preliminary impact" step with a structured change matrix |
| `trace-requirement` | Identifies which specs, plans, and test cases are affected by the change |

---

## Part 5 — What Gets Removed

### Commands removed
- **`speckit.plan-bridge`** — absorbed into `acps.plan` as a mandatory bridge phase (traceability matrix always produced)
- **`speckit.analyze`** — absorbed into `acps.plan` as an optional pre-implement review step
- **`speckit.clarify`** — absorbed into `acps.spec` as the built-in dialogue loop
- **`speckit.tasks`** — absorbed into `acps.plan`
- **`speckit.acps.docs`** — absorbed into `acps.uat` (docs follow immediately after UAT pass)
- **`speckit.acps.scope`** — absorbed into `acps.release` Phase 1
- **`speckit.acps.count`** as standalone — now an auto-step inside `acps.spec`

### Commands renamed only (no merge, no logic change)
- `setup` + `constitution` → `acps.init`
- `create-epic-backlog` → `acps.backlog`
- `specify` → `acps.spec`
- `release-plan` → `acps.baseline`
- `plan` → `acps.plan`
- `implement` → `acps.implement`
- `test` → `acps.test`
- `bugfix` → `acps.fix`
- `uat` → `acps.uat`
- `release` → `acps.release`
- `change-request` → `acps.cr`

---

## Part 6 — Files to Create / Modify

### Repository structure changes

| Action | File / Path |
|--------|------------|
| **Update** | `config/acps-config.template.yml` — all paths to `specs/` |
| **Update** | `memory/acps-methodology.md` — new command names, single namespace, `specs/` root |
| **Update** | `memory/acps-states.md` — new state machine with 11 commands |
| **Rename** | `commands/setup.md` → `commands/init.md` (merged with constitution logic) |
| **Rename** | `commands/create-epic-backlog.md` → `commands/backlog.md` |
| **Rename** | `commands/release-plan.md` → `commands/baseline.md` |
| **Rename + Merge** | `commands/bugfix.md` → `commands/fix.md` |
| **Rename + Merge** | `commands/uat.md` → absorbs `docs.md` content in Phase 3 |
| **Rename + Merge** | `commands/release.md` → absorbs `scope.md` as Phase 1 |
| **Delete** | `commands/docs.md` (absorbed into `commands/uat.md`) |
| **Delete** | `commands/scope.md` (absorbed into `commands/release.md`) |
| **Delete** | `commands/plan-bridge.md` (absorbed into `commands/plan.md`) |
| **Archive** | All `speckit.*` command headers → rename internal references to `acps.*` |

### Per-command bigpowers wiring (in each `commands/*.md`)

| Command file | bigpowers skills to reference |
|-------------|------------------------------|
| `commands/init.md` | map-codebase, survey-context, seed-conventions, model-domain, define-language, hook-commits, guard-git, session-state |
| `commands/backlog.md` | grill-me, elaborate-spec |
| `commands/spec.md` | grill-me, elaborate-spec, define-success, trace-requirement |
| `commands/baseline.md` | plan-release |
| `commands/plan.md` | deepen-architecture, design-interface, spike-prototype, scope-work, plan-work |
| `commands/implement.md` | develop-tdd, enforce-first, delegate-task, dispatch-agents, execute-plan, wire-observability |
| `commands/test.md` | audit-code, request-review, respond-review, validate-fix |
| `commands/fix.md` | investigate-bug, diagnose-root, validate-fix |
| `commands/uat.md` | define-success, trace-requirement, edit-document |
| `commands/release.md` | assess-impact, inspect-quality, commit-message, release-branch |
| `commands/cr.md` | assess-impact, trace-requirement |

---

## Part 7 — Implementation Sequence

```
PHASE A — Structure (do first, unblocks everything)
  A1. Update acps-config.template.yml (paths → specs/)
  A2. Rewrite memory/acps-methodology.md (new names, new namespace)
  A3. Rewrite memory/acps-states.md (new state machine)

PHASE B — Renames (mechanical, no logic change)
  B1. commands/setup.md → commands/init.md  (merge constitution logic)
  B2. commands/create-epic-backlog.md → commands/backlog.md
  B3. commands/release-plan.md → commands/baseline.md
  B4. commands/bugfix.md → commands/fix.md

PHASE C — Merges (command consolidations)
  C1. Absorb plan-bridge into commands/plan.md
  C2. Absorb clarify/analyze into commands/spec.md + commands/plan.md
  C3. Absorb tasks into commands/plan.md
  C4. Absorb docs into commands/uat.md (new Phase 3)
  C5. Absorb scope into commands/release.md (new Phase 1)
  C6. Remove commands/docs.md, commands/scope.md, commands/plan-bridge.md

PHASE D — bigpowers wiring (enrich merged commands)
  D1. commands/fix.md ← investigate-bug + diagnose-root + validate-fix
  D2. commands/test.md ← audit-code + validate-fix mode + request-review
  D3. commands/cr.md ← assess-impact + trace-requirement
  D4. commands/uat.md ← define-success + trace-requirement
  D5. commands/release.md ← assess-impact + commit-message + release-branch
  D6. commands/init.md ← map-codebase + seed-conventions + session-state + hooks
  D7. commands/spec.md ← grill-me + elaborate-spec + define-success (auto-count)
  D8. commands/plan.md ← deepen-architecture + design-interface + plan-work
  D9. commands/implement.md ← develop-tdd + dispatch-agents + execute-plan
```

---

## Part 8 — Design Principles for the Redesigned Workflow

1. **One namespace, one folder.** `acps.*` owns all commands; `specs/` owns all artifacts. No hidden directories, no dual-prefix confusion.

2. **bigpowers skills are sub-routines, never commanders.** Skills produce evidence and artifacts. ACPS commands own the gateway decisions, `PROJECT_STATUS.md` updates, and user confirmation gates.

3. **Count is always automatic.** Removing `acps.count` as a standalone command eliminates a source of process friction. Counting happens inside `acps.spec`; the data is there when `acps.baseline` needs it.

4. **Docs and scope are not ceremonies, they are phases.** Absorbing `docs` into UAT and `scope` into `release` removes two command invocations from the hot path without losing any logic.

5. **Session continuity via `session-state`.** Every ACPS command writes a state snapshot. `survey-context` reads it at session start. The workflow is resumable at any step without re-reading all artifacts manually.

6. **Traceability as a first-class concern.** `trace-requirement` runs at `spec`, `uat`, and `cr`. Every acceptance criterion, UAT check, and change request links back to a backlog item.

---

*Plan updated: 2026-05-18*
*Scope: `.specify/` → `specs/` migration · 19 → 11 command consolidation · bigpowers skill integration*
