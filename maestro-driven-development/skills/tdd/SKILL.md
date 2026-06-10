---
name: tdd
description: Use when implementing Maestro-supported features with strict RED-GREEN-REFACTOR cycles where Maestro flows are the required tests.
---

# Maestro TDD

## Overview

This skill defines RED-GREEN-REFACTOR discipline where the required test artifact is a Maestro flow. Platform-native unit tests are not allowed inside this TDD workflow: do not create them, run them, or count them as RED/GREEN/regression evidence for a behavior cycle. Follow [`../usage/SKILL.md`](../usage/SKILL.md) and the selected platform adapter for Maestro environment preflight, YAML authoring, selector details, debugging, and evidence collection.

Core principle:

```text
If you did not watch the Maestro flow fail for the expected missing-behavior reason, you do not know whether it tests the requirement.
```

## When to Use

Use after implementation planning has produced a plan with Maestro-first tasks and a selected platform adapter.

Use for:
- Android, iOS, or Web user-visible behavior changes.
- UI-visible bug fixes.
- Behavior changes that can be verified through Maestro.

Do not use as a substitute for real execution when Maestro, platform tooling, device/simulator/browser/server, build/install, or launch prerequisites are missing.

## Iron Law

```text
NO PRODUCTION CODE FOR A BEHAVIOR WITHOUT A MAESTRO FLOW THAT FAILED FIRST FOR THE CORRECT REASON.
```

If production code was written first, stop and isolate the unverified implementation for that behavior. Only revert changes that are clearly yours from the current behavior cycle; if ownership is unclear, ask the user before changing or removing code.

A platform-native unit test failure is not a valid RED in this skill. Do not substitute JVM, XCTest, Jest, browser component tests, or module unit test tasks for a Maestro flow.

## Environment Gate

Before RED, follow [`../usage/SKILL.md`](../usage/SKILL.md) and the selected platform adapter to verify or report:

```bash
maestro --version
java -version
# plus selected adapter checks for Android, iOS, or Web
```

Also determine:
- selected platform adapter,
- app identity (`appId` for Android/iOS or `url` for Web),
- build/server/install path,
- launch command/flow,
- device/emulator, iOS simulator, or web runtime availability,
- scenario setup/reset strategy.

If any prerequisite is missing, stop and report the blocker. Do not claim TDD evidence from static YAML review.

See [`../usage/references/maestro-environment-gate.md`](../usage/references/maestro-environment-gate.md) and the selected adapter:

- Android: [`../usage/references/platform-adapters/android.md`](../usage/references/platform-adapters/android.md)
- iOS: [`../usage/references/platform-adapters/ios.md`](../usage/references/platform-adapters/ios.md)
- Web: [`../usage/references/platform-adapters/web.md`](../usage/references/platform-adapters/web.md)

## CHECKPOINT - Environment Failure Classification

Do not enter RED until the flow can reach the intended app or page state. Classify setup failures before touching production code.

| Trigger | Classification | Action | RED can resume when |
|---|---|---|---|
| `deviceInfo` fails before the app screen is reached | driver/bootstrap blocker | Inspect Maestro output, adapter notes, platform logs, and device/runtime state | `deviceInfo` succeeds and the flow reaches the target app |
| gRPC `UNAVAILABLE` or `Command failed (tcp:<port>): closed` | Android driver blocker | Repair or restart the driver/device path through the Android adapter; do not edit production code | The same flow reaches the intended app behavior |
| Build, install, server, or launch fails | environment blocker | Fix setup or report the missing prerequisite | Launch succeeds and the behavior assertion runs |
| Fixture, deep link, auth, permission, or reset fails | fixture/setup blocker | Fix setup data or route before behavior assertions | The flow starts from the planned known state |
| Target node is absent from hierarchy because of selector or semantics | selector/observability blocker | Inspect hierarchy and UI/observability source; add planned labels/IDs/semantics when needed | The assertion targets the intended behavior |

## RED - Write and Run Failing Maestro Flow

1. Write the smallest Maestro flow or assertion for one behavior.
2. Use the stable selector and setup/reset strategy from the Maestro usage document and selected adapter.
3. Reset app/browser state or load test data so the flow is repeatable.
4. Run the specific flow:

```bash
maestro test .maestro/<feature>/<flow>.yaml
```

A correct RED must fail because the requested user-visible behavior is missing.

If the flow passes immediately, it is not a valid RED for new behavior. Either the behavior already exists, the assertion is wrong, or the requirement should be reframed. Stop and resolve that before writing production code.

Not correct RED:
- Platform-native unit test failure.
- YAML syntax error.
- App/site cannot build, install, serve, or launch.
- App/page crashes before the tested behavior.
- Required device, simulator, browser, server, or network path is missing.
- Selector is wrong or too brittle.
- Assertion checks the wrong thing.
- Backend/mock/fixture/deep link/setup is missing or failed.
- Permission, system dialog, auth, or browser state blocks the target behavior unexpectedly.
- Wrong app identity, bundle ID, package, or URL was launched.
- Platform-driver/bootstrap failure before the flow reaches the target.
- `optional: true` masked an action required for the behavior under test.

