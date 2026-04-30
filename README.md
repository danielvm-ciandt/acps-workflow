# ACPS Workflow — Spec Kit Extension

**ACPS Workflow** layers a delivery trunk on top of [Spec Kit](https://github.com/github/spec-kit): epic backlog cycling, baseline **release planning**, **plan bridging**, quality gates (test → UAT → docs), optional **scope** review, **release** packaging, **change-request** handling, and **BCP / FP / SNAP** scope counting — while reusing Spec Kit’s core spec/plan/tasks/implement loop.

## Installation

**From GitHub:**

```bash
specify extension add acps --from https://github.com/danielvm/acps-workflow
```

**Local development (path to this repo):**

```bash
specify extension add --dev /path/to/acps-workflow
```

On first use, copy `config/acps-config.template.yml` to `acps-config.yml` in your project (or rely on defaults where supported).

## Commands

Extension commands are exposed as `speckit.acps.*` (your IDE may show `/workflow.*` aliases).

| Command | Description |
|---------|-------------|
| `speckit.acps.setup` | Bootstrap project environment, agent context, and status files. |
| `speckit.acps.create-epic-backlog` | Build an ordered epic backlog (e.g. `BACKLOG.md`). |
| `speckit.acps.release-plan` | Size the backlog and establish baseline scope in `RELEASE_PLAN.md`. |
| `speckit.acps.plan-bridge` | Align baseline / release plan with the technical plan from `/speckit.plan`. |
| `speckit.acps.test` | Run tests and record evidence (e.g. `TEST_SUMMARY.md`). |
| `speckit.acps.bugfix` | Track and fix failures under `.specify/bugs/`. |
| `speckit.acps.uat` | User acceptance testing with artifacts under `.specify/uat/`. |
| `speckit.acps.docs` | Refresh documentation and agent-facing project notes. |
| `speckit.acps.scope` | Optional scope-impact assessment under `.specify/scope/`. |
| `speckit.acps.release` | Cut a release: changelog / release notes / artifacts. |
| `speckit.acps.change-request` | Register change requests under `.specify/project/`. |
| `speckit.acps.count` | Assess scope via BCP, simplified counting, or FP+SNAP. |

## Workflow

The diagram follows the team-trunk state machine. **Blue** nodes use **Spec Kit core** commands; **orange** nodes use **ACPS extension** commands. **Pink** diamonds are gateways.

```mermaid
flowchart TD
  subgraph trunk_start["Trunk: setup & backlog"]
    Start_Inicio([Start]) --> Task_Setup[setup]
    Task_Setup --> Task_Constitution[constitution]
    Task_Constitution --> Task_Backlog[create-epic-backlog]
    Task_Backlog --> GW_BacklogLista{remaining specs?}
  end

  subgraph baseline_loop["Baseline: specify loop"]
    GW_BacklogLista -->|yes| Task_Specify[specify]
    Task_Specify --> Activity_02mqji3[clarify]
    Activity_02mqji3 --> GW_BacklogLista
  end

  GW_BacklogLista -->|no| Activity_0xqnl2a[release-plan]
  Activity_0xqnl2a --> GW_PostReleaseSpecs{pipeline}
  GW_PostReleaseSpecs -->|pipeline| Task_PlanTech[plan]

  subgraph per_spec["Per-spec: plan to test"]
    Task_PlanTech --> Task_SpeckTasks[tasks]
    Task_SpeckTasks --> Activity_0g0snbx[analyze]
    Activity_0g0snbx --> Task_Implement[implement]
    Task_Implement --> Task_Test[test]
  end

  Task_Test --> GW_TestsOk{tests OK?}
  GW_TestsOk -->|yes| Task_UAT[uat]
  GW_TestsOk -->|no| Task_Bugfix[bugfix]
  Task_Bugfix --> GW_TestsOk

  Task_UAT --> GW_UATOk{UAT OK?}
  GW_UATOk -->|yes| Task_Docs[docs]
  GW_UATOk -->|no| Task_Bugfix

  Task_Docs --> GW_ScopeTrigger{scope review?}
  GW_ScopeTrigger -->|yes| Task_ScopeMgmt[scope]
  GW_ScopeTrigger -->|no| GW_EpicoCompleto{epic complete?}
  Task_ScopeMgmt --> GW_EpicoCompleto

  GW_EpicoCompleto -->|yes| Task_Release[release]
  GW_EpicoCompleto -->|no| Task_PlanTech

  Task_Release --> GW_MaisTrabalho{more epics?}
  GW_MaisTrabalho -->|yes| Task_Backlog
  GW_MaisTrabalho -->|no| End_Fim([End])

  subgraph parallel_cr["Parallel process"]
    Task_ChangeRequest[change-request]
  end

  classDef speckit fill:#e3f2fd,stroke:#1565c0,stroke-width:2px
  classDef acps fill:#fff3e0,stroke:#e65100,stroke-width:2px
  classDef gw fill:#fce4ec,stroke:#ad1457,stroke-width:2px
  classDef terminal fill:#f5f5f5,stroke:#616161,stroke-width:2px

  class Task_Constitution,Task_Specify,Activity_02mqji3,Task_PlanTech,Task_SpeckTasks,Activity_0g0snbx,Task_Implement speckit
  class Task_Setup,Task_Backlog,Activity_0xqnl2a,Task_Test,Task_Bugfix,Task_UAT,Task_Docs,Task_ScopeMgmt,Task_Release,Task_ChangeRequest acps
  class GW_BacklogLista,GW_PostReleaseSpecs,GW_TestsOk,GW_UATOk,GW_ScopeTrigger,GW_EpicoCompleto,GW_MaisTrabalho gw
  class Start_Inicio,End_Fim terminal
```

**`plan-bridge`** is not a separate node in the SCXML above; run it after **`speckit.plan`** when using ACPS hooks so the technical plan matches the baseline (see `extension.yml`).

## Configuration

- Template: **`config/acps-config.template.yml`**
- Copy to **`acps-config.yml`** and set project name, artifact paths, quality gates, and counting defaults (BCP maturity thresholds, `default_mode`, auto-count after specify).

## Counting

The **`speckit.acps.count`** command supports:

- **full** — Full **BCP** (10 functional + 3 NFR dimensions) using the rubric in `memory/bcp-rubric.md` and prompts under `prompts/`.
- **simplified** — Reduced prompts (e.g. boundaries, business rules, interface elements) for faster triage.
- **fp-snap** — **Function points** + **SNAP**-style assessment paths for teams tracking IFPUG-style sizing alongside BCP.

See `memory/acps-methodology.md` for folder layout (including `.specify/counting/`).

## Agent context

- **`memory/acps-methodology.md`** — Command namespaces, trunk order, folders, CR/release policies.
- **`memory/acps-states.md`** — State IDs, commands, and gateway transitions.
- **`memory/bcp-rubric.md`** — Quick BCP scoring reference.

## License

MIT
