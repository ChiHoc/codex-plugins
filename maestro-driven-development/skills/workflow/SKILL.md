---
name: workflow
description: "Mandatory entry point for Maestro Driven Development/MDD on Android, iOS, or Web work. Use when the user asks to build, fix, implement, test, or plan with MDD or Maestro TDD. Always clarify requirements first, write a Maestro test plan inside the implementation plan second, then execute with Maestro-only RED/GREEN evidence."
---

# Maestro Driven Development Orchestration

## Overview

Use this as the entry point for feature work on Maestro-supported targets. It keeps the agent from jumping straight into code by enforcing staged work with a selected platform adapter and Maestro usage preflight before claimed RED/GREEN evidence:

1. Requirements grilling.
2. Platform adapter selection.
3. Maestro test planning inside implementation planning.
4. Plan execution.
5. Maestro-only TDD cycles inside plan execution.
6. Final reporting inside plan execution.

The bundle optimizes for more reliable code generation by turning ambiguous product intent into explicit, Maestro-observable acceptance criteria before implementation starts.

## Mandatory Entry Contract

When the user asks to use Maestro Driven Development, MDD, Maestro TDD, or this plugin for a feature, UI bug, or user-visible behavior change, start here and follow this contract:

1. Clarify requirements before planning or coding.
2. Produce a Maestro test plan before implementation. The plan must name the target adapter, app identity or URL, setup/reset, stable selectors, YAML drafts or flow files, expected RED reasons, and exact commands.
3. Execute only from the approved plan, task by task, with Maestro-only RED/GREEN evidence.

Do not jump directly to implementation, Maestro YAML authoring, or a RED/GREEN cycle from a short user request. If requirements or a Maestro test plan are missing, stay in the owning phase and create or repair those artifacts first.

## When to Use

Use for:
- Android, iOS, or Web feature work where Maestro can launch, inspect, and interact with the target.
- Native, React Native, Flutter, or browser UI work when the selected platform adapter can provide enough preflight and diagnosis evidence.
- Tasks where user-visible behavior can be verified through Maestro.
- Bug fixes that can be reproduced by a Maestro flow.

Do not use as-is for:
- Pure library/module work with no UI-observable behavior.
- Targets Maestro cannot launch or inspect.
- Situations where the required device, simulator, browser, or app server cannot be made available and the user still expects real TDD evidence.

## Required Phase Order

### CHECKPOINT - Phase Gates STOP

Do not skip, merge, or reorder these gates. Before moving to the next phase, verify the current phase has its required inputs, outputs, and blocker route:

| Gate | Required input | Required output | Stop when | Route back to |
|---|---|---|---|---|
| 1. Requirements grilling complete | User request plus existing project docs/code when available | Requirements and boundary artifacts with Maestro-observable acceptance or recorded risks | Actor, trigger, data state, acceptance, relevant boundaries, target platform, or Maestro observability is missing | Requirements grilling |
| 2. Platform adapter selected | Requirements and target environment | Android, iOS, or Web adapter selected with supported-target evidence | Target is not Maestro-supported or platform prerequisites are unknown for the first cycle | Requirements grilling or Maestro usage |
| 3. Implementation plan complete | Requirements, boundary artifacts, and selected adapter | Self-contained plan with file map, coverage map, Maestro YAML drafts, expected RED reasons, commands, fixtures, and evidence plan | Any first-cycle file, appId/url, command, flow draft, setup/reset, or expected RED reason is guessed or missing | Implementation planning |
| 4. Execution gate clear | Approved implementation plan | Task-by-task execution state with current blockers resolved | Plan contradicts requirements, lacks evidence contracts, or asks for scope not accepted in requirements | Implementation planning or requirements grilling |
| 5. Correct RED observed | Runnable Maestro flow and platform preflight | Failing flow output caused by missing user-visible behavior | Failure is environment, build/install/server, launch, fixture/setup, YAML, selector, crash, permission, or platform-driver related | Maestro usage or Maestro TDD diagnosis |
| 6. GREEN and regression verified | Minimal implementation for one behavior | Passing flow output plus required build/regression evidence | GREEN requires unrelated behavior, verification fails, or evidence is not real command output | Current TDD cycle or plan execution |
| 7. Final handoff ready | Completed, deferred, or blocked task list | Final evidence package and explicit remaining risks | Any claimed flow/build was not run or any risk is unstated | Plan execution |

If a gate fails, stop in the current phase. Do not fill gaps from memory or treat static review as runtime evidence.

### Phase 1 - Requirements Grilling

Read and follow [`../requirements/SKILL.md`](../requirements/SKILL.md).

Required outputs:
- `.requirements/<feature>/requirements.md`
- `.requirements/<feature>/edge-cases.md`

The agent must not proceed until every requirement is either:
- Maestro-observable,
- explicitly out of scope,
- or recorded as an accepted Maestro-only coverage risk.

The requirements must also identify the target platform or record it as a blocker.

### Phase 2 - Maestro Test + Implementation Planning

Read and follow [`../planning/SKILL.md`](../planning/SKILL.md).

Required outputs:
- `.plans/<feature>/implementation-plan.md`
- complete Maestro YAML drafts inside the implementation plan for every planned behavior.
- a dedicated Maestro Test Plan section in the implementation plan covering setup/reset, selectors, commands, expected RED reasons, and evidence.

Creating `.maestro/<feature>/*.yaml` files during planning is optional. The plan itself must still contain enough YAML, setup/reset, assertions, commands, and expected RED reasons for execution without guessing.

Every implementation task must identify:
- the selected platform adapter,
- the expected RED Maestro command,
- why the RED should fail,
- the minimal GREEN change,
- coverage mapping from requirements and relevant boundaries,
- and final verification/evidence commands.