Fix those problems and rerun until RED is correct.

After fixing an invalid RED cause, rerun the same flow from a clean setup. Classify RED only after the flow reaches the intended app state and fails because the requested user-visible behavior is missing.

If production code was written before the Maestro flow, stop and isolate that behavior cycle. Only revert code that you can clearly identify as your own unverified change; otherwise ask the user how to handle the existing code. Do not treat the earlier code as validated.

## Failure Diagnosis During TDD

When a Maestro flow fails during RED, GREEN, refactor, or regression, diagnose from the behavior's real implementation context before relying on screenshots:

1. Read the relevant code path: screen/page/component, state holder, fixture/deep link/setup, command wiring, and async side effects.
2. Read the relevant UI/observability source: layout, accessibility labels/identifiers, content descriptions, resource IDs, ARIA labels, or framework semantics that determine what Maestro can see and select.
3. Inspect runtime logs around the failure window: Android logcat, iOS simulator/app logs, web dev server/browser/app logs, or project-specific logs from the selected adapter.
4. Use hierarchy inspection when selector visibility, state, attributes, or bounds are unclear. With CLI, run `maestro hierarchy` after the relevant flow operation; with MCP, use `inspect_view_hierarchy`.
5. Capture screenshots only when the code, UI source, hierarchy, and runtime log evidence do not provide enough information, or when visual mismatch needs supplemental proof.

Do not use screenshots as the primary diagnosis path for ordinary TDD failures. A screenshot alone is not enough to classify RED, explain GREEN failure, or justify a production change.

Before adding labels, IDs, debug UI, fixtures, or other production-code observability hooks, inspect the current hierarchy and UI source. Prefer existing user-visible and accessible surface; touch business or production code only when observability is genuinely missing.

## GREEN - Minimal Implementation

Implement only enough production code to pass the current Maestro flow.

Allowed in GREEN:
- straightforward UI state wiring,
- minimal fake/test fixture entry point if planned,
- adding semantic labels, accessibility identifiers, resource IDs, ARIA labels, or framework semantics for stable selectors only after existing hierarchy and UI source prove insufficient,
- hardcoded or narrow implementation if it is the smallest step and will be refactored while green.

Not allowed:
- implementing future requirements,
- broad refactors unrelated to the failing flow,
- changing the test to match wrong behavior,
- skipping the flow because manual testing looked fine,
- adapting previously written production code that never had a correct RED,
- writing or running platform-native unit tests to satisfy or supplement the current TDD cycle.

Run the specific flow until it passes.

## REFACTOR - Clean Up While Green

After GREEN:
- simplify code,
- remove duplication,
- improve names,
- keep selectors stable,
- keep test data setup explicit.

Run the same Maestro flow after each meaningful refactor. If it fails, revert or take a smaller refactor step.

## Repeat

Move to the next planned Maestro flow/assertion only after the current one is green.

For a large requirement, repeat small cycles rather than implementing against many failing flows at once.

## Build and Regression Verification

Before completion, run the selected adapter's build/server/regression commands plus the relevant Maestro flows, for example:

```bash
maestro test .maestro/<feature>/
```

Include real command output in the final report. Do not add platform-native unit test tasks to the TDD verification set.

## Common Pitfalls

1. **Counting a broken environment as RED.** RED must prove missing behavior.
2. **Writing code before the flow.** Revert or isolate and restart.
3. **Using huge flows.** One scenario/assertion per cycle.
4. **Overusing coordinates/OCR.** Use stable selectors; add labels/IDs/semantics if needed.
5. **No deterministic setup.** Flaky state makes TDD evidence meaningless.
6. **Changing tests during GREEN to make them pass.** Only fix test mistakes that prevent correct RED; otherwise fix production behavior.
7. **Treating an immediately passing flow as RED.** Passing first means the flow did not prove missing behavior.
8. **Falling back to platform-native unit tests.** This skill is Maestro-only; unit tests are outside the TDD workflow.

## Verification Checklist

- [ ] Loaded/read the implementation plan.
- [ ] Loaded/read the selected platform adapter.
- [ ] Environment gate passed with real command output.
- [ ] Each behavior has a Maestro flow written before implementation.
- [ ] Each flow was observed failing for the expected reason.
- [ ] No platform-native unit tests were created, run, or counted as TDD evidence.
- [ ] Failures were diagnosed using code context, UI/observability source, runtime logs, and hierarchy before screenshot evidence.
- [ ] Minimal code made each flow pass.
- [ ] Refactors happened only while green.
- [ ] Relevant feature flows and build/server/regression command pass at the end.
- [ ] Final response includes commands run, pass/fail output, skipped flows, and risks.
