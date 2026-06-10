# Maestro Usage Preflight

Run this before claiming RED/GREEN evidence. If a required tool or project value is missing, report the missing prerequisite and the install or repair step. When the runtime supports MCP, confirm the Maestro MCP server is configured as command `maestro` with args `["mcp"]` and that the current session exposes working Maestro MCP tools. Do not treat the long-running MCP server command as a one-shot preflight command.

## Required Shared Checks

```bash
maestro --version
java -version
```

Then run the selected platform adapter checks:

- Android: [`platform-adapters/android.md`](platform-adapters/android.md)
- iOS: [`platform-adapters/ios.md`](platform-adapters/ios.md)
- Web: [`platform-adapters/web.md`](platform-adapters/web.md)

Discover or confirm:

- Selected platform adapter.
- App identity: Android applicationId, iOS bundle ID, or Web URL.
- Device/emulator, simulator, browser, or app server availability.
- Maestro MCP configuration when available.
- Maestro MCP service/tool availability in the current session when the runtime supports MCP; use a lightweight MCP call such as `list_devices`.
- Existing `.maestro/` flows and conventions.
- Scenario fixture/reset path.

Record where each project-specific value came from, such as manifests, build settings, route config, server config, build output, existing flows, docs, or code.

## Typical Install / Repair Hints

```bash
curl -Ls "https://get.maestro.mobile.dev" | bash
brew install openjdk@17
```

Do not install tools, start emulators/simulators/servers, or mutate system setup unless the user approves.

## Blockers

These block Maestro-TDD until fixed:

- Maestro is not installed.
- Java is not installed or unavailable to Maestro.
- Selected platform adapter is unknown.
- Required platform tooling is missing.
- Required device, simulator, browser, or app server is unavailable.
- Project cannot build, install, serve, or launch the target.
- App identity (`appId` or `url`) is unknown.
- Test data cannot be initialized for the required scenario.
- Runtime supports MCP but the Maestro MCP service is not loaded, enabled, or callable for the current session when MCP evidence is required.

A blocker is not a RED. RED means the flow runs and fails because the requested behavior is absent.
