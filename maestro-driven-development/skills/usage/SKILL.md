---
name: usage
description: Use for Maestro MCP/CLI preflight, adapter selection, selectors, YAML authoring, evidence, and failure diagnosis. In MDD feature work, use only as support for requirements/planning/execution, not as a shortcut around the required requirements and Maestro test plan phases.
---

# Maestro Usage

## Overview

Use this skill for Maestro MCP, Maestro CLI, flow authoring, platform adapter selection, environment preflight, debugging, and evidence collection. It is not the RED-GREEN-REFACTOR discipline; follow [`../tdd/SKILL.md`](../tdd/SKILL.md) for the TDD cycle.

This skill is platform-aware. The core rules apply to every Maestro-supported target, and the concrete platform details live in adapters:

- Android: [`references/platform-adapters/android.md`](references/platform-adapters/android.md)
- iOS: [`references/platform-adapters/ios.md`](references/platform-adapters/ios.md)
- Web: [`references/platform-adapters/web.md`](references/platform-adapters/web.md)

Maestro Cloud, OTP/auth cookbooks, and project-specific test infrastructure are out of scope unless the target project already uses them or the user explicitly requests them.

## When to Use

Use before:
- selecting a platform adapter,
- using Maestro MCP tools for device/browser reconnaissance or UI interaction,
- writing Maestro YAML drafts,
- running claimed RED/GREEN flows,
- handing an implementation plan to execution,
- debugging Maestro flow failures,
- reporting Maestro evidence.

Do not claim Maestro TDD evidence until the MCP/CLI preflight and selected platform adapter preflight have passed or the blocker is reported.

## Direct Invocation Guard

If this skill is invoked for user-visible feature work and the user asked for Maestro Driven Development/MDD, do not treat Maestro usage as the whole workflow. Route through the workflow entry point unless requirements and the Maestro Test Plan already exist.

Use this skill directly only for Maestro setup, preflight, selector/debugging, or flow-failure diagnosis. For implementation work, return to requirements or planning when the request lacks clarified acceptance criteria, target adapter, setup/reset, flow drafts, expected RED reasons, or exact commands.

## Select a Platform Adapter

Select exactly one primary adapter before planning a runnable flow:

| Target | Adapter | App identity |
| --- | --- | --- |
| Android emulator or physical device | [`platform-adapters/android.md`](references/platform-adapters/android.md) | Android package/applicationId as `appId` |
| iOS Simulator | [`platform-adapters/ios.md`](references/platform-adapters/ios.md) | iOS bundle ID as `appId` |
| Web browser | [`platform-adapters/web.md`](references/platform-adapters/web.md) | Web `url` |

React Native and Flutter are framework-specific notes inside Android or iOS adapters. They are not standalone platform adapters because the runtime target, install/launch path, and diagnostics still come from Android or iOS.

If the target is multi-platform, plan one adapter-specific execution path per platform or explicitly accept the untested platform as a risk.

## Environment Preflight

Maestro MCP is the preferred interactive path when the target runtime exposes MCP tools. Maestro CLI remains required for durable flow files, reproducible RED/GREEN commands, and runtimes without MCP.

### CHECKPOINT - STOP Before RED/GREEN Evidence

Do not write or claim RED/GREEN evidence until both checks below are classified:

1. MCP path: available, unavailable with CLI fallback, or blocked.
2. Selected platform adapter path: preflight passed, or blocked with the missing prerequisite.

If either path is blocked, report the blocker and stop before TDD evidence. Static YAML review, screenshots, or a configured `.mcp.json` do not prove that Maestro can reach the target behavior.

Run or discover enough to prove the local Maestro path is usable:

```bash
maestro --version
java -version
# plus selected adapter checks for Android, iOS, or Web
```

Also confirm:
- selected platform adapter,
- Maestro MCP server is configured with command `maestro` and args `["mcp"]` when the runtime supports MCP,
- the local runtime has loaded/enabled the Maestro MCP service and exposes the Maestro MCP tools for this session,
- app identity: Android applicationId, iOS bundle ID, or Web URL,
- app launch path,
- target device, simulator, browser, or app server,
- existing `.maestro/` conventions,
- fixture/reset path for the scenario.

Record where project-specific values came from: manifests, build settings, Gradle tasks, Xcode project settings, server config, route config, build output, existing flows, docs, or code.

