---
name: planning
description: Use when Maestro-observable requirements and platform boundary analysis are ready for implementation planning.
---

# Maestro Implementation Planning

## Overview

This skill converts clarified requirements and boundary analysis into a self-contained implementation plan. The plan must be specific enough for an agent with no prior conversation context to execute it task-by-task, with each user-visible behavior starting from a Maestro RED on the selected platform adapter.

## When to Use

Use after [`../requirements/SKILL.md`](../requirements/SKILL.md) has produced:

- `docs/requirements/<feature>/requirements.md`
- `docs/requirements/<feature>/edge-cases.md`

Do not use if requirements are still missing Maestro-observable acceptance criteria or the target platform adapter is unknown.

Do not use if blocking open questions remain. Unresolved items must already be marked as non-goals, deferred items, or accepted risks before planning starts.

## Required Setup

1. Read the requirements and boundary-analysis artifacts.
2. Select and read the target platform adapter:
   - Android: [`../usage/references/platform-adapters/android.md`](../usage/references/platform-adapters/android.md)
   - iOS: [`../usage/references/platform-adapters/ios.md`](../usage/references/platform-adapters/ios.md)
   - Web: [`../usage/references/platform-adapters/web.md`](../usage/references/platform-adapters/web.md)
3. Explore the project structure using the adapter's preflight and discovery rules:
   - app/package/bundle/url identity,
   - build or server commands,
   - install/launch path,
   - UI framework and selector exposure,
   - existing `.maestro/` flows,
   - existing test fixtures, fake data, debug menus, deep links, universal links, route params, or auth setup.
4. Do not invent paths or commands. Inspect them from the project or classify them:
   - blocking unknown: required for the first RED/GREEN cycle and must be resolved before execution,
   - deferred unknown: not needed for the next executable task and explicitly recorded as deferred or accepted risk.
5. Before writing tasks, map files and responsibilities so task decomposition is grounded in the actual project structure.
6. Follow [`../usage/SKILL.md`](../usage/SKILL.md) for Maestro environment preflight commands, YAML draft shape, selector strategy, setup/reset design, and debugging/evidence expectations.

## CHECKPOINT - Planning Gate STOP

Do not hand a plan to execution until the first executable RED/GREEN cycle has all required values below:

- selected platform adapter,
- app identity (`appId` or `url`),
- build/server/install and launch path,
- fixture or reset strategy,
- exact Maestro RED command,
- complete YAML draft or flow file,
- expected missing-behavior RED reason,
- stable selector strategy,
- no blocking unknowns for the first cycle.

| Missing item | Route | Resume planning when |
|---|---|---|
| Target adapter, app identity, launch path, or build/server command | Inspect project files or return to requirements grilling | The value is discovered with source evidence or recorded as a blocker |
| Fixture/reset path required by the first flow | Define fixture, deep link, seeded data, or reset path | The first RED can start from a known state |
| Complete YAML draft, exact command, or expected RED reason | Stay in implementation planning | The task can be executed without inventing test details |
| Stable selector strategy | Read UI/observability source or require labels/IDs/semantics | The flow can target the behavior without coordinates/OCR |
| Deferred unknown affects the first cycle | Reclassify it as blocking | It is resolved, removed from first scope, or accepted as a non-executable risk |

## Output Files

Use this skill's [`templates/implementation-plan.md`](templates/implementation-plan.md) as the default plan shape.

Required output:

```text
docs/plans/<feature>/implementation-plan.md
```

Optional output when the project and runtime are ready for materialized flows:

```text
.maestro/<feature>/*.yaml
```

The implementation plan is required. Creating final `.maestro/<feature>/*.yaml` files during planning is optional, but every planned behavior must include a complete Maestro YAML draft inside the plan: `appId` or `url`, setup/reset, launch path, actions, assertions, expected RED reason, and exact command. A high-level Maestro plan is not enough for execution.

## Plan Requirements

The plan must include:

- Goal and architecture summary.
- Target platform adapter and framework-specific notes.
- Assumptions and non-goals.
- File map with exact create/modify/test paths.
- Coverage map linking requirements, relevant boundaries, tasks, Maestro flow drafts, adapter evidence, and risk status.
- State and wiring map for affected behavior, enough to avoid guessing during execution.
- Environment gate commands from the selected adapter.
- Build/install/server/run commands with expected outputs.
- App identity: Android applicationId, iOS bundle ID, or Web URL.
- Launch strategy.
- Stable selector strategy.
- Test data and fixture strategy.
- Evidence and handoff package plan.
- Bite-sized tasks, each with:
  1. Write or update one Maestro flow/assertion.
  2. Run it and verify correct RED.
  3. Implement the minimal production code.
  4. Run the specific Maestro flow and verify GREEN.
  5. Run relevant regression flows/build/server checks.
  6. Refactor only while green.
  7. Commit or checkpoint the completed behavior when appropriate for the target runtime.

## Task Granularity

Each task should cover one user-visible behavior or one key assertion that can complete its own RED, GREEN, regression check, and evidence capture. If a task spans unrelated assertions or multiple independent UI outcomes, split it.

Good task:

