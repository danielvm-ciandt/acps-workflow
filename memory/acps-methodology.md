# ACPS — Agent methodology reference

Condensed reference for agents working inside the **ACPS Workflow** extension. All commands live in the single `acps.*` namespace. Artifacts are stored under `specs/`.

## Command system

| Command | Role |
|---------|------|
| `acps.init` | Bootstrap environment, governance, project conventions |
| `acps.backlog` | Create and maintain the ordered epic/feature backlog |
| `acps.spec` | Write specification, refine via dialogue, auto-count scope (BCP/FP/SNAP) |
| `acps.baseline` | Size backlog and establish baseline scope contract in `specs/RELEASE_PLAN.md` |
| `acps.plan` | Technical plan → bridge to baseline → task breakdown → optional analysis |
| `acps.implement` | TDD implementation: red → green → refactor per behavior slice |
| `acps.test` | Run tests with evidence; quality gate for UAT |
| `acps.fix` | Root-cause triage, fix, and re-verify; returns to `acps.test` |
| `acps.uat` | User acceptance testing + docs update (Phase 3) |
| `acps.release` | Scope review (Phase 1) + semver bump + changelog + tag + publish |
| `acps.cr` | Register change request, assess impact, update backlog |

## bigpowers skills

bigpowers skills are **sub-routines**: they produce evidence and artifacts. ACPS commands own the gateway decisions, user confirmation gates, and `specs/project/PROJECT_STATUS.md` updates.

## Workflow order

1. **`acps.init`** — Initialize environment, governance, and `specs/` structure.
2. **`acps.backlog`** — Build ordered epic list in `specs/BACKLOG.md`.
3. **Spec loop:** `acps.spec` → gateway: remaining specs? If yes, repeat; if no, exit loop.
4. **`acps.baseline`** — Establish numeric scope baseline in `specs/RELEASE_PLAN.md`.
5. **Per-spec pipeline:** `acps.plan` → `acps.implement` → `acps.test`.
6. **Quality — tests:** `acps.test` → pass → `acps.uat`; fail → `acps.fix` → `acps.test`.
7. **UAT + docs:** `acps.uat` → pass → `acps.release`; fail → `acps.fix` → `acps.test`.
8. **Release:** `acps.release` (includes scope review) → gateway: epic complete? yes → gateway: more work? yes → `acps.backlog`; no → end.

**`acps.cr`** runs as a **parallel process**: may start at any time and updates `specs/BACKLOG.md` immediately.

## Folder conventions

| Path | Purpose |
|------|---------|
| `specs/` | All ACPS artifacts (visible root) |
| `specs/BACKLOG.md` | Epic / feature list |
| `specs/RELEASE_PLAN.md` | Baseline scope contract |
| `specs/TEST_SUMMARY.md` | Latest test run results |
| `specs/CONTEXT.md` | Codebase context (from `map-codebase`) |
| `specs/UBIQUITOUS_LANGUAGE.md` | Domain language (from `define-language`) |
| `specs/CONSTITUTION.md` | Project governance (from `acps.init`) |
| `specs/adr/` | Architecture decision records |
| `specs/plan/` | Technical plan and bridge artifacts |
| `specs/counting/` | BCP / FP / SNAP output files |
| `specs/bugs/` | BUG[NNN]-slug.md files |
| `specs/uat/` | milestone-uat.md files |
| `specs/scope/` | Scope impact drafts (archived; now produced inside `acps.release`) |
| `specs/project/` | PROJECT_STATUS.md + change-requests/ |
| `specs/setup/` | environment-check.md |
| `AGENT.md` | Agent entrypoint — stays at repo root |
| `CHANGELOG.md` | Release history — stays at repo root |

## Policies

- **Change requests:** Update `specs/BACKLOG.md` immediately when a CR is registered; refresh `specs/RELEASE_PLAN.md` only at cadence (per milestone/epic) via `acps.baseline`, not on every CR.
- **Baseline scope:** After `acps.baseline`, scope is the delivery contract. Scope changes flow through `acps.cr`.
- **Counting:** Runs automatically at the end of every `acps.spec` invocation (`after_spec` hook). Recount without re-specifying: `acps.spec --count-only`.
- **Session continuity:** Every ACPS command writes a state snapshot to `specs/project/PROJECT_STATUS.md`. The `survey-context` skill reads it at session start to orient the agent without re-reading all artifacts.
