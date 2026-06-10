# Platform Boundary Analysis Reference

Use this during requirements grilling and planning to think through boundary conditions that matter for the specific feature, project, and selected Maestro platform adapter.

## Analysis Approach

Start from the user's requested behavior, then inspect current docs, code, UI state, data sources, selected platform adapter, platform APIs, and existing automation. Derive only the boundary conditions that could change user-visible behavior, Maestro observability, implementation risk, or verification confidence.

For each relevant boundary, record:

- scenario,
- why it matters,
- expected user-visible behavior,
- selected platform adapter impact,
- Maestro coverage strategy,
- status: tested by Maestro, deferred, accepted risk, non-goal, or not applicable.

## Common Sources to Consider

Consider these only when they are relevant to the feature:

- permissions and system state,
- network availability, latency, backend errors, and recovery,
- empty, stale, duplicate, invalid, paginated, or large data,
- loading, error, retry, disabled controls, and repeated actions,
- navigation, background/foreground, process or page reload, and resume behavior,
- screen size, orientation, viewport, dark mode, font scaling, locale, timezone, and formatting,
- keyboard, focus, invalid input, paste, and platform text input behavior,
- accessibility labels, content descriptions, resource IDs, identifiers, ARIA labels, and framework semantics,
- first install, upgrade, logged-out/logged-in state, retained browser state, keychain/cookies/local storage, and local data migration,
- debug fixtures, fake data, deep links, universal links, route parameters, and test-only setup paths,
- platform-specific toolchain, simulator/device/browser, app server, install, launch, and reset constraints.

The list is a prompt for analysis, not a required structure. Add project-specific boundaries when the code or requirements suggest them, and ignore irrelevant items.
