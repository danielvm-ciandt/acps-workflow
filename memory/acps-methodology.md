# ACPS / SDD — Agent methodology reference

Condensed reference for agents working inside the **ACPS Workflow** Spec Kit extension. Full SDD (Specification-Driven Development) practices still apply; this doc wires them to commands and folders.

## Dual command system

| Namespace | Role |
|-----------|------|
| `/speckit.*` | **Spec Kit core** — constitution, specification, planning, tasks, analysis, implementation. |
| `speckit.acps.*` | **ACPS workflow extension** — trunk setup, epic backlog, release baseline, quality gates, scope, release, counting, change control. |

Workflow slash commands may appear as `/workflow.<name>` in tooling; they map to the `speckit.acps.*` commands listed in the extension.

### Spec Kit core (`/speckit.*`)

- `constitution` — project principles and governance
- `specify` — write/update the feature spec
- `clarify` — optional refinement loop
- `plan` — technical / delivery plan
- `tasks` — task breakdown
- `analyze` — optional pre-implementation review
- `implement` — implementation pass

### ACPS extension (`speckit.acps.*`)

- `setup` — bootstrap environment and project status
- `create-epic-backlog` — ordered epic list
- `release-plan` — sizing and baseline release plan
- `plan-bridge` — align baseline plan with technical plan from `speckit.plan`
- `test` — run tests; capture evidence
- `bugfix` — failure handling loop
- `uat` — user acceptance
- `docs` — documentation updates
- `scope` — optional scope-impact review
- `release` — release notes / release artifacts
- `change-request` — formal CR handling
- `count` — BCP / FP / SNAP scope counting

## Team trunk workflow (order)

1. **setup → constitution → create-epic-backlog** — Initialize project, rules, and epic list.
2. **Baseline loop:** **specify** → **clarify** (optional) → gateway: **“remaining specs?”** If yes, continue specifying; if no, exit loop.
3. **release-plan** — Establish numeric baseline and `RELEASE_PLAN.md`.
4. **Per-spec pipeline:** **plan** → **plan-bridge** → **tasks** → **analyze** (optional) → **implement** → **test**.
5. **Quality — tests:** **test** → gateway: pass → **uat**; fail → **bugfix** → **test** again.
6. **UAT:** **uat** → gateway: pass → **docs**; fail → **bugfix** → **test**.
7. **Close spec:** **docs** → **scope** (optional) → gateway: **“epic complete?”**
8. **Release / next epic:** **release** → gateway: **“more epics?”** — yes → **create-epic-backlog** / backlog work; no → end.

**Change request** runs as a **parallel process**: it may start anytime; it updates governance artifacts per policy (see below), not only at linear step boundaries.

## Folder conventions

| Path | Purpose |
|------|---------|
| `.specify/` | All Spec Kit / ACPS artifacts (default root) |
| `.specify/bugs/` | Bugfix trail, test failures |
| `.specify/uat/` | UAT evidence and verdicts |
| `.specify/scope/` | Scope assessments |
| `.specify/counting/` | BCP / FP / SNAP outputs |
| `.specify/project/` | Project-level records (e.g. change requests) |

(Exact filenames such as `BACKLOG.md` / `RELEASE_PLAN.md` may live at repo root or under `.specify/project/` depending on template — follow project `acps-config.yml`.)

## Policies

- **Change requests:** Update **`BACKLOG.md` immediately** when a CR is accepted or recorded; refresh **`RELEASE_PLAN.md` only on the agreed cadence** (e.g. per milestone/epic), not on every CR edit.
- **Baseline scope:** After **release-plan**, the baseline scope is treated as the **contract** for delivery and scope-change conversations (see `scope` and CR flow).
