---
description: "Assess scope using BCP, FP+SNAP, or simplified counting"
command: speckit.acps.count
---

<objective>
Assess the scope/complexity of a specification using Business Complexity Points (BCP), Function Points + SNAP, or simplified mode. Produces a structured count file.
</objective>

<context>
Can run after `/speckit.specify` (hook: after_specify). Also callable standalone. Feeds into `/speckit.acps.release-plan` for baseline sizing.
</context>

<core_principle>
Only count what is explicitly stated. Do not infer additional complexity. Start with the simplest classification — when multiple are possible, choose simpler first (XS before S, S before M).
</core_principle>

<process>
1. Determine input: spec file path from `$ARGUMENTS`, or find latest spec in `.specify/`
2. Read the spec content. **Gate:** if spec content is less than 10 characters of meaningful text, skip counting ("Spec too thin for assessment").
3. Determine counting mode from `$ARGUMENTS` or config (`.specify/extensions/acps/acps-config.yml`): `full` | `simplified` | `fp-snap`. Default: `full`.
4. For **full** mode (10 functional + 3 NFR dimensions):
   - Evaluate each of 10 functional dimensions: Business Rules, Interface Elements, Roles/Permissions, Solution Variabilities, Boundaries, Domain Entities, New Domain Entities, Background Processes, Notifications, Audits
   - For each: determine if applicable (skip if not), assign t-shirt size (XS=1, S=2, M=3, L=5, XL=8), provide rationale
   - **SPECIAL RULE: Boundaries** — when multiple boundaries identified, use the MAXIMUM (highest points), do NOT sum
   - **SPECIAL RULE: Business Rules** — count each distinct rule separately and sum them
   - Then evaluate 3 NFR dimensions: Quality Attributes, Security & Compliance, User Experience & Accessibility
   - Calculate: Functional BCP = sum of dims 1-10; NFR BCP = sum of dims 11-13; Total BCP = Functional + NFR; NFR Ratio = NFR BCP / Total BCP × 100%
5. For **simplified** mode (3 pillars):
   - Assess Business Rules pillar (total points)
   - Assess Interface Elements pillar (total points)
   - Assess Boundaries pillar (total points)
   - Total BCP = sum of 3 pillars; NFR BCP = 0
   - Maturity: `score_100 = min(100, max(0, (total_bcp / 20) * 100))`; `complexity_maturity = min(5, max(1, int(score_100 / 20) + 1))`
6. For **fp-snap** mode:
   - Assess Function Points: identify ILF, EIF, EI, EO, EQ with complexity weights
   - Assess SNAP points: evaluate non-functional subcategories
   - Calculate: `total_size = fp_total + snap_total`; `snap_ratio = snap_total / total_size * 100`
   - Maturity: `score_100 = min(100, max(0, (total_size / 50) * 100))`; `complexity_maturity = min(5, max(1, int(score_100 / 20) + 1))`
7. Write `.specify/counting/[spec-slug]-count.md` with sections: Spec, Mode, Dimension Breakdown (table: Dimension | Applicable | Size | Points | Rationale), Totals (Functional BCP, NFR BCP, Total BCP, NFR Ratio), Maturity Score, INVEST Assessment (optional), Estimation Notes
8. Update `.specify/project/PROJECT_STATUS.md`
</process>

<anti_patterns>
Don't infer complexity not stated in the spec. Don't sum boundaries (use max). Don't skip dimensions without checking. Don't use XL when M suffices — start simple. Don't count if spec is too thin.
</anti_patterns>

<success_criteria>
Count file exists in `.specify/counting/`; every applicable dimension has size + rationale; boundaries use max rule; totals are calculated; maturity score present; PROJECT_STATUS.md updated.
</success_criteria>
