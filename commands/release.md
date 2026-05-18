---
description: "Scope review (Phase 1) + semver bump + changelog + tag + publish"
command: acps.release
---

<objective>
Prepare and execute a release in four phases: (1) scope review — assess any scope drift before packaging; (2) prepare — determine version bump and summarize changes; (3) bump — write changelog, commit, and tag; (4) publish — push and document. Scope review is always the first phase, not an optional branch.
</objective>

<context>
Entered when `GW_UATOk` yes and (from `GW_EpicComplete`) the epic is complete. Feeds `GW_MoreWork`.
</context>

<core_principle>
Scope review first, confirmation gates before irreversible actions. Never push without user approval. Scope changes found in Phase 1 flow through `acps.cr`, not silent edits to `specs/RELEASE_PLAN.md`.
</core_principle>

<bigpowers_skills>
Sub-routines invoked by this command:

- **`assess-impact`** — Phase 1 scope review: structured impact matrix before committing to release packaging
- **`inspect-quality`** — structured code/artifact audit → `specs/BUG-LOG.md` before tagging
- **`commit-message`** — derives Conventional Commits-based semver bump from git log
- **`release-branch`** — creates PR with coverage gates, semver tag, and worktree cleanup
</bigpowers_skills>

<process>
**Phase 1 — Scope Review**

1. Identify the scope review trigger: read `specs/RELEASE_PLAN.md` (baseline) and compare with what was actually implemented (specs, tasks, commits since last release tag).
2. Run **`assess-impact`** — structured impact matrix: for each delta between baseline and implementation, classify scope / budget / timeline / resource impact. Classify severity: Minor / Moderate / Major.
3. If scope drift is found, present findings with old → new proposals for affected artifacts.
4. **MANDATORY DECISION GATE:** present findings. Options: 1) Accept drift as-is and proceed 2) Open `acps.cr` before release 3) Defer items to next cycle 4) Discuss further. Wait for user response.
5. Write `specs/scope/scope-review-[date].md` with: Trigger, Impact Analysis, Severity, Change Proposals, Decision.
6. Update `specs/project/PROJECT_STATUS.md` with scope review outcome.

**Phase 2 — Prepare**

7. Run **`inspect-quality`** — structured audit of changed code and artifacts → `specs/BUG-LOG.md`. Address any blockers before tagging.
8. Read git log since last tag: `git log <last_tag>..HEAD --oneline`.
9. Run **`commit-message`** — derive semver bump from Conventional Commits in git log: major (BREAKING CHANGE), minor (feat), patch (fix/other).
10. Summarize commits by type. Note any concerns (unmerged PRs, failing CI).
11. **Gate:** present proposed version and commit summary. Ask user to confirm.

**Phase 3 — Bump**

12. Bump version in the appropriate file (`package.json`, `pyproject.toml`, `Cargo.toml`, etc.).
13. Generate `CHANGELOG.md` entry in Keep a Changelog format under `## [x.y.z] - YYYY-MM-DD`.
14. Commit: `chore(release): vX.Y.Z`.
15. Create annotated tag: `git tag -a vX.Y.Z -m "Release vX.Y.Z"`.
16. **Gate:** show diff and tag. Confirm before pushing.

**Phase 4 — Publish**

17. Run **`release-branch`** — creates PR with coverage gates, semver tag, and worktree cleanup. Push commit and tag (only with user approval).
18. Verify CI passes on tagged commit.
19. Write `releases/vX.Y.Z.md` with: what shipped, links to changelog, key changes.
20. Update `specs/project/PROJECT_STATUS.md` with release entry.
21. **Report:** state `GW_MoreWork` gateway: "More epics? YES → `acps.backlog`, NO → end".
</process>

<anti_patterns>
Don't skip scope review (Phase 1) — it is always mandatory, not a conditional branch. Don't push without confirmation. Don't skip CHANGELOG. Don't guess the version — derive from commits using `commit-message`. Don't publish to registries without explicit ask. Don't present vague scope impact ("might affect things") — `assess-impact` must produce specific findings.
</anti_patterns>

<success_criteria>
Scope review (`specs/scope/scope-review-[date].md`) exists and decision gate was presented. `inspect-quality` audit completed. `CHANGELOG.md` updated with new entry. Version bumped in project file. Tag created. `releases/vX.Y.Z.md` exists. User confirmed each irreversible action. `specs/project/PROJECT_STATUS.md` updated. Gateway line spoken: YES → `acps.backlog` or NO → end.
</success_criteria>