```markdown
### Task 3: Show empty state when there are no saved articles

**Files:**
- Test: `.maestro/articles/empty-state.yaml`
- Modify: `<screen-or-page-file>`

- [ ] **Step 1: Write failing Maestro flow**
Add `.maestro/articles/empty-state.yaml` asserting `No saved articles yet` appears after launching with the empty fixture.

- [ ] **Step 2: Run RED**
Run: `maestro test .maestro/articles/empty-state.yaml`
Expected: FAIL because the empty state is not rendered.

- [ ] **Step 3: Minimal GREEN implementation**
Add empty-state UI and wire it to the saved articles screen.

- [ ] **Step 4: Run GREEN**
Run: `maestro test .maestro/articles/empty-state.yaml`
Expected: PASS.

- [ ] **Step 5: Run regression checks**
Run the feature regression command from this plan.

- [ ] **Step 6: Refactor while green**
Only clean names/duplication after the flow is green; rerun the flow if refactoring changes behavior or selectors.

- [ ] **Step 7: Capture evidence and checkpoint**
Record RED/GREEN outputs and changed files; commit or checkpoint when appropriate.
```

Bad task:

```markdown
### Task 3: Implement saved articles
```

## Maestro Flow Planning Rules

- Prefer stable selectors: text, accessibility labels/identifiers, content descriptions, resource IDs, ARIA labels, and framework semantics that Maestro can resolve.
- Prefer existing user-visible and accessible surface before touching business or production code.
- Plan hierarchy inspection with `inspect_view_hierarchy` or CLI `maestro hierarchy` after relevant flow actions when selector visibility, element attributes, state, or bounds are uncertain.
- Add harmless labels, IDs, semantics, debug entry points, or fixtures only when existing hierarchy and UI source cannot make the behavior Maestro-observable.
- Avoid coordinates and OCR except as documented last resort.
- Include setup/reset steps so flows are repeatable.
- Keep flows focused; one scenario per file when practical.
- Record why each RED should fail.
- Use checkbox steps so plan execution can track progress without rewriting the plan.

## Environment Gate in the Plan

Every plan must include selected-adapter commands to verify:

```bash
maestro --version
java -version
# plus the Android, iOS, or Web adapter's device/simulator/browser/build/install/server checks
maestro test .maestro/<feature>/<flow>.yaml
```

Discover platform-specific values from project files, build settings, manifests, bundle configuration, route/server configuration, build output, existing flows, docs, or code. Record the source of each discovered value. If the project lacks enough information for the first RED/GREEN cycle, mark it as a blocking unknown instead of guessing.

If Maestro, Java, platform tooling, device/simulator/browser/server, app identity, launch path, or setup/reset is missing, record the missing prerequisite and tell the user what to install or provide before execution. Do not claim RED/GREEN evidence from static YAML review.

## State, Fixture, and Evidence Planning

Do not over-specify internals that the executor can discover safely, but do plan the wiring needed to avoid guessing:

- For affected UI behavior, identify the source-of-truth state and the likely path through the screen/page/component, state holder, repository/service, route, or fixture.
- For Maestro TDD failure diagnosis, plan the code context, UI/observability source, runtime logs, and hierarchy signals the executor should inspect before relying on screenshots.
- For fixtures, deep links, universal links, route params, fake data, auth state, or debug-only UI, record scope, reset/cleanup path, and release-safety expectation.
- For final handoff, list the evidence each task should produce: RED/GREEN command output, build/server/regression output, code/UI source context, runtime log excerpts when failures occur, supplemental screenshots only when needed, and changed files.

## Do Not Do / Planning Anti-patterns

| Do not | Why | Do instead |
|---|---|---|
| Say "write tests" without exact Maestro YAML or assertions | Execution cannot classify RED without concrete flow content | Include a precise flow draft or materialized flow file |
| Omit the expected RED reason | The implementer cannot know what failure proves missing behavior | Record the missing user-visible behavior each RED should expose |
| Put unrelated UI outcomes in one task | A broad task blurs RED/GREEN evidence and refactor safety | Split into one flow/assertion per cycle |
| Ignore setup, fixture, auth, route, or reset data | Maestro flows must be repeatable from a known state | Plan the fixture/reset path and release-safety expectations |
| Invent app identity, platform commands, paths, or selectors | Guessed values turn execution into debugging infrastructure | Inspect the project and cite the source, or mark a blocker |
| Plan production-code observability changes before checking existing hierarchy and UI source | It makes automation more invasive than necessary | Inspect hierarchy, source, and logs first; add observability hooks only when genuinely missing |
| Carry first-cycle blocking unknowns into execution | Execution will fill gaps from memory or skip RED | Resolve them before handoff or remove the behavior from executable scope |
| Treat static YAML review or platform-native unit tests as RED | Neither proves Maestro reached a missing user-visible behavior | Require runnable Maestro flow evidence for each behavior cycle |

## Verification Checklist

- [ ] Requirements and boundary analysis were read.
- [ ] Target platform adapter was selected and read.
- [ ] Blocking open questions are resolved or explicitly marked non-goal/deferred/accepted risk.
- [ ] Project structure and build/server commands were inspected.
- [ ] File map lists exact create/modify/test paths.
- [ ] Coverage map links requirements, boundaries, tasks, flows, adapter evidence, and risk status.
- [ ] Plan has environment gate commands from the selected adapter.
- [ ] Maestro preflight commands and missing install/repair steps are documented.
- [ ] Blocking unknowns are resolved before execution handoff.
- [ ] Tasks use checkbox steps suitable for plan execution.
- [ ] Every implementation task starts with a Maestro RED.
- [ ] Every RED has an expected failure reason.
- [ ] Stable selector and fixture strategies are documented.
- [ ] Any production-code observability change is justified by missing existing hierarchy/UI-source coverage.
- [ ] Fixture/deep link/debug-only entry points have scope, reset, and release-safety notes when relevant.
- [ ] Evidence needed for final handoff is listed.
- [ ] Uncovered boundaries are listed as deferred or accepted risks.
