# <Feature> Maestro Implementation Plan

> **For agentic workers:** REQUIRED: execute this plan task-by-task using the bundle's plan-execution document. Each behavior implementation must follow the Maestro TDD document and the selected platform adapter. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:**

**Architecture:**

**Target Platform / Adapter:** Android | iOS | Web

**Tech Stack:**

## Inputs

- Requirements: `docs/requirements/<feature>/requirements.md`
- Boundary analysis: `docs/requirements/<feature>/edge-cases.md`
- Platform adapter:

## Environment Gate

```bash
maestro --version
java -version
# Add selected adapter checks here:
# Android: adb/device/build/install
# iOS: Xcode/simulator/build/install
# Web: URL/server/build/browser-state checks
maestro test .maestro/<feature>/<flow>.yaml
```

- App identity (`appId` or `url`):
- Build/server command:
- Install/launch path:
- Device/simulator/browser:
- Discovery sources:

## Blocking Unknowns

List only values that would block the first executable RED/GREEN cycle. Resolve these before execution.

| Unknown | Why it blocks | How to resolve | Status |
| --- | --- | --- | --- |

## Coverage Map

| Requirement | Relevant boundary | Platform adapter evidence | Task | Maestro flow draft/file | Risk status |
| --- | --- | --- | --- | --- | --- |

## Selector Strategy

## Fixture / Test Data Strategy

## State and Wiring Map

- Source-of-truth state:
- UI entry point / navigation:
- Data or fixture injection path:
- Debug-only or test-only surfaces:
- Platform-specific observability notes:

## Tasks

### Task 1: <Small Behavior>

**Objective:**

**Files:**
- Test: `.maestro/<feature>/<flow>.yaml`
- Modify:

- [ ] **Step 1: Write failing Maestro flow**

```yaml
# Android/iOS use appId. Web uses url instead of appId.
appId: <applicationId-or-bundleId>
# url: <web-url>
---
# Include setup/reset needed for repeatability.
- launchApp
- assertVisible: "<expected text>"
```

- [ ] **Step 2: Run RED**

```bash
maestro test .maestro/<feature>/<flow>.yaml
```

Expected: FAIL because <target behavior does not exist yet>.

- [ ] **Step 3: Minimal GREEN implementation**

- [ ] **Step 4: Run GREEN**

```bash
maestro test .maestro/<feature>/<flow>.yaml
```

Expected: PASS.

- [ ] **Step 5: Run task regression/build checks**

```bash
# Use the selected platform adapter's build/server/regression command.
maestro test .maestro/<feature>/<flow>.yaml
```

- [ ] **Step 6: Refactor while green**

Rerun the current flow or relevant regression command after any behavior-affecting refactor.

- [ ] **Step 7: Capture evidence**

- RED output:
- Diagnosis context:
  - Code path:
  - UI/observability source:
  - Runtime logs:
  - Hierarchy inspection:
- GREEN output:
- Regression output:
- Supplemental screenshots/artifacts, only if code/UI/log/hierarchy evidence is insufficient or visual mismatch needs proof:
- Changed files:

- [ ] **Step 8: Checkpoint according to project/runtime policy**

Commit only when the user, project policy, or approved plan explicitly requires it.

## Regression Verification

```bash
# Use the selected platform adapter's build/server/regression command.
maestro test .maestro/<feature>/
```

## Final Handoff Review

Before reporting completion, check:

- requirements and boundary-analysis fit,
- selected platform adapter fit,
- changed files,
- RED/GREEN evidence,
- final build/server/regression output,
- diagnosis context: code path, UI/observability source, runtime logs, and hierarchy before screenshots,
- screenshots/artifacts are supplemental only,
- skipped verification and accepted risks.

## Evidence Package

- Changed files:
- RED/GREEN command outputs:
- Build/server/regression outputs:
- Diagnosis context:
  - Code path:
  - UI/observability source:
  - Runtime logs:
  - Hierarchy inspection:
- Supplemental screenshots/artifacts, only if needed:
- Deferred items / accepted risks:

## Deferred / Accepted Risks
