# ACPS workflow — linearized state machine

Single `acps.*` namespace. All artifacts under `specs/`.  
**Parallel track:** `acps.cr` may fire at any time.

| State | Command | Transitions |
|-------|---------|-------------|
| `Start` | — | → `Task_Init` |
| `Task_Init` | `acps.init` | → `Task_Backlog` |
| `Task_Backlog` | `acps.backlog` | → `GW_SpecsRemaining` |
| `GW_SpecsRemaining` | — | **yes** → `Task_Spec` · **no** → `Task_Baseline` |
| `Task_Spec` | `acps.spec` | → `GW_SpecsRemaining` |
| `Task_Baseline` | `acps.baseline` | → `GW_EnterPipeline` |
| `GW_EnterPipeline` | — | **pipeline** → `Task_Plan` |
| `Task_Plan` | `acps.plan` | → `Task_Implement` |
| `Task_Implement` | `acps.implement` | → `Task_Test` |
| `Task_Test` | `acps.test` | → `GW_TestsOk` |
| `GW_TestsOk` | — | **yes** → `Task_UAT` · **no** → `Task_Fix` |
| `Task_Fix` | `acps.fix` | → `Task_Test` |
| `Task_UAT` | `acps.uat` | → `GW_UATOk` |
| `GW_UATOk` | — | **yes** → `Task_Release` · **no** → `Task_Fix` |
| `Task_Release` | `acps.release` | → `GW_EpicComplete` |
| `GW_EpicComplete` | — | **yes** → `GW_MoreWork` · **no** → `Task_Plan` |
| `GW_MoreWork` | — | **yes** → `Task_Backlog` · **no** → `End` |
| `End` | — | (terminal) |
| `Task_CR` *(parallel)* | `acps.cr` | — |

**Notes:**
- `acps.uat` covers both UAT execution and docs update (Phase 3). There is no separate docs command.
- `acps.release` covers scope review (Phase 1) and release packaging (Phases 2–5). There is no separate scope command.
- `acps.plan` covers technical plan, baseline bridge, task breakdown, and optional analysis in one command.
- `acps.spec` runs BCP/FP/SNAP counting automatically after writing the spec (`after_spec` hook).