Read and follow [`../usage/SKILL.md`](../usage/SKILL.md) during planning when selecting the platform adapter, authoring Maestro YAML drafts, designing selectors and setup/reset, and defining environment preflight commands.

### Phase 3 - Plan Execution

Read and follow [`../execution/SKILL.md`](../execution/SKILL.md).

Required inputs:
- `.requirements/<feature>/requirements.md`
- `.requirements/<feature>/edge-cases.md`
- `.plans/<feature>/implementation-plan.md`

The agent must review the plan for blockers before touching production code, execute tasks in order, track progress, and stop rather than guessing when instructions or evidence are insufficient.

Before any claimed RED/GREEN evidence, follow [`../usage/SKILL.md`](../usage/SKILL.md) and the selected platform adapter to run the environment preflight. If preflight cannot run, report the concrete blocker and installation or repair step; do not claim RED/GREEN evidence.

### Phase 4 - Maestro-Only TDD Cycles

Read and follow [`../tdd/SKILL.md`](../tdd/SKILL.md) inside each behavior implementation cycle.

Iron law:

```text
NO PRODUCTION CODE FOR A BEHAVIOR WITHOUT A MAESTRO FLOW THAT FAILED FIRST FOR THE CORRECT REASON.
```

Build, server, launch, YAML syntax, selector, permission, device/simulator/browser, or setup errors do not count as RED. Fix the environment or test before implementing the feature.

Platform-native unit tests are not allowed for this bundle's TDD cycles. Do not create, run, or count them as RED/GREEN/regression evidence; route every behavior cycle through Maestro.

### Final Reporting

Before reporting completion inside plan execution:
- Run the relevant Maestro flows and capture the real command output.
- Run the project build/regression command used during implementation.
- For failed or ambiguous Maestro TDD steps, diagnose with platform diagnostics before relying on screenshots; screenshots are supplemental evidence only.
- Check requirements fit, implementation plan completion, changed project files and Maestro flows, RED/GREEN evidence, final build/regression evidence, skipped flows, flaky selectors, untested boundaries, and accepted risks.
- Fix critical findings before claiming completion. Important findings must be fixed, blocked with a concrete reason, or explicitly accepted.
- Report the final evidence package and remaining risks.

## Platform Adapters

Select exactly one primary platform adapter for each executable plan:

- Android: [`../usage/references/platform-adapters/android.md`](../usage/references/platform-adapters/android.md)
- iOS: [`../usage/references/platform-adapters/ios.md`](../usage/references/platform-adapters/ios.md)
- Web: [`../usage/references/platform-adapters/web.md`](../usage/references/platform-adapters/web.md)

React Native and Flutter are framework-specific notes inside the Android or iOS adapter, not standalone platform adapters.

## Core Decisions

- The plugin source lives in the workspace first; Codex plugin installation is the primary path, and direct skill-directory installation is a fallback for runtimes that do not support plugins.
- The bundle supports Maestro-driven Android, iOS, and Web targets through platform adapters.
- Maestro is the default required TDD test type.
- Platform-native unit tests are not permitted inside this bundle's TDD workflow.
- Requirements must become Maestro-observable or be marked as risk/non-goal.
- Stable selectors are mandatory; coordinates and OCR are last-resort brittle fallbacks.
- Maestro usage preflight is required before claiming RED/GREEN evidence.
- Maestro TDD failure diagnosis must combine code context, platform UI/observability source, runtime logs, and hierarchy where available; screenshots are supplemental when those sources are insufficient.
- Plan execution is an explicit stage, and final handoff must include real verification evidence.

## Do Not Do / Phase-Skip Blacklist

| Do not | Why | Do instead |
|---|---|---|
| Skip requirements because the user says they are clear | Missing actor, trigger, data, acceptance, target, or observability breaks planning | Verify the requirements gate and record risks/non-goals explicitly |
| Plan code tasks without Maestro RED tasks | Code tasks lose measurable behavior evidence | Give every code task a Maestro RED command and expected failure reason |
| Treat environment errors as RED | A correct RED proves missing behavior, not broken tooling | Route environment failures through Maestro usage or the selected adapter |
| Write huge multi-behavior flows | Failures become hard to classify and refactor safely | Split large requirements into small red-green cycles |
| Use fragile selectors as the default | Coordinates, OCR, and indexes create flaky evidence | Add semantic labels, accessibility identifiers, resource IDs, or framework semantics |
| Execute an incomplete plan | Execution will guess missing commands, flows, or app identity | Return to planning instead of filling gaps from memory |
| Use platform-native unit tests as fallback TDD | This bundle's RED/GREEN evidence is Maestro-only | Route every behavior cycle through a Maestro flow |
| Skip final evidence review | Completion claims become unverifiable | Check evidence, changed files, skipped flows, and open risks before handoff |
| Cross a failed phase gate | Incomplete artifacts leak into later stages | Stop and route back to the owning phase |

## Verification Checklist

- [ ] Requirements grilling completed and wrote requirements + boundary-analysis artifacts.
- [ ] Target platform was selected and the matching adapter was read.
- [ ] Implementation planning completed and wrote a bite-sized plan with Maestro test steps.
- [ ] Plan execution reviewed and executed the plan task-by-task.
- [ ] Maestro TDD ran each RED/GREEN cycle with real command output.
- [ ] Final handoff evidence reviewed requirements fit, changed files, RED/GREEN output, and build/regression output.
- [ ] All claimed Maestro flows were actually run.
- [ ] Uncovered or non-observable requirements are explicitly listed.