If a prerequisite is missing, stop before RED and tell the user exactly what is missing. Typical install or repair hints:

```bash
curl -Ls "https://get.maestro.mobile.dev" | bash
brew install openjdk@17
```

Do not silently install tools, start emulators/simulators/servers, or mutate system setup unless the user approves.

### Local MCP Service Check

When the runtime supports MCP, checking `.mcp.json` is not enough. Before using MCP-driven reconnaissance or claiming MCP evidence:

1. Confirm the session exposes Maestro MCP tools such as `list_devices`, `inspect_view_hierarchy`, `run_flow`, and `take_screenshot`.
2. Run a lightweight MCP check, preferably `list_devices`, to prove the MCP client has started `maestro mcp` and can talk to the local service.
3. If the tools are missing or the check fails, classify it as an MCP/service setup blocker. Tell the user to reload the plugin/session, enable the plugin, or start a new Codex thread after the plugin is installed.
4. Do not run `maestro mcp` as a one-shot shell preflight command; it is a long-running MCP server command that the MCP client should launch from `.mcp.json`.

If MCP remains unavailable, continue only with the CLI path and say that MCP reconnaissance/evidence is unavailable. CLI evidence still requires the normal Maestro CLI and selected platform adapter checks.

### MCP / CLI Fallback Routes

| Trigger | First action | Still blocked |
|---|---|---|
| Maestro MCP tools are not exposed in the session | Classify as MCP/service setup blocker; tell the user to reload the plugin/session or enable the plugin | Use CLI only and say MCP reconnaissance/evidence is unavailable |
| `list_devices` or the first MCP health check fails | Classify as MCP/service setup blocker; do not call it RED | Use CLI preflight if available; otherwise stop with environment blocker |
| MCP `run_flow`, `run_flow_files`, or syntax validation fails before the target app state | Reproduce through `maestro test --debug <flow>` when a flow file exists | Collect CLI/debug/log evidence; do not claim MCP evidence |
| `maestro --version` or `java -version` fails | Report the missing CLI prerequisite and installation or repair hint | Stop as environment blocker; do not install tools silently |
| Flow does not reach the intended screen/page | Classify as launch/setup/driver/bootstrap/selector before missing behavior | Fix setup or route to the selected adapter; not a correct RED |

## Maestro MCP Usage

The plugin configures Maestro MCP in `.mcp.json`:

```json
{
  "mcpServers": {
    "maestro": {
      "command": "maestro",
      "args": ["mcp"]
    }
  }
}
```

Use Maestro MCP for interactive reconnaissance and evidence when available:

1. Discover devices or runtimes with `list_devices`; start an emulator, simulator, browser, or server only when the user or project policy allows it.
2. Launch or stop the target app/site with the available Maestro MCP commands for the selected adapter.
3. Inspect UI with `inspect_view_hierarchy` before choosing selectors, and re-inspect after flow actions when the current element tree matters.
4. Use `take_screenshot` only as supplemental evidence when hierarchy, code/UI source context, and runtime logs do not explain the failure or when visual mismatch must be shown.
5. Interact with the app using `tap_on`, `input_text`, and navigation commands when debugging a flow.
6. Validate and run flows with `check_flow_syntax`, `run_flow`, or `run_flow_files`.
7. Use `query_docs` or `cheat_sheet` for Maestro syntax questions instead of guessing.

Prefer MCP for reconnaissance and debugging because it can inspect hierarchy and drive the current target without hand-rolled shell parsing. Prefer CLI commands for the final reproducible evidence block when the plan requires exact command output. When using the CLI path, `maestro hierarchy` can be run after flow operations to inspect the current screen's element tree, including element attributes and bounds when the platform exposes them.

## Minimal Flow Shape

Use this skill's [`templates/maestro-flow.yaml`](templates/maestro-flow.yaml) as the starter flow template when the project does not already have a better local convention.

Android and iOS flows use `appId`:

```yaml
appId: ${APP_ID}
---
- launchApp:
    clearState: true
- extendedWaitUntil:
    visible: "<ready or target state>"
    timeout: 10000
- tapOn: "<stable action target>"
- assertVisible: "<expected user-visible result>"
```

Web flows use `url`:

