# ACPS × ECC Integration Plan

> Status: Draft v0.1 — May 2026
> Author: Daniel V. Magalhães (danielvm@ciandt.com)
> Scope: fold the relevant pieces of [ECC ("Everything Claude Code")](https://github.com/affaan-m/ECC) into the existing **acps-workflow** Spec Kit extension, distributed as a **single** extension to delivery teams.

---

## 1. Why this plan exists

acps-workflow already provides the **delivery trunk** on top of Spec Kit: epic backlog cycling, baseline release planning, plan bridging, the test → UAT → docs quality chain, change requests, scope review, and BCP/FP/SNAP counting. The bigpowers integration plan (`BIGPOWERS-INTEGRATION-PLAN.md`) collapsed that surface into 11 lean `acps.*` commands that call skills as sub-routines.

What's missing is the **how-to-actually-code-well-with-agents** layer: enforced TDD, build resolvers per language, code review, security scanning, hook-driven quality gates, agent harness audit, continuous learning, memory persistence across sessions, cross-harness portability. That's exactly what ECC ships — and unlike rolling our own, ECC is MIT-licensed, multi-harness, and battle-tested (60 agents, 232 skills, 75 commands, ~10 months of daily-use evolution).

The two systems live at different layers and complement each other cleanly:

| Layer | Owns | Source |
|---|---|---|
| **Delivery governance** — what to build, when to gate, what to count, how to talk to the client | trunk state machine, artifacts under `specs/`, BCP/FP/SNAP, change requests, UAT, release packaging | acps-workflow |
| **Engineering execution** — TDD enforcement, code review, build fixers per language, security, harness performance, memory across sessions | agents/skills/hooks invoked as sub-routines inside acps commands | ECC |
| **Spec authoring engine** | spec / plan / tasks / implement core loop | Spec Kit |

Goal of this document: one single Spec Kit extension (still `acps`), one install, one namespace — with ECC absorbed the same way bigpowers was absorbed.

---

## 2. What ECC actually ships (verified inventory)

Read from the live repo at `/Users/danielvm/.opensrc/repos/github.com/affaan-m/ECC/main/` on 2026-05-25:

- **Version**: 2.0.0-rc.1 (MIT)
- **60 agents** (e.g. planner, tdd-guide, code-reviewer, security-reviewer, build-error-resolver, e2e-runner, doc-updater, harness-optimizer, loop-operator, plus language-specific reviewers/resolvers for TypeScript, Go, Rust, Kotlin, Java, C++, Python, Django, F#, PyTorch, ML)
- **232 skills** across `.claude/skills`, `.agents/skills`, `.cursor/skills`
- **75 commands** (e.g. `/plan`, `/tdd`, `/code-review`, `/build-fix`, `/verify`, `/quality-gate`, `/e2e`, `/test-coverage`, `/security-scan`, `/docs`, `/harness-audit`, `/learn`, `/evolve`, `/save-session`, `/resume-session`, plus 75 legacy command shims for compatibility)
- **Hook system** with PreToolUse / PostToolUse / SessionStart / Stop / SessionEnd matchers covering Bash, Write, Edit, MultiEdit — including a GateGuard fact-forcing gate, governance-capture, config-protection, MCP health checks, format/typecheck batching, session persistence, and cost tracking
- **Install profiles**: `minimal | core | developer | security | research | full` driven by `manifests/install-profiles.json`
- **Cross-harness manifests**: Claude Code (`.claude-plugin/`), Codex (`.codex/`, `.codex-plugin/`), Cursor (`.cursor/`), OpenCode, Codebuddy, Antigravity
- **Rules per language**: `rules/{angular,arkts,cpp,csharp,dart,fsharp,golang,java,kotlin,perl,php,python,ruby,rust,swift,typescript,web,...}/`
- **Enterprise controls** starter at `.claude/enterprise/controls.md` (audit allowlists, approval expectations, generated-skill review policy)
- **ECC 2.0 alpha (Rust control-plane)** in `ecc2/` — out of scope for MVP
- **Plugin schema gotchas** documented in `.claude-plugin/PLUGIN_SCHEMA_NOTES.md` (validator strictness on `agents`, `version`, array fields) — must be respected when we extend our own `plugin.json` / `extension.yml`

---

## 3. Hard constraints from this plan

These are the non-negotiables that shape every decision below:

- **Single extension**: deliver as the existing `acps-workflow` extension, extended in-place. No second install for teams.
- **Mixed audience**: both PMs (PMBoK artifacts, gates, traceability) and devs (low-ceremony IDE commands) must each have a clean entry path.
- **No ECC reference repo of our own**: we synthesize the mapping from PMBoK + Agile + Spec Kit principles. The ECC OSS upstream remains the source of truth for skills/agents; we vendor a curated subset.
- **Licensing**: ECC is MIT, acps is MIT — compatible. Preserve `LICENSE`, `NOTICE`, and per-file attribution where required.
- **No upstream ECC GitHub App / Pro**: free OSS path only. AgentShield can be optional add-on, but defaults stay free.
- **Respect the trunk**: ECC pieces become sub-routines of `acps.*` commands. They do not introduce a parallel command namespace visible to users.

---

## 4. Concept-to-trunk mapping (the heart of the plan)

The trunk stays exactly the 11 `acps.*` commands defined in `memory/acps-methodology.md`. ECC content lands as sub-routines, hooks, profiles, or seeded artifacts inside those commands.

### 4.1 Per-command absorption matrix

| acps command | ECC artifacts absorbed | Role in command | Conflict / dedup with bigpowers |
|---|---|---|---|
| `acps.init` | `/harness-audit`, `harness-optimizer` agent, `SessionStart` hook (memory persistence), `install-profiles.json` (profile selector), `.claude/enterprise/controls.md` template | One-time harness audit at project bootstrap; choose install profile (minimal/core/developer/security/research/full); seed `specs/CONSTITUTION.md` with enterprise control language; install SessionStart hook that hydrates `specs/STATE.md` automatically | Coexists with bigpowers `map-codebase` / `survey-context`. Order: ECC harness-audit first, then bigpowers context skills. |
| `acps.backlog` | `planner` agent (decomposition mode) | Used to break large initiatives into epic candidates before bigpowers `grill-me` refines them | bigpowers `grill-me` / `elaborate-spec` lead; ECC `planner` is the upstream decomposer when no clear epic shape exists |
| `acps.spec` | — (intentionally none) | acps.spec is product authoring; ECC is implementation-side | No change |
| `acps.baseline` | — | Pure scope contract; no ECC overlap | No change |
| `acps.plan` | `/plan` command logic (risk assessment + step-by-step plan + confirm-before-touching-code pattern), `architect` agent, `database-reviewer` (when schema work is in scope) | `acps.plan` already covers tech plan → bridge → tasks → analyze. ECC `/plan` semantics (restate requirements, risk assessment, wait-for-confirm) become an explicit sub-routine. `architect` is the agent invoked for non-trivial design decisions; ADRs land in `docs/architecture/adr/`. | bigpowers `architect-system` / `decompose-tasks` stay primary; ECC `planner`/`architect` are deeper agents invoked when complexity demands it |
| `acps.implement` | `tdd-guide` agent, `/tdd` command logic, language `*-reviewer` agents (typescript-reviewer, python-reviewer, go-reviewer, rust-reviewer, kotlin-reviewer, java-reviewer, cpp-reviewer, etc.), `rules/{lang}/` (coding-style, hooks, patterns, security, testing) | ECC `tdd-guide` becomes the engine behind acps.implement's red→green→refactor. Per-language reviewer runs as final pre-test self-review. Language rules are auto-seeded into `docs/test/TEST_LATEST.md` and `docs/architecture/ARCHITECTURE_LATEST.md`. | bigpowers `develop-tdd` and ECC `tdd-guide` overlap. **Resolution**: bigpowers `develop-tdd` orchestrates the cycle; ECC `tdd-guide` is the deeper agent invoked when the cycle produces flaky/incomplete tests. F.I.R.S.T check (bigpowers `enforce-first`) runs after both. |
| `acps.test` | `/verify`, `/quality-gate`, `/test-coverage`, `code-reviewer` agent, `security-reviewer` agent, `/security-scan` (AgentShield optional), `e2e-runner` agent, `/e2e` | acps.test gains: (a) full quality gate (build + lint + typecheck + test) as a single sub-routine via `/verify`; (b) mandatory `code-reviewer` pass before PASS verdict; (c) `security-reviewer` for security-sensitive changes flagged by the spec; (d) e2e mode for critical user flows when UAT will exercise them | bigpowers `audit-code`/`request-review` run first (cheap, in-session). ECC `code-reviewer` runs on top as the harder gate. PASS requires both. |
| `acps.fix` | `build-error-resolver` agent + per-language `*-build-resolver` agents (`/build-fix` dispatcher), GateGuard fact-forcing hook | When tests/build fail, dispatch to the right language-specific resolver instead of letting the trunk agent flounder. GateGuard sub-routine forces investigation (importers, schemas, instruction trail) before allowing the first Edit on a file. | bigpowers `validate-fix` / `respond-review` already define root-cause discipline. ECC resolvers are the **mechanism**; bigpowers is the **discipline**. Both run; resolver does the fix, bigpowers verifies. |
| `acps.uat` | `e2e-runner` (replay critical flows + screenshots/videos/traces), `doc-updater` agent | E2E artifacts (screenshots, traces) are attached as UAT evidence. `doc-updater` refreshes `docs/*_LATEST.md` snapshots before release. | bigpowers `update-docs` already exists; ECC `doc-updater` is the deeper agent for cross-doc consistency |
| `acps.release` | `security-reviewer` (release gate), `/security-scan`, governance-capture hook log (Phase 1 scope review evidence), `code-architect` for ADR snapshots | Pre-release: security review gate; scope review reads the `governance-capture` event log accumulated since the last release for an honest "what changed and why". ADR snapshots are validated by `code-architect`. | No bigpowers conflict; bigpowers `release-package` stays primary |
| `acps.cr` | governance-capture hook events, `planner` agent | CR impact assessment uses the captured governance log + planner's risk decomposition. Impact appears in the CR file alongside the BCP delta. | bigpowers `assess-impact` leads; ECC planner deepens when impact is unclear |

### 4.2 ECC pieces that **do not** absorb

We do **not** import:
- The 75 ECC commands as-is — they would explode our surface. Their **logic** lives inside acps.* commands; the slash-command form is not exposed.
- 200+ of the 232 skills. We curate **~30 skills** (see §5 below). The rest stay upstream and can be opted into by teams that want them.
- ECC 2.0 / `ecc2/` Rust control-plane — alpha, out of MVP. Re-evaluate after GA.
- ECC dashboard GUI (`ecc_dashboard.py`) — desktop app, not needed for the trunk. Optional add-on later.
- GitHub App / ECC Pro path — paid hosted layer. Out of scope. We document teams can install it separately if they want PR-triggered audits.
- Continuous learning / `/learn` / instincts as a default. We surface them as **optional** under a new `acps.learn` sub-command in a later phase (Phase 5) — they are powerful but risky to enable org-wide without a curation policy.

### 4.3 Hooks: the most important integration risk

ECC ships an aggressive hook set (PreToolUse on Bash/Write/Edit, PostToolUse, Stop, SessionStart, SessionEnd). acps already has `after_spec` and `after_implement` hooks. Without coordination, hooks collide and slow the agent.

**Decision**: introduce a single **hook profile selector** in `acps-config.yml`:

```yaml
ecc:
  hook_profile: "minimal"   # off | minimal | standard | strict
  enabled_hooks:
    quality_gate: true       # post:quality-gate after Edit/Write/MultiEdit
    gateguard: true          # pre:edit-write:gateguard-fact-force
    config_protection: true  # pre:config-protection (blocks lint config weakening)
    governance_capture: true # pre/post:governance-capture (acps.cr / acps.release evidence)
    session_persistence: true # SessionStart bootstrap + Stop session-end
    format_typecheck: true   # stop:format-typecheck batching
  disabled_hooks: []
```

This mirrors ECC's own `ECC_HOOK_PROFILE` + `ECC_DISABLED_HOOKS` env vars and lets each project tune the noise. Default for acps installs: `minimal` — only the hooks that produce evidence acps commands rely on (governance-capture, session-persistence, quality-gate).

### 4.4 Install profiles → client tiers

ECC's six profiles already map almost 1:1 to common consultancy delivery tiers:

| ECC profile | acps client-tier label | Use when |
|---|---|---|
| `minimal` | **Lean Discovery** | Pre-sales, prototypes, short-cycle work; no hook runtime |
| `core` | **Standard Agile** | Most internal team projects; baseline hook runtime on |
| `developer` | **Engineering-Heavy** | Most application codebase engagements; language packs + DB + orchestration |
| `security` | **Regulated / SOX / HIPAA / PCI** | Adds AgentShield + governance-capture hardening |
| `research` | **Innovation / R&D** | Investigation-led work with content/output skills |
| `full` | **Enterprise Flagship** | Long-term embedded teams; everything on |

acps adds two of its own on top, since ECC has no concept of these:
- **`pmbok-heavy`**: forces stricter `acps.cr` impact docs, mandatory `acps.uat` artifacts, locked `BASELINE_LATEST.md` re-baselining only at milestone boundaries
- **`agile-heavy`**: shorter cadence, optional `acps.uat` per slice instead of per spec, looser CR ceremony

The `acps.init` command asks once which client tier applies and writes both `ecc.profile` and `acps.delivery_tier` into `acps-config.yml`.

### 4.5 The bigpowers ↔ ECC overlap, resolved

| Concern | bigpowers skill | ECC equivalent | Resolution in acps |
|---|---|---|---|
| Code review | `audit-code`, `request-review`, `respond-review` | `code-reviewer` agent, `/code-review` | bigpowers first (in-session, fast); ECC code-reviewer as second gate (deeper, fresh agent). Both must pass for `acps.test` PASS. |
| TDD | `develop-tdd`, `enforce-first` | `tdd-guide`, `/tdd` | bigpowers orchestrates the cycle; ECC `tdd-guide` is invoked when bigpowers reports flakiness or low coverage. F.I.R.S.T (`enforce-first`) is the rubric for both. |
| Build fix | `validate-fix` | `build-error-resolver` + language resolvers | ECC resolver does the fix (language-aware); bigpowers `validate-fix` confirms regression test passes after fix. |
| Docs | `update-docs` | `doc-updater` agent | bigpowers leads; ECC doc-updater invoked when multi-doc consistency is at risk. |
| Session continuity | `session-state` | SessionStart/Stop hooks | ECC hooks own the mechanism (write/read state); bigpowers `session-state` owns the schema. The hook calls into the bigpowers schema. |
| Security | (none) | `security-reviewer`, `/security-scan`, AgentShield | ECC owns this lane outright. |
| Harness performance | (none) | `harness-optimizer`, `/harness-audit` | ECC owns this lane outright. |
| Language reviewers | (none) | 12 language-specific reviewers | ECC owns this lane outright. |

**Principle**: bigpowers stays as the in-session, fast, discipline-focused layer. ECC is the deeper, agent-based, language-aware layer. Either can stand alone for a given gate; both stacked is the default for high-stakes gates (`acps.test`, `acps.release`).

---

## 5. The curated 30-skill set

To keep the extension lean, we vendor only the ECC skills that map to a trunk command or hook. Everything else stays in upstream ECC and teams opt-in manually.

### Core engineering (10)
`tdd-workflow` · `verification-loop` · `code-review` · `security-review` · `e2e-testing` · `api-design` · `coding-standards` · `backend-patterns` · `frontend-patterns` · `documentation-lookup`

### Build & resolve (4, with language fan-out)
`build-error-resolver` aggregator + per-language resolvers loaded on demand from `rules/{lang}/` (TS, Python, Go, Rust, Kotlin, Java, C++, Django)

### Quality & observability (4)
`eval-harness` · `agent-introspection-debugging` · `mcp-server-patterns` · `agent-sort`

### Research & spec support (4)
`deep-research` · `market-research` · `product-capability` · `everything-claude-code` (project conventions skill template)

### Governance & ops (4)
`strategic-compact` · `dmux-workflows` (parallel agent orchestration) · `documentation-lookup` · `exa-search` (optional)

### Cross-cutting (4)
`brand-voice` · `article-writing` (release notes / client comms) · `content-engine` · `crosspost` (optional)

This is a starting list. Phase-1 curation review can drop/add. Crucially, **none of these are exposed as a slash command** to end users — they're invoked from inside `acps.*`.

---

## 6. Distribution shape (single Spec Kit extension)

Folder layout for the extended `acps-workflow` repo, after integration:

```
acps-workflow/
├── extension.yml                  # unchanged 11 commands, version bumped to 3.0.0
├── README.md                      # rewritten: PM entry + dev entry sections
├── ECC-INTEGRATION-PLAN.md        # this file
├── BIGPOWERS-INTEGRATION-PLAN.md  # existing
├── LICENSE                        # MIT (acps)
├── NOTICE                         # NEW — attribution for ECC + bigpowers
├── commands/
│   └── *.md                       # 11 existing acps.* command files, extended with ECC sub-routine references
├── config/
│   └── acps-config.template.yml   # extended with ecc.* section + delivery_tier
├── memory/
│   ├── acps-methodology.md
│   ├── acps-states.md
│   ├── repo-artifacts.md
│   ├── bcp-rubric.md
│   ├── ecc-skill-catalog.md       # NEW — the curated 30 with descriptions + which command uses them
│   └── ecc-hook-profiles.md       # NEW — minimal/standard/strict definitions
├── templates/                     # existing _LATEST.md templates, plus:
│   ├── HARNESS_AUDIT.md           # NEW — output template for /harness-audit at init
│   └── ENTERPRISE_CONTROLS.md     # NEW — seeded into specs/CONSTITUTION.md by acps.init
├── ecc/                           # NEW — vendored ECC subset
│   ├── VERSION                    # pinned upstream commit / tag
│   ├── skills/                    # curated 30 SKILL.md files (with attribution headers)
│   ├── agents/                    # ~15 curated agents (planner, tdd-guide, code-reviewer, security-reviewer, build-error-resolver + per-language reviewers/resolvers we ship)
│   ├── hooks/                     # subset of ECC hooks gated by acps.config
│   └── rules/                     # selected language rules (TS/Python/Go/Java/Kotlin/Rust to start)
└── prompts/                       # existing
```

The user-facing surface remains: 11 `acps.*` commands. The user never types `/tdd`, `/code-review`, `/build-fix`. Everything is dispatched from the trunk.

### 6.1 Versioning & upstream sync

- Pin upstream ECC commit in `ecc/VERSION`.
- Quarterly sync job (Phase 4): a script reads `ecc/VERSION`, fetches the curated subset from upstream, opens a PR with diffs. Human approval required.
- AgentShield, if enabled, is referenced — never vendored. Teams who want it install separately.

### 6.2 Cross-harness positioning (deferred)

ECC ships manifests for Codex / Cursor / OpenCode / Codebuddy / Antigravity. For MVP we stay Spec Kit + Claude Code. **Phase 5** adds Codex + Cursor packaging by mirroring our 11-command surface into their idioms — same trunk, different harness wrapper. We do not commit to all five harnesses.

---

## 7. Phased rollout

### Phase 0 — Alignment (done by this doc)
- ECC inventory verified against live repo
- Mapping to 11-command trunk drafted
- Curation list defined (30 skills, ~15 agents)
- bigpowers vs ECC overlap resolved

**Exit**: this plan reviewed and signed off by Daniel and one tech lead.

### Phase 1 — Curation & Specification (1 sprint)
- Lock the 30-skill / 15-agent curation list (review with two devs and one PM)
- Write per-command extension specs: each `commands/*.md` updated with explicit ECC sub-routine names alongside the existing `<bigpowers_skills>` block (becomes `<ecc_subroutines>`)
- Specify hook profile semantics in `memory/ecc-hook-profiles.md`
- Specify delivery-tier semantics in `memory/delivery-tiers.md`
- Decide MVP language scope: **TypeScript + Python + Go** for the first cut (covers ~80% of CI&T engagements)

**Exit criteria**: Specs reviewed; no open questions on which sub-routine fires when.

### Phase 2 — MVP integration (2 sprints)
- Vendor ECC subset under `ecc/` with attribution headers
- Extend `acps.implement`, `acps.test`, `acps.fix`, `acps.init` to invoke ECC sub-routines (other commands unchanged in MVP)
- Implement hook profile selector with `minimal` default
- Update `config/acps-config.template.yml` with `ecc.*` + `acps.delivery_tier`
- Update `extension.yml` to version 3.0.0; bump tags
- Write `NOTICE` file (MIT attribution)
- Refresh `README.md` with the two entry paths (PM, dev)

**Exit criteria**: Integration test on a sample project — `acps.init` → backlog → spec → baseline → plan → implement → test → uat → release runs end-to-end with ECC sub-routines firing without conflicts. Test summary, governance log, and harness audit artifacts produced.

### Phase 3 — Pilot (2 sprints)
- Roll to 2 internal projects: one greenfield, one brownfield
- One pilot uses `agile-heavy` + `developer` profile; other uses `pmbok-heavy` + `security` profile
- Weekly retros; track:
  - Time from spec to UAT
  - Hook-induced slowdown (target: < 10% on non-edit operations)
  - Number of `acps.cr` fired (signal of scope leakage)
  - Defect leakage past `acps.test` to `acps.uat`
- Capture friction in a `PILOT_FINDINGS.md`; feed into Phase 4

**Exit criteria**: Both pilots reach `acps.release` once each. Findings doc reviewed. Decision: GA-ready or one more iteration.

### Phase 4 — Hardening + Cross-team rollout (2 sprints)
- Address pilot findings (likely: hook noise, ECC skill verbosity, two-system overlap edges)
- Add the upstream-sync script + scheduled PR job for ECC subset refresh
- Add `ENTERPRISE_CONTROLS.md` template wired to `acps.init` for regulated clients
- Tag `v3.0.0`; publish release ZIP
- Internal training: one 60-min recorded walkthrough for PMs + one 90-min hands-on for devs

**Exit criteria**: v3.0.0 tagged; release ZIP installable via `specify extension add acps --from <url>`; training material published.

### Phase 5 — Optional expansions (no fixed date)
- Cross-harness packaging: Codex + Cursor wrappers around the same trunk
- `acps.learn` sub-command exposing ECC continuous-learning (`/learn`, `/evolve`, instincts) under a curation policy
- ECC dashboard (`ecc_dashboard.py`) as optional view over `specs/STATE.md` + governance log
- AgentShield onboarding doc for regulated tier teams
- ECC2 (Rust control-plane) re-evaluation when it reaches beta

### Phase 6 — GA + Steady state
- v3.x release cadence quarterly (ECC sync) or on demand
- Single Slack/Teams channel for team feedback
- Open question per quarter: drop bigpowers entirely if ECC covers all gates? — defer; do not rush

---

## 8. Risks and mitigations

| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| Hook collisions (ECC + acps hooks fire same matcher, double-cost) | High | Medium | Hook profile selector + `disabled_hooks` config; default `minimal`; integration test in Phase 2 validates no double-firing |
| ECC + bigpowers redundancy confuses devs | Medium | Medium | The §4.5 dedup table is the spec; PR template enforces "which skill ran where" in each command file |
| Surface bloat (232 skills tempt scope creep) | Medium | High | Hard cap at 30 in `ecc/skills/`; new additions require ADR with delete proposal in exchange |
| Upstream ECC churn breaks pinned subset | High | Low–Medium | Pin commit in `ecc/VERSION`; quarterly sync PR with human review; never auto-merge |
| MIT attribution miss | Low | High (legal) | `NOTICE` file + per-file SPDX header on vendored files; pre-release checklist item |
| Claude Code plugin validator rejects `extension.yml` after bump | Medium | Medium | Follow ECC's documented `PLUGIN_SCHEMA_NOTES.md`; validate locally with `specify extension add --dev` before tagging |
| Hook performance tanks on large repos | Medium | Medium | `format_typecheck` runs only at Stop (batched), not per-edit — ECC pattern already adopted; load-test in Phase 3 |
| AgentShield licensing surprises | Low | Medium | We **don't** vendor it. Documented as optional install for regulated tiers only. |
| ECC 2.0 (Rust) overtaking ECC 1.x before we finish | Low | Low | Phase 5 task to re-evaluate; current ECC 1.x is stable and won't disappear |
| PM audience drops off because surface still feels dev-heavy | Medium | High | The PM entry path in `README.md` shows only `acps.backlog`, `acps.baseline`, `acps.uat`, `acps.release`, `acps.cr`. Devs see the full 11. Two `quick start` blocks. |

---

## 9. Open questions to resolve before Phase 1

1. **Which 30 skills exactly?** §5 is a draft list; needs PM + 2-dev review.
2. **Should `acps.learn` be in MVP or Phase 5?** Recommendation: Phase 5 (curation policy is not trivial), but ask Daniel.
3. **AgentShield default**: off, or auto-enable for `delivery_tier: pmbok-heavy` + `security` profile? Recommendation: opt-in only — never auto.
4. **Pilot project selection**: which two internal projects? Need PM + delivery lead alignment.
5. **Versioning of vendored ECC**: pin to `v2.0.0-rc.1` for MVP, or wait for `v2.0.0` GA? Recommendation: pin to `v2.0.0-rc.1` and bump after GA — the rc has been stable for the surface we use.
6. **Telemetry**: do we want ECC's `post:ecc-metrics-bridge` writing token/cost metrics into our `specs/project/PROJECT_STATUS.md`? Useful for client billing transparency.
7. **CR cadence**: should `governance-capture` events auto-fire `acps.cr` when they cross a threshold (e.g. > 20% baseline delta)? Or always manual? Recommendation: manual in MVP; revisit after pilots.

---

## 10. Quick-start (what teams see at GA)

### For PMs
```bash
specify extension add acps --from https://github.com/danielvm-ciandt/acps-workflow/releases/download/v3.0.0/acps-workflow-3.0.0.zip
cp config/acps-config.template.yml acps-config.yml   # pick delivery_tier
/acps.init            # bootstraps specs/ + harness audit + enterprise controls
/acps.backlog         # build epic list
/acps.cr              # register a scope change any time
/acps.release         # cut a release with scope review
```

### For Devs
```bash
/acps.spec            # write & clarify the spec; auto-counts BCP/FP/SNAP
/acps.plan            # tech plan + bridge + tasks
/acps.implement       # TDD per task (ECC tdd-guide + bigpowers develop-tdd)
/acps.test            # quality gate (build, lint, test, code review, security)
/acps.fix             # language-aware build/test fix when red
/acps.uat             # UAT artifacts + docs refresh
```

Same install. Same namespace. Same `specs/` tree. Two entry paths.

---

## 11. Decision log

| Date | Decision | Rationale |
|---|---|---|
| 2026-05-25 | ECC absorbed as sub-routines inside `acps.*`, not as parallel commands | Project constraint: single extension, mixed audience |
| 2026-05-25 | Vendor curated 30 skills + 15 agents; do not vendor full ECC | Surface bloat risk; teams can opt-in to extra ECC skills upstream |
| 2026-05-25 | bigpowers stays primary; ECC layered on top for deeper / language-aware gates | Existing bigpowers integration is well-formed; ECC adds depth, not replaces |
| 2026-05-25 | Cross-harness packaging deferred to Phase 5 | MVP risk control; Spec Kit + Claude Code covers the team today |
| 2026-05-25 | AgentShield never vendored; documented as opt-in for regulated tier | Licensing & free OSS path constraint |
| 2026-05-25 | ECC2 (Rust) deferred to Phase 5+ | Upstream alpha; no production signal yet |

---

*Next action*: Daniel reviews this plan, answers the 7 open questions in §9, then we open the Phase 1 sprint with the curation review.
