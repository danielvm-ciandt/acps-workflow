# Release Plan — [Release Name] v[X.Y]

> Living document. Epics and stories are added here as `create-epic-backlog` and `specify` run.  
> Baseline is locked separately in `BASELINE_LATEST.md` — do not edit the baseline file directly.

## Release Summary

| Field | Value |
|-------|-------|
| Release | v[X.Y] |
| Target date | YYYY-MM-DD |
| Initiative Brief | [specs/INITIATIVE_BRIEF_LATEST.md](specs/INITIATIVE_BRIEF_LATEST.md) |
| Baseline | [specs/BASELINE_LATEST.md](specs/BASELINE_LATEST.md) |
| Total BCP | — |
| Epics | — |
| Stories | — |
| Status | Draft |

---

## Epics & Stories

<!-- Template for each epic — repeat as needed -->

### Epic 1: [Epic Title]

> [One sentence describing what this epic delivers end-to-end]

**Priority:** `must` | `should` | `could`  
**Complexity:** `small` | `medium` | `large`  
**Epic BCP:** [sum of story BCPs]  
**Status:** `todo` | `in-progress` | `done`

---

#### Story 1.1: [Story Title]

**As a** [role], **I want** [capability], **so that** [benefit].

**Acceptance criteria:**

```gherkin
Scenario: [happy path — primary success scenario]
  Given [initial context or precondition]
  When [user action or system event]
  Then [expected observable outcome]

Scenario: [alternative or edge case]
  Given [context]
  When [action]
  Then [expected outcome]

Scenario: [failure / error path]
  Given [context]
  When [invalid action or error condition]
  Then [expected error handling or message]
```

**Tasks:**
- [ ] [implementation task — be specific enough to assign]
- [ ] [implementation task]
- [ ] Write unit tests covering the scenarios above
- [ ] Update `docs/` if this changes architecture or design conventions

**BCP:** [count] · **Status:** `todo` | `in-progress` | `done`

---

#### Story 1.2: [Story Title]

**As a** [role], **I want** [capability], **so that** [benefit].

**Acceptance criteria:**

```gherkin
Scenario: [scenario name]
  Given [context]
  When [action]
  Then [expected outcome]
```

**Tasks:**
- [ ] [implementation task]

**BCP:** [count] · **Status:** `todo`

---

### Epic 2: [Epic Title]

> [One sentence]

**Priority:** `must` · **Complexity:** `medium` · **Epic BCP:** — · **Status:** `todo`

---

#### Story 2.1: [Story Title]

**As a** [role], **I want** [capability], **so that** [benefit].

**Acceptance criteria:**

```gherkin
Scenario: [scenario name]
  Given [context]
  When [action]
  Then [expected outcome]
```

**Tasks:**
- [ ] [implementation task]

**BCP:** [count] · **Status:** `todo`

---

## Out of Scope

| Item | Reason | Deferred to |
|------|--------|-------------|
| | | |

## Deferred

| Item | Reason | Target release |
|------|--------|---------------|
| | | |

## Sizing Summary

| Epic | Stories | BCP | Priority | Status |
|------|---------|-----|----------|--------|
| Epic 1 | — | — | must | todo |
| Epic 2 | — | — | must | todo |
| **Total** | | | | |

## Risks & Assumptions

| Risk / Assumption | Impact | Mitigation |
|-------------------|--------|-----------|
| | | |