```yaml
url: ${BASE_URL}
---
- openLink: ${BASE_URL}
- extendedWaitUntil:
    visible: "<ready or target state>"
    timeout: 10000
- tapOn: "<stable action target>"
- assertVisible: "<expected user-visible result>"
```

Use `clearState`, deep links, universal links, route params, fake data, seeded backend data, or debug-only setup only when they are needed for repeatability. Setup failure is not a RED.

## How to Use Maestro

### Install / Verify

```bash
curl -Ls "https://get.maestro.mobile.dev" | bash
brew install openjdk@17
maestro --version
java -version
```

Use the install commands as guidance for the user. Do not run installation or mutate the user's system unless they approve.

### Write a Flow

Create focused mobile flows under `.maestro/<feature>/` or the project's existing Maestro directory:

```yaml
appId: <applicationId-or-bundleId>
---
- launchApp
- tapOn: "<action label or stable selector>"
- assertVisible: "<expected result>"
```

For Web flows, declare `url` and open that URL instead of launching an app:

```yaml
url: <web-url>
---
- openLink: <web-url>
- tapOn: "<action label or stable selector>"
- assertVisible: "<expected result>"
```

If the feature needs known data or navigation state, add setup before the action:

```yaml
appId: ${APP_ID}
---
- launchApp:
    clearState: true
- openLink: "<debug-or-test-fixture-deep-link>"
- extendedWaitUntil:
    visible: "<ready marker>"
    timeout: 10000
- tapOn: "<action target>"
- assertVisible: "<expected result>"
```

### Run and Debug

```bash
# `maestro mcp` is the MCP server command configured in `.mcp.json`; MCP clients launch it.
maestro test .maestro/<feature>/<flow>.yaml
maestro test --debug .maestro/<feature>/<flow>.yaml
maestro studio
```

Use Maestro MCP `inspect_view_hierarchy` for interactive inspection when MCP is available; the MCP client starts the configured `maestro mcp` server. Use `maestro studio` for local manual exploration. Use `--debug` when a flow fails and the failure reason is unclear. Capture platform/runtime logs for timing-related, crash, state, setup, auth, server, or permission failures. Capture screenshots only when the information from code context, UI/observability source, hierarchy, and runtime logs is still insufficient, or when a visual mismatch needs supplemental proof.

After a flow action changes the screen, prefer `inspect_view_hierarchy` or CLI `maestro hierarchy` to confirm the current UI tree before changing selectors or production UI. The hierarchy output can expose text, ids, accessibility attributes, state, and bounds, which often makes a selector or timing issue diagnosable without adding new app code.

### Reuse Setup with Sub-Flows

Use sub-flows only for repeated setup or navigation sequences:

```text
.maestro/
├── flows/
│   └── reset-to-empty-state.yaml
└── saved-articles/
    └── empty-state.yaml
```

```yaml
- runFlow:
    file: ../flows/reset-to-empty-state.yaml
```

Keep assertions close to the main feature flow so the RED reason stays clear.

## Selector Strategy

For deeper selector guidance, use this skill's [`references/maestro-selector-strategy.md`](references/maestro-selector-strategy.md) and the selected platform adapter.

Prefer non-invasive selector and diagnosis paths before touching business or production code. Read the current hierarchy, UI source, and runtime logs first; add labels, ids, semantics, fixtures, or debug entry points only when the behavior cannot be made Maestro-observable through existing user-visible or accessible surface.

Prefer stable selectors:

1. Stable user-visible text when product copy is stable.
2. Accessibility labels, identifiers, content descriptions, ARIA labels, or semantic names.
3. Platform IDs or resource IDs when the adapter supports them.
4. Framework semantics/test tags that Maestro can resolve.
5. Relative selectors when repeated elements need context.

Avoid coordinates, screenshots, OCR, and index-based selectors for normal flows. They are last-resort choices and must be documented as brittle.

Useful Maestro selector refinements:

```yaml
- tapOn:
    id: "submit-button"
    enabled: true

- assertVisible:
    id: "terms-checkbox"
    checked: true

- tapOn:
    text: "Delete"
    childOf:
      id: "item-card-42"
```

Use `optional: true` only for genuinely optional cleanup, such as dismissing a system dialog that may or may not appear. Optional actions must not hide the behavior being tested.

## Waiting and Repeatability

