---
description: "Assess scope impact in .specify/scope/"
command: speckit.acps.scope
---

<objective>
When scope creep is detected, assess the impact on baseline (RELEASE_PLAN.md), technical plan, schedule, and resources. Produce a scope impact draft with classification and recommendation.
</objective>

<context>
Entered from GW_ScopeTrigger (yes) after docs. Before GW_EpicoCompleto.
</context>

<core_principle>
Assess first, decide later. Present findings to user — never auto-implement scope changes.
</core_principle>

<process>
1. Identify the scope change trigger: what is the proposed addition/change? Read from `$ARGUMENTS` or recent conversation.
2. Load context: read RELEASE_PLAN.md (baseline), BACKLOG.md, plan.md, recent spec artifacts.
3. Impact analysis: for each artifact, determine what changes. Classify: scope / budget / timeline / resource impact.
4. Classify severity: Minor (can implement within current sprint) / Moderate (requires backlog reorg) / Major (requires fundamental replan).
5. Draft specific change proposals: show old → new for affected artifacts.
6. Write `.specify/scope/scope-impact-draft-[date].md` with sections: Trigger, Impact Analysis (by artifact), Severity Classification, Change Proposals (old→new), Recommendation, Risks.
7. **MANDATORY DECISION GATE:** Present findings. Options: 1) Approve changes 2) Defer to next cycle 3) Reject 4) Discuss further. Wait for user response.
8. If approved, note that BACKLOG.md and RELEASE_PLAN.md should be updated via their respective commands.
9. Update `.specify/project/PROJECT_STATUS.md`.
</process>

<anti_patterns>
Don't auto-approve scope changes. Don't skip impact analysis. Don't modify RELEASE_PLAN.md or BACKLOG.md directly — those updates go through their own commands. Don't present vague impact ("might affect things").
</anti_patterns>

<success_criteria>
Scope impact draft exists; impact per artifact is specific; severity is classified; decision gate was presented; user made a decision; PROJECT_STATUS.md updated.
</success_criteria>
