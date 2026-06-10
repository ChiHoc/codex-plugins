# iOS Platform Adapter

Use this adapter when the Maestro target is an iOS app running on an Xcode Simulator. Native SwiftUI, UIKit, React Native, and Flutter iOS targets all use this adapter; framework differences belong in the framework-specific notes below.

## Supported Target

The target is supported when Maestro can drive an iOS Simulator through native Apple development tools, the simulator build is installed or installable, the `appId` maps to the iOS bundle identifier, and flows can reach user-visible UI from a repeatable state.

## Preflight

Run or report:

```bash
maestro --version
java -version
xcode-select -p
xcrun simctl list devices available
xcodebuild -list
xcodebuild -scheme <Scheme> -configuration <Debug> -sdk iphonesimulator build
xcrun simctl install <device> <path-to-app.app>
maestro test .maestro/<feature>/<flow>.yaml
```

Discover and record:

- Xcode workspace/project and scheme.
- Build configuration.
- Simulator device id or name.
- `.app` path for the simulator build.
- Bundle ID used as Maestro `appId`.
- App launch route and any required universal link, deep link, launch argument, or debug fixture.
- Existing `.maestro/` flow conventions.
- Reset strategy, usually `launchApp: { clearState: true }`, `clearState`, deep links, or debug fixtures.

## Minimal Flow Shape

```yaml
appId: ${APP_ID}
---
- launchApp:
    clearState: true
- extendedWaitUntil:
    visible: "<ready marker>"
    timeout: 10000
- tapOn: "<stable action target>"
- assertVisible: "<expected visible result>"
```

Use `maestro test -e APP_ID=<ios.bundle.id> .maestro/<feature>/<flow>.yaml` when keeping flows cross-platform.

## Selector Strategy

Prefer, in order:

1. Stable user-visible text.
2. Accessibility labels and identifiers exposed through the iOS Accessibility layer.
3. Stable accessibility traits or state when Maestro can query them.
4. Relative selectors such as `childOf`, `below`, `above`, or `containsChild`.

Coordinates, screenshots, OCR, and index-only selectors are last resorts and must be recorded as brittle.

## Framework-Specific Notes

- SwiftUI: add stable accessibility labels/identifiers for controls, dynamic content, and stateful assertions.
- UIKit: prefer accessibility identifiers for controls whose visible copy is unstable.
- React Native iOS: prefer accessibility labels and identifiers; confirm the rendered accessibility tree before relying on framework-level test IDs.
- Flutter iOS: expose semantics for actionable controls and important assertions.

## iOS System Dialogs

iOS permission dialogs are outside app selectors. Prefer Maestro permission configuration on `launchApp` when the scenario expects known permissions:

```yaml
- launchApp:
    appId: ${APP_ID}
    permissions:
      location: allow
      notifications: allow
```

If a system prompt blocks the target behavior unexpectedly, classify it as setup/permission failure, not RED.

## Failure Classification

Before deciding a flow is RED, classify:

- environment: Maestro, Java, Xcode command line tools, simulator, or build tooling unavailable,
- build/install: simulator `.app` missing, install failed, wrong scheme/configuration,
- launch: wrong bundle ID or app cannot start,
- fixture/setup: seed, fake data, deep link, backend/mock, permission, or reset failed,
- YAML: syntax or unsupported command,
- selector: target not found because selector is wrong, hidden from accessibility, or brittle,
- runtime crash: app crashes before the behavior under test,
- simulator state: simulator boot, permissions, keychain, pasteboard, locale, or retained app state blocks the scenario,
- masked action: `optional: true` hid a required interaction,
- missing behavior: flow reached the target state and failed because the requested user-visible behavior does not exist.

Only missing behavior can count as a correct RED.

## Diagnosis Source Order

1. Code context for the behavior under test: screen/view, state holder, fixtures, deep links, command wiring, and async side effects.
2. UI source that affects Maestro observability: SwiftUI accessibility modifiers, UIKit accessibility labels/identifiers, React Native accessibility props, or Flutter semantics.
3. Simulator/app logs around the failure window, using project logging, Xcode logs, `xcrun simctl spawn <device> log stream`, or a project-approved log capture path.
4. Runtime hierarchy inspection for selector visibility and accessibility state.
5. Screenshots only as supplemental evidence when the previous sources do not explain the problem, or when the issue is specifically visual.

Screenshots are not a replacement for reading implementation context or simulator logs.