- Prefer `extendedWaitUntil` over sleeps or arbitrary delays.
- Wait for a stable screen/page-ready marker when the target has async loading.
- Reset state explicitly when the scenario depends on a known starting point.
- Use sub-flows only for repeated setup sequences.
- Keep one flow focused on one behavior or key assertion.

## Platform Dialogs, Permissions, and Browser State

System prompts, browser state, retained app state, cookies, keychain, local storage, permissions, and auth state are setup concerns unless the requirement is specifically about them. Handle expected prompts and state explicitly through the selected adapter.

If these block the target behavior unexpectedly, classify the result as setup/permission/auth/browser-state failure, not RED.

## Failure Classification

Before deciding a flow is RED, classify the failure:

- environment: Maestro, Java, platform tooling, device/simulator/browser/server, or network unavailable,
- build/install/server: app/site cannot build, install, serve, or expose the target route,
- launch: wrong app identity, bundle ID, package, URL, or target cannot start,
- fixture/setup: seed, fake data, deep link, backend/mock, auth, permission, state reset, or browser state failed,
- YAML: syntax or unsupported command,
- selector: target not found because selector is wrong or brittle,
- runtime crash: app/page crashes before the behavior under test,
- platform-driver/bootstrap: adapter-specific driver or simulator/browser bootstrap failed before the target state,
- masked action: `optional: true` hid a required interaction,
- missing behavior: flow reached the target state and failed because the requested user-visible behavior does not exist.

Only the final category can count as a correct RED.

## Diagnosis Source Order

When a flow fails, use evidence in this order:

1. Code context for the behavior under test: screen/page/component, state holder, fixtures, routes, deep links, command wiring, and async side effects.
2. UI/observability source that affects Maestro selectors: layout, accessibility labels/identifiers, content descriptions, resource IDs, ARIA labels, semantic HTML, or framework semantics.
3. Platform/runtime logs around the failure window: Android logcat, iOS simulator/app logs, browser/server logs, backend fixture logs, or project-specific logs.
4. Runtime hierarchy inspection for selector visibility and accessible state.
5. Screenshots only as supplemental evidence when the previous sources do not explain the problem, or when the issue is specifically visual.

Screenshots are not a replacement for reading implementation context or runtime logs. Do not classify a TDD failure from screenshots alone.

## Debugging and Evidence

Useful commands:

```bash
# `maestro mcp` is the MCP server command configured in `.mcp.json`; MCP clients launch it.
maestro test .maestro/<feature>/<flow>.yaml
maestro test --debug .maestro/<feature>/<flow>.yaml
maestro studio
```

Capture evidence for handoff:

- selected platform adapter and app identity,
- preflight command results,
- code and UI/observability context that explains the failure or fix,
- runtime log excerpts around the RED/GREEN failure or runtime issue,
- hierarchy inspection when selectors are unclear,
- MCP tool evidence when used, such as device/runtime list, hierarchy snapshot, or flow run result,
- RED output and failure reason,
- GREEN output,
- final regression output,
- screenshots only when needed as supplemental visual evidence,
- changed flow files or YAML drafts.

Do not summarize a flow as passing unless the command was actually run.

## Do Not Do / Anti-patterns

| Do not | Why | Do instead |
|---|---|---|
| Skip Maestro, Java, adapter, app identity, or runtime preflight | Missing setup blocks real TDD evidence | Run preflight or report the concrete blocker before RED |
| Treat setup, permission, auth, launch, YAML, selector, or driver failure as RED | These failures do not prove missing user-visible behavior | Fix setup or route to the selected adapter, then rerun |
| Add labels, ids, debug UI, or other production-code changes before inspecting the current hierarchy | This can make automation intrusive and hide existing observable state | Use `inspect_view_hierarchy` or `maestro hierarchy` after the relevant flow action, then change app code only if observability is genuinely missing |
| Use coordinates, screenshots, OCR, or indexes as normal selectors | They are brittle and hide semantics regressions | Prefer stable text, accessibility, IDs, semantics, or contextual relative selectors |
| Put `optional: true` around a required behavior step | Optional actions can hide the failure the flow must expose | Use optional only for genuine cleanup or non-deterministic system dialogs |
| Write broad multi-scenario flows for one RED | Failure classification becomes ambiguous | Keep one flow focused on one behavior or key assertion |
