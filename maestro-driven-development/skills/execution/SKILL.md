---
name: execution
description: Use only when approved requirements, boundary analysis, and a Maestro-backed implementation plan with a complete Maestro test plan are ready to execute on a selected platform adapter.
---

# Maestro Plan Execution

## Overview

Use this stage after [`../planning/SKILL.md`](../planning/SKILL.md) has produced an approved implementation plan. It adapts plan-execution discipline for Maestro-supported work: load the plan, review it critically, execute tasks in order, and stop rather than guessing when requirements, environment, adapter, or evidence are insufficient.

This document does not replace [`../tdd/SKILL.md`](../tdd/SKILL.md). It coordinates the plan execution loop and applies the Maestro RED-GREEN-REFACTOR discipline for each user-visible behavior.

## When to Use

Use when:
- `.plans/<feature>/implementation-plan.md` exists.
- Requirements and boundary-analysis artifacts exist.
- The plan names Android, iOS, or Web as the selected platform adapter.
- The next step is to implement the planned work.

Do not use when:
- Requirements are still unresolved.
- The implementation plan lacks exact files, complete Maestro YAML drafts or flow files, commands, adapter selection, or expected RED reasons.
- The implementation plan lacks a dedicated Maestro Test Plan section.
- The required device, simulator, browser, app server, or platform tooling cannot be made available and the user expects real TDD evidence.

## Direct Invocation Guard

If this skill is invoked before requirements artifacts and the implementation plan exist, stop and route to the workflow entry point. If the plan exists but lacks a Maestro Test Plan with executable flow drafts, commands, expected RED reasons, setup/reset, and evidence expectations, route back to planning. Do not patch production code while repairing the plan.

## Required Setup

1. Read:
   - `.requirements/<feature>/requirements.md`
   - `.requirements/<feature>/edge-cases.md`
   - `.plans/<feature>/implementation-plan.md`
   - the selected platform adapter under [`../usage/references/platform-adapters/`](../usage/references/platform-adapters/)
2. Review the plan before touching production code:
   - Are tasks small enough to execute one behavior at a time?
   - Does each task name exact files and commands?
   - Does each task start with a Maestro RED and include a complete YAML draft or flow file?
   - Is the expected RED failure reason specific and user-visible?
   - Are fixture/setup steps repeatable?
   - Are blockers, deferred boundaries, and accepted risks explicit?
   - Does the coverage map link requirements, relevant boundaries, tasks, flows, adapter evidence, and risk status?
   - Are blocking unknowns resolved for the first executable task?
   - Is the evidence package enough for final handoff?
   - Is the state/wiring map sufficient to avoid guessing without over-constraining details the executor can verify from code?
3. Follow [`../usage/SKILL.md`](../usage/SKILL.md) and the selected adapter to run or report the environment preflight before claiming RED/GREEN evidence.
4. If the plan has critical gaps, stop and ask for clarification or return to implementation planning.

## CHECKPOINT - Plan Readiness STOP

Do not enter the execution loop until the plan has exact files, runnable Maestro flow drafts or flow files, commands, selected adapter, expected RED reasons, fixture/reset path, and an evidence plan.

If any item is missing, route back to implementation planning. Do not fill gaps from memory, static review, screenshots, or prior conversation.

## Execution Loop

For each planned task:

1. Mark the task as in progress in the agent's task tracker if available.
2. Confirm the task gate:
   - blocking unknowns needed for this task are resolved,
   - fixture/reset path is available,
   - platform adapter, app identity (`appId` or `url`), build/server/install, and launch path are still valid,
   - no requirement or boundary contradiction has appeared,
   - planned observability changes still require touching production code after checking the current hierarchy and UI source.
3. Follow the task's requirement, RED/GREEN, selector, fixture, safety, adapter, and verification contract. Implementation details may be adjusted after reading current code, as long as acceptance criteria and evidence stay intact. Record the reason for any deviation from the planned implementation.
4. Follow [`../tdd/SKILL.md`](../tdd/SKILL.md) for the behavior under implementation:
   - if the plan contains only a YAML draft, materialize it into a runnable flow file first,
   - do not change production code before the flow file can run,
   - run it,
   - verify correct RED,
   - implement the minimal production change,
   - run the flow to GREEN,
   - refactor only while green.
5. Run the task's specified regression checks.
6. Record evidence:
   - preflight result or blocker,
   - selected adapter and app identity,
   - materialized flow file or YAML draft,
   - commands run,
   - pass/fail result,
   - meaningful failure text for RED,
   - code path and UI/observability source used to diagnose failures,
   - runtime log excerpts around runtime, setup, selector, or state failures,
   - hierarchy inspection when selector visibility, state, attributes, or bounds are unclear,
   - reason any production-code observability change was unavoidable,
   - GREEN output,
   - build/server/regression output,
   - screenshots only when code, UI source, hierarchy, and runtime logs are insufficient or visual mismatch needs proof,
   - changed production files.
7. Mark the task complete only after verification passes. A task may be marked deferred or blocked when a risk was already accepted in requirements/planning or the user accepts it during execution. Do not self-accept new failed verification as an accepted risk.

## Stop Conditions

Stop and report before continuing when:

- The plan instruction is unclear or contradicts requirements.
- A required file, command, device, simulator, browser, app identity, fixture, or selector cannot be discovered.
- Maestro, platform tooling, build/install/server, launch, or test setup fails before the behavior is reached.
- RED is caused by syntax, launch, environment, wrong selector, platform-driver issue, or crash instead of missing behavior.
- GREEN requires implementing unrelated future behavior.
- Verification fails repeatedly.
- User approval is needed for secrets, permissions, external services, destructive commands, or publishing.

## Replanning Routes

Route blockers to the right stage instead of guessing:

| Trigger | Route | Resume condition |
|---|---|---|
| Requirement contradiction or new domain ambiguity | Return to requirements grilling | Requirement, boundary, non-goal, deferred item, or accepted risk is recorded |
| Missing flow draft, commands, file paths, coverage map, adapter choice, or evidence plan | Return to implementation planning | The executable task contains exact files, flow, command, expected RED, fixture/reset, and evidence contract |
| Missing Maestro, Java, platform tooling, device/simulator/browser/server, app identity, build/install/setup/reset, or unclear flow failure | Follow the Maestro usage document and selected adapter | Preflight passes or blocker is reported with the missing prerequisite |
| Wrong RED classification or production code written before RED | Follow the Maestro TDD document and restart the behavior cycle | The flow reaches the intended app state and fails for missing user-visible behavior |
| Failure cause unclear after flow output | Inspect code context, UI/observability source, runtime logs, and hierarchy before screenshots | Failure is classified as environment, setup, selector, crash, bootstrap, or missing behavior |
| Implementation would change scope or acceptance criteria | Ask the user or return to planning | Scope change is accepted and reflected in requirements or plan artifacts |

## Progress Reporting

During execution, report:

- current task name,
- current phase: plan review, RED, GREEN, refactor, regression, or blocked,
- selected platform adapter,
- commands that produced decisive evidence,
- blockers with failure classification.

Report at each task completion or block. For long-running tasks, report at decisive RED, GREEN, regression, or blocker points.

Do not claim a task is complete because code was written. Completion requires the planned Maestro evidence or an explicitly accepted risk.

## Completion Criteria

Execution batch is ready for final handoff when:

- Every planned task is completed, deferred, or blocked with a clear reason.
- All claimed Maestro RED/GREEN cycles were actually run.
- Build/server/regression commands from the plan were run or explicitly blocked.
- Evidence is ready for final reporting.

Feature implementation is complete only when:

- No task remains blocked.
- Every claimed behavior is GREEN.
- Deferred tasks or risks were already accepted by requirements/planning or by the user during execution.
- Final build/server/regression evidence exists or is blocked with a concrete environment reason.

## Do Not Do / Execution Red Flags

| Do not | Why | Do instead |
|---|---|---|
| Execute an incomplete plan | Missing plan details force guessing during RED/GREEN | Return to planning and fill exact files, flow, commands, RED reason, and evidence |
| Treat plan execution as free-form coding | It breaks the requirements-to-evidence chain | Follow task order unless a blocker requires replanning |
| Skip correct RED | Maestro TDD governs every user-visible behavior cycle | Materialize the flow, run it, and verify missing-behavior RED first |
| Count environment, launch, setup, selector, or bootstrap failure as progress | These failures do not prove behavior is missing | Classify and fix setup before claiming RED/GREEN evidence |
| Add labels, ids, debug UI, fixtures, or other production-code observability hooks before checking current hierarchy and UI source | This makes automation more invasive than necessary | Use `inspect_view_hierarchy` or `maestro hierarchy` after the relevant flow action, then touch production code only if observability is genuinely missing |
| Move past failed verification | Unresolved failures become untracked risk | Stop, classify, and resolve or ask the user to accept the risk |
| Force a stale plan through current code | Runtime or code evidence can invalidate the plan | Pause and route the mismatch back to requirements or planning |
| Self-accept new risk | Only requirements/planning or the user can accept new execution risk | Block until fixed or explicitly accepted |

## Verification Checklist

- [ ] Requirements, boundary analysis, implementation plan, and selected adapter were read.
- [ ] Plan was reviewed for blockers before implementation.
- [ ] Coverage map, blocking unknowns, state/wiring notes, and evidence plan were checked.
- [ ] Maestro environment preflight passed or blockers were reported with install/repair steps.
- [ ] Task-level gates were checked before each task.
- [ ] Each task was executed in order or explicitly replanned.
- [ ] Any deviation from planned implementation details was recorded with a reason.
- [ ] Each behavior followed the Maestro TDD document.
- [ ] YAML drafts were materialized into runnable flow files before production code changed.
- [ ] No platform-native unit tests were created, run, or counted as TDD evidence.
- [ ] Failures were diagnosed with code context, UI/observability source, runtime logs, and hierarchy before screenshot evidence.
- [ ] Production-code observability changes were avoided unless existing hierarchy/UI-source coverage was insufficient.
- [ ] RED/GREEN evidence was captured for every claimed behavior.
- [ ] Regression commands were run or blocked with reasons.
- [ ] New risks were not self-accepted.
- [ ] Evidence and open risks are ready for final handoff.
