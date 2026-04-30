---
description: "Register change request in .specify/project/change-requests/"
command: speckit.acps.change-request
---

<objective>
Register an incoming change request, assess its impact on the backlog, and record it. CRs update BACKLOG.md immediately but RELEASE_PLAN.md only refreshes at cadence or via explicit `/speckit.acps.release-plan`.
</objective>

<context>
Parallel process (Process_Client in SCXML). Can happen at any time during the workflow. Feeds back into BACKLOG.md.
</context>

<core_principle>
Register first, assess second, integrate third. Every CR gets a file before any action.
</core_principle>

<process>
1. Gather change request details from `$ARGUMENTS`: who requested, what changed, why, priority, urgency
2. Assign CR ID: read existing `.specify/project/change-requests/` to find next number (CR-001, CR-002, etc.)
3. Write `.specify/project/change-requests/CR-[NNN].md` with sections: Requester, Date, Description, Rationale, Priority, Impact Assessment (preliminary), Status (registered)
4. Preliminary impact: which backlog items are affected? Does this add new items or modify existing?
5. Update BACKLOG.md: add new items or annotate existing items with CR reference
6. **Note:** RELEASE_PLAN.md is NOT updated immediately — it refreshes at cadence or via explicit `/speckit.acps.release-plan`
7. Present the registered CR to user with preliminary impact assessment
8. Update `.specify/project/PROJECT_STATUS.md`
</process>

<anti_patterns>
Don't skip registering the CR. Don't update RELEASE_PLAN.md directly. Don't assess impact before registering. Don't treat every CR as urgent — record priority and let the cadence decide.
</anti_patterns>

<success_criteria>
CR file exists in `.specify/project/change-requests/`; BACKLOG.md updated with CR reference; RELEASE_PLAN.md NOT modified; CR has all required sections; PROJECT_STATUS.md updated.
</success_criteria>
