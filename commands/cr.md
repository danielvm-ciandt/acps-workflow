---
description: "Register change request, assess impact, update specs/BACKLOG.md"
command: acps.cr
---

<objective>
Register an incoming change request, assess its structured impact on the backlog and related artifacts, and record it. CRs update `specs/BACKLOG.md` immediately but `specs/RELEASE_PLAN.md` only refreshes at cadence or via explicit `acps.baseline`.
</objective>

<context>
Parallel process — may run at any time during the workflow. Feeds back into `specs/BACKLOG.md`.
</context>

<core_principle>
Register first, assess second, integrate third. Every CR gets a file before any action.
</core_principle>

<bigpowers_skills>
Sub-routines invoked by this command:

- **`assess-impact`** — replaces the informal "preliminary impact" step with a structured change matrix covering scope, budget, timeline, and resource dimensions
- **`trace-requirement`** — identifies which specs, plans, and test cases are affected by the change; records traceability links
</bigpowers_skills>

<process>
1. Gather change request details from `$ARGUMENTS`: who requested, what changed, why, priority, urgency.
2. Assign CR ID: read existing `specs/project/change-requests/` to find the next number (CR-001, CR-002, etc.).
3. Write `specs/project/change-requests/CR-[NNN].md` with sections: Requester, Date, Description, Rationale, Priority, Status (registered).
4. Run **`assess-impact`** — structured impact matrix: which artifacts are affected (backlog, specs, plan, tests), classify scope / budget / timeline / resource impact, severity (Minor / Moderate / Major).
5. Run **`trace-requirement`** — identify which specs, plan sections, UAT checks, and counting files are affected; record traceability links in the CR file.
6. Update `specs/BACKLOG.md`: add new items or annotate existing items with the CR reference.
7. **Note:** `specs/RELEASE_PLAN.md` is NOT updated immediately — it refreshes at cadence via `acps.baseline`.
8. Present the registered CR and impact assessment to the user.
9. Update `specs/project/PROJECT_STATUS.md`.
</process>

<anti_patterns>
Don't skip registering the CR before assessing impact. Don't update `specs/RELEASE_PLAN.md` directly — that belongs to `acps.baseline`. Don't treat every CR as urgent — record priority and let the cadence decide. Don't present vague impact ("might affect things") — `assess-impact` must produce specific artifact-level findings.
</anti_patterns>

<success_criteria>
CR file exists in `specs/project/change-requests/` with all required sections including structured impact matrix. `specs/BACKLOG.md` updated with CR reference. `specs/RELEASE_PLAN.md` NOT modified. `specs/project/PROJECT_STATUS.md` updated.
</success_criteria>
