# ACPS workflow — linearized state machine

Derived from the team-trunk SCXML. **Gateways** use events shown on transitions.  
**Parallel track:** `Process_Client` may fire **`speckit.acps.change-request`** at any time (`/workflow.change-request` → `speckit.acps.change-request`).

| State | Title | Command | Transitions |
|-------|-------|---------|-------------|
| `Start_Inicio` | Start | — | → `Task_Setup` |
| `Task_Setup` | Setup | `/workflow.setup` → `speckit.acps.setup` | → `Task_Constitution` |
| `Task_Constitution` | Constitution | `/speckit.constitution` | → `Task_Backlog` |
| `Task_Backlog` | Create epic backlog | `/workflow.create-epic-backlog` → `speckit.acps.create-epic-backlog` | → `GW_BacklogLista` |
| `GW_BacklogLista` | Gateway: remaining specs? | — | **yes** → `Task_Specify` · **no** → `Activity_0xqnl2a` |
| `Task_Specify` | Specify | `/speckit.specify` | → `Activity_02mqji3` |
| `Activity_02mqji3` | Clarify (optional) | `/speckit.clarify` | → `GW_BacklogLista` |
| `Activity_0xqnl2a` | Release plan | `/workflow.release-plan` → `speckit.acps.release-plan` | → `GW_PostReleaseSpecs` |
| `GW_PostReleaseSpecs` | Gateway: enter per-spec pipeline | — | **pipeline** → `Task_PlanTech` |
| `Task_PlanTech` | Plan | `/speckit.plan` | → `Task_SpeckTasks` |
| `Task_SpeckTasks` | Tasks | `/speckit.tasks` | → `Activity_0g0snbx` |
| `Activity_0g0snbx` | Analyze (optional) | `/speckit.analyze` | → `Task_Implement` |
| `Task_Implement` | Implement | `/speckit.implement` | → `Task_Test` |
| `Task_Test` | Test | `/workflow.test` → `speckit.acps.test` | → `GW_TestsOk` |
| `GW_TestsOk` | Gateway: tests OK? | — | **yes** → `Task_UAT` · **no** → `Task_Bugfix` |
| `Task_Bugfix` | Bugfix | `/workflow.bugfix` → `speckit.acps.bugfix` | → `GW_TestsOk` |
| `Task_UAT` | UAT | `/workflow.uat` → `speckit.acps.uat` | → `GW_UATOk` |
| `GW_UATOk` | Gateway: UAT OK? | — | **yes** → `Task_Docs` · **no** → `Task_Bugfix` |
| `Task_Docs` | Docs | `/workflow.docs` → `speckit.acps.docs` | → `GW_ScopeTrigger` |
| `GW_ScopeTrigger` | Gateway: run scope review? | — | **yes** → `Task_ScopeMgmt` · **no** → `GW_EpicoCompleto` |
| `Task_ScopeMgmt` | Scope management | `/workflow.scope` → `speckit.acps.scope` | → `GW_EpicoCompleto` |
| `GW_EpicoCompleto` | Gateway: epic complete? | — | **yes** → `Task_Release` · **no** → `Task_PlanTech` |
| `Task_Release` | Release | `/workflow.release` → `speckit.acps.release` | → `GW_MaisTrabalho` |
| `GW_MaisTrabalho` | Gateway: more epics / work? | — | **yes** → `Task_Backlog` · **no** → `End_Fim` |
| `End_Fim` | End | — | (terminal) |
| `Task_ChangeRequest` | Change request (parallel) | `/workflow.change-request` → `speckit.acps.change-request` | — (parallel process; `Process_Client`) |

**Note:** The extension also defines **`speckit.acps.plan-bridge`** between baseline and execution plans; run it after `speckit.plan` when following the full ACPS per-spec pipeline (see `extension.yml`).
