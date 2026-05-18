---
description: "Write spec, refine via dialogue, and auto-count scope (BCP/FP/SNAP)"
command: acps.spec
---

<objective>
Write or update the feature specification for a backlog item, refine it through structured dialogue, and automatically count scope complexity (BCP/FP/SNAP) at the end. Produces a spec file under `specs/` and a count file under `specs/counting/`. Feeds `acps.baseline` for sizing and `acps.uat` for acceptance criteria.
</objective>

<context>
Runs inside the spec loop (entered from `GW_SpecsRemaining` yes). Output feeds `acps.baseline` (counting) and `acps.plan` (spec content). Use `$ARGUMENTS` for spec identifier, backlog item reference, or `--count-only` to recount without re-specifying.
</context>

<core_principle>
Only count what is explicitly stated in the spec. Do not infer additional complexity. Start with the simplest classification — when multiple sizes are possible, choose simpler first (XS before S, S before M). Specs must be verifiable: every acceptance criterion maps to a concrete step → verify pair.
</core_principle>

<bigpowers_skills>
Sub-routines invoked by this command:

- **`grill-me`** — surfaces hidden assumptions before the spec is written, one question at a time
- **`elaborate-spec`** — structured refinement dialogue for unclear or thin backlog items
- **`define-success`** — converts acceptance criteria into step → verify pairs; these become the test cases for `acps.uat`
- **`trace-requirement`** — traces the spec back to its backlog item; records traceability link in the spec file
</bigpowers_skills>

<process>
**Phase 1 — Discovery and refinement**

1. Identify the backlog item from `$ARGUMENTS` or by reading `specs/BACKLOG.md` for the next unspecified item.
2. **Gate (thin spec):** if the backlog item description is less than two sentences of meaningful content, run **`elaborate-spec`** first to build it out via dialogue before proceeding.
3. Run **`grill-me`** — surface hidden assumptions and constraints one question at a time.
4. Read `specs/CONSTITUTION.md` for principles and quality gates that constrain spec scope.

**Phase 2 — Write the spec**

5. Write the spec file at `specs/<slug>.md` (or `specs/<area>/<slug>.md` for grouped specs). Include:
   - Title and backlog item reference
   - Context and motivation
   - Scope (in / out of scope for this spec)
   - Functional requirements (numbered)
   - Acceptance criteria (bullets)
   - Non-functional requirements (if applicable)
   - Open questions (to be resolved before planning)
6. Run **`define-success`** — convert each acceptance criterion into a step → verify pair. Append the step → verify table to the spec file under **Verification Pairs**. These feed `acps.uat` directly.
7. Run **`trace-requirement`** — confirm the spec links back to a specific backlog item; write the traceability reference in the spec frontmatter or header.
8. Present the spec draft to the user for confirmation. Allow one round of amendments; do not silently reshuffle after approval.

**Phase 3 — Auto-count (runs automatically after spec is approved)**

9. **Gate:** if spec content is less than 10 characters of meaningful text, skip counting with note: "Spec too thin for assessment".
10. Determine counting mode from `$ARGUMENTS` or `acps-config.yml` (`full` | `simplified` | `fp-snap`). Default: `full`.

For **full** mode (10 functional + 3 NFR dimensions):
- Evaluate each of 10 functional dimensions: Business Rules, Interface Elements, Roles/Permissions, Solution Variabilities, Boundaries, Domain Entities, New Domain Entities, Background Processes, Notifications, Audits.
- For each: determine if applicable (skip if not), assign t-shirt size (XS=1, S=2, M=3, L=5, XL=8), provide rationale.
- **SPECIAL RULE: Boundaries** — use the MAXIMUM (highest points) across all boundaries; do NOT sum.
- **SPECIAL RULE: Business Rules** — count each distinct rule separately and sum them.
- Evaluate 3 NFR dimensions: Quality Attributes, Security & Compliance, User Experience & Accessibility.
- Calculate: Functional BCP = sum dims 1–10; NFR BCP = sum dims 11–13; Total BCP = Functional + NFR; NFR Ratio = NFR BCP / Total BCP × 100%.

For **simplified** mode (3 pillars):
- Assess Business Rules, Interface Elements, Boundaries pillars.
- Total BCP = sum of 3 pillars; NFR BCP = 0.
- Maturity: `score_100 = min(100, max(0, (total_bcp / 20) * 100))`; `complexity_maturity = min(5, max(1, int(score_100 / 20) + 1))`.

For **fp-snap** mode:
- Assess Function Points: identify ILF, EIF, EI, EO, EQ with complexity weights.
- Assess SNAP points: evaluate non-functional subcategories.
- Calculate: `total_size = fp_total + snap_total`; `snap_ratio = snap_total / total_size * 100`.

11. Write `specs/counting/<spec-slug>-count.md` with sections: Spec, Mode, Dimension Breakdown (table: Dimension | Applicable | Size | Points | Rationale), Totals, Maturity Score, Estimation Notes.
12. Update `specs/project/PROJECT_STATUS.md` with spec and count entry.

**`--count-only` mode:** Skip Phases 1–2; re-run Phase 3 on the existing spec file. Overwrite the existing count file.
</process>

<anti_patterns>
Don't infer complexity not stated in the spec. Don't sum boundaries (use max). Don't skip `grill-me` when the backlog item is vague. Don't skip `define-success` — the step → verify pairs are the foundation for UAT. Don't count if spec is too thin. Don't use XL when M suffices — start simple.
</anti_patterns>

<success_criteria>
Spec file exists under `specs/` with all required sections, traceability reference, and step → verify pairs. Count file exists in `specs/counting/` with every applicable dimension scored and rationale provided; boundaries use max rule; totals are calculated; maturity score present. `specs/project/PROJECT_STATUS.md` updated.
</success_criteria>
