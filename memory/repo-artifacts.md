# Repo Artifacts — versioned living docs

Every project managed by ACPS produces a set of **versioned living documents** split across two root folders: `docs/` (app documentation — what you built) and `specs/` (project workflow artifacts — how you're building it). All files are AI-readable, human-visible (no hidden directories), and snapshotted on each update so every change is traceable. The old `.specify/` hidden folder is eliminated — everything lives in `specs/`.

## Artifact inventory

| File | Role | Created by | Updated by | Snapshotted by |
|------|------|-----------|-----------|---------------|
| `AGENT.md` (repo root) | AI context — conventions, stack, key paths, how to run/test | `setup` | `docs` | — |
| `docs/architecture/ARCHITECTURE_LATEST.md` | Technical decisions, system design, architecture patterns | `setup` | `docs` | `docs`, `release` |
| `docs/security/SECURITY_LATEST.md` | Security guardrails and compliance requirements | `setup` | `docs` | `docs`, `release` |
| `docs/design/DESIGN_LATEST.md` | UX / design system conventions, component patterns, accessibility baseline | `setup` | `docs` | `docs` |
| `docs/test/TEST_LATEST.md` | Test strategy, coverage expectations, quality baseline | `setup` | `test`, `docs` | `docs` |
| `specs/INITIATIVE_BRIEF_LATEST.md` | Initiative north star — vision, success criteria, scope boundaries | `create-epic-backlog` | `create-epic-backlog` | `create-epic-backlog` (per cycle) |
| `specs/BASELINE_LATEST.md` | Locked scope contract — in-scope stories + BCP totals at baseline date | `release-plan` | `release-plan` | `release-plan` (per re-baseline) |
| `specs/STATE.md` | Workflow position — current phase, last/next command, open blockers | every command | every command | — |

## Versioning convention

**`_LATEST.md`** is the current living version. Agents always read `_LATEST.md`.

**Date-stamped snapshots** are immutable. On each update, copy the current `_LATEST.md` to a dated snapshot *before* overwriting it:

```
docs/architecture/ARCHITECTURE_LATEST.md    ← agents read this
docs/architecture/ARCHITECTURE-2026-04-15.md ← immutable; never edited after creation
docs/architecture/ARCHITECTURE-2026-03-20.md ← older snapshot
```

Naming pattern: `[ARTIFACT]-YYYY-MM-DD.md`. If multiple updates happen on the same day, append a counter: `ARCHITECTURE-2026-04-15-2.md`.

## Artifact descriptions

### AGENT.md (root)
Project-level instructions for AI agents — detected stack, primary entrypoints, naming conventions, how to run tests and build, pointers into `specs/` and `docs/`. Agents read this first on every session. Not versioned with snapshots; git history is the audit trail.

### specs/INITIATIVE_BRIEF_LATEST.md
Created **once per initiative** at the start of `create-epic-backlog`. Captures the north star vision that all backlog items must serve. Acts as a scope-drift anchor: during `scope` and `change-request`, the agent cross-checks proposed changes against Polaris before recommending approval.

Sections: **North Star** (one sentence), **Strategic Intent**, **Success Criteria** (table), **Scope Boundaries** (in / out), **Constraints & Non-Negotiables**.

### docs/architecture/ARCHITECTURE_LATEST.md
Technical decisions, system design, and architecture patterns. Updated during `docs` whenever architectural decisions change. Snapshotted before any `release` tag so every shipped version has an immutable architecture baseline.

Sections: **System Overview**, **Component Map**, **Technology Decisions** (table: decision / choice / rationale / date), **Architecture Principles**, **Known Constraints**, **ADR Index** (links to `docs/architecture/adr/`), **Change History**.

### docs/security/SECURITY_LATEST.md
Security rules and compliance requirements. The AI cannot deviate from policies listed here without explicit human approval. Snapshotted on every update — the immutable snapshot is the auditable record.

Sections: **Authentication & Authorization**, **Data Handling** (PII, encryption), **Dependency Policy**, **Secret Management**, **Compliance Requirements**, **Change History**.

### docs/test/TEST_LATEST.md
Quality baseline for this specific project. The `test` command reads this before running the suite to understand what "quality" means here (coverage floors, required gate checks, forbidden patterns). Updated whenever the team's quality bar changes.

Sections: **Coverage Expectations** (table: layer / minimum / type), **Test Framework**, **Quality Gates** (what must pass before any ACPS gateway succeeds), **Forbidden Patterns**, **Change History**.

## ADR convention

Architecture Decision Records live at `docs/architecture/adr/ADR-NNN-slug.md`. Each ADR has: **Status** (proposed / accepted / deprecated / superseded), **Context**, **Decision**, **Consequences**, **Date**. The ADR Index in `ARCHITECTURE_LATEST.md` links to all accepted ADRs.

### docs/design/DESIGN_LATEST.md
UX and design system conventions for the project — component patterns, token standards, accessibility baseline, Figma/Storybook links. Snapshotted during `docs` runs. The testing agent and implementation agent read this alongside ARCHITECTURE to understand UI constraints.

Sections: **Design System**, **Component Conventions**, **Token Standards**, **Accessibility Baseline**, **Tools & References**, **Change History**.

### specs/INITIATIVE_BRIEF_LATEST.md
Created **once per initiative** at the start of `create-epic-backlog`. Captures the north star that all epics and stories must serve. Acts as the primary scope-drift anchor: during `scope` and `change-request`, the agent cross-checks proposed changes against the brief before recommending approval.

Sections: **North Star**, **Strategic Intent**, **Success Criteria**, **Scope Boundaries** (in/out), **Constraints & Non-Negotiables**, **Stakeholders**, **Change History**.

### specs/BASELINE_LATEST.md
Produced by `release-plan` after scope is confirmed and BCP totals are calculated. Represents the **locked scope contract** — the agreed set of in-scope stories with their sizes. All change requests are measured against this. `RELEASE_PLAN.md` may evolve (task status, new stories from CRs); the baseline does not change until `release-plan` is run again.

Sections: **Baseline header** (locked date, total BCP), **In-Scope Epics & Stories** (summary table), **Sizing Summary**, **Milestones**, **Out of Scope**, **Deferred**, **Risks & Assumptions**, **Baseline History**.

### specs/STATE.md
Workflow position snapshot — the resume point for agents between sessions. Updated by every ACPS command as its last step. Not a log (use git history for audit trail); a concise current-state record.

Fields: **Current initiative**, **Current epic**, **Current story**, **Current phase** (e.g. `per-spec pipeline: implement`), **Last command** (command + timestamp), **Next expected command**, **Open blockers**, **Recent completions** (last 3–5 steps).

## Snapshot protocol (for command authors)

When a command needs to update a versioned artifact:

1. Read the current `_LATEST.md`.
2. Write it to `[ARTIFACT]-YYYY-MM-DD.md` (immutable snapshot — never edit after creation).
3. Overwrite `_LATEST.md` with the new content.
4. Update `specs/STATE.md` with the snapshot filename and a pointer to the updated LATEST.
