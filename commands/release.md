---
description: "Create release notes in CHANGELOG.md and releases/"
command: speckit.acps.release
---

<objective>
Prepare and execute a release: determine version bump, generate CHANGELOG entry, create tag, and document the release.
</objective>

<context>
Entered when GW_EpicoCompleto (yes) — epic is complete. Before GW_MaisTrabalho.
</context>

<core_principle>
Confirmation gates before irreversible actions. Never push without user approval.
</core_principle>

<process>
**Phase 1 — Prepare:**

1. Read git log since last tag: `git log <last_tag>..HEAD --oneline`
2. Propose semver bump: major (BREAKING CHANGE), minor (feat), patch (fix/other)
3. Summarize commits by type. Note any concerns (unmerged PRs, failing CI).
4. **Gate:** Present proposed version and commit summary. Ask user to confirm.

**Phase 2 — Bump:**

5. Bump version in appropriate file (package.json, pyproject.toml, Cargo.toml, etc.)
6. Generate CHANGELOG.md entry in Keep a Changelog format under `## [x.y.z] - YYYY-MM-DD`
7. Commit: `chore(release): vX.Y.Z`
8. Create annotated tag: `git tag -a vX.Y.Z -m "Release vX.Y.Z"`
9. **Gate:** Show diff and tag. Confirm before pushing.

**Phase 3 — Publish:**

10. Push commit and tag (only with user approval)
11. Verify CI passes on tagged commit

**Phase 4 — Document:**

12. Write `releases/vX.Y.Z.md` with: what shipped, links to changelog, key changes
13. Update `.specify/project/PROJECT_STATUS.md` with release entry
14. **Report:** state GW_MaisTrabalho gateway: "More epics? YES → back to backlog, NO → end"
</process>

<anti_patterns>
Don't push without confirmation. Don't skip CHANGELOG. Don't guess the version — derive from commits. Don't publish to registries without explicit ask.
</anti_patterns>

<success_criteria>
CHANGELOG.md updated with new entry; version bumped in project file; tag created; releases/ doc exists; user confirmed each irreversible action; PROJECT_STATUS.md updated.
</success_criteria>
