# Web Platform Adapter

Use this adapter when the Maestro target is a web application running in Maestro's Chromium-based browser support.

## Supported Target

The target is supported when Maestro can open the web `url`, interact with the rendered page, inspect enough user-visible state for stable selectors, and run repeatable flows from a known browser state. Treat Maestro Web support as Beta and record that risk when planning release-critical coverage.

## Preflight

Run or report:

```bash
maestro --version
java -version
curl -I <base-url>
maestro test .maestro/<feature>/<flow>.yaml
```

When the app needs a local dev server, also run or report the project-specific start/build commands, for example:

```bash
npm install
npm run build
npm run dev
```

Discover and record:

- Base URL used as the Maestro flow `url`.
- Local or deployed environment and how it is started.
- Authentication/session strategy.
- Browser state reset strategy for cookies, local storage, and origin data.
- Test data or backend fixture strategy.
- Existing `.maestro/` flow conventions.

## Minimal Flow Shape

```yaml
url: ${BASE_URL}
---
- launchApp:
    clearState: true
- extendedWaitUntil:
    visible: "<ready marker>"
    timeout: 10000
- tapOn: "<stable action target>"
- assertVisible: "<expected visible result>"
```

Use `maestro test -e BASE_URL=<https-or-local-url> .maestro/<feature>/<flow>.yaml` when keeping flows portable between environments.

## Selector Strategy

Prefer, in order:

1. Stable user-visible text.
2. Accessible names/labels tied to real controls.
3. Stable ARIA attributes or semantic HTML that Maestro can resolve.
4. Relative selectors such as `childOf`, `below`, `above`, or `containsChild`.

Coordinates, screenshots, OCR, and index-only selectors are last resorts and must be recorded as brittle. Do not rely on DOM-only selectors unless Maestro can actually resolve the rendered element.

## Framework-Specific Notes

- React/Vue/Angular/plain HTML: keep labels and accessible names stable for important controls.
- Flutter Web: expose Semantics for actionable controls and assertions because rendering may hide useful text from normal selectors.
- Server-rendered apps: ensure loading, auth redirects, and hydration states have stable ready markers.

## Web State and Auth

Browser state can persist between flows in the same run. Record whether the scenario uses `clearState`, a fresh test account, seeded backend data, URL parameters, or API-backed reset steps. Setup failure is not RED.

Authentication must be deterministic. If login depends on OTP, external identity providers, CAPTCHA, or manual user action, record the blocker, fixture, or accepted risk before planning TDD evidence.

## Failure Classification

Before deciding a flow is RED, classify:

- environment: Maestro, Java, Chromium download/runtime, network, or local dev server unavailable,
- build/server: web app cannot build, start, or serve the target route,
- launch: wrong URL, redirect loop, TLS/certificate failure, or page cannot load,
- fixture/setup: auth, seed data, backend/mock, cookies/local storage, or reset failed,
- YAML: syntax or unsupported command,
- selector: target not found because selector is wrong, hidden from accessibility, or brittle,
- runtime crash: page crashes or shows an app-level error before the behavior under test,
- browser limitation: Chromium-only Beta behavior, viewport, locale, or unsupported browser configuration blocks the scenario,
- masked action: `optional: true` hid a required interaction,
- missing behavior: flow reached the target state and failed because the requested user-visible behavior does not exist.

Only missing behavior can count as a correct RED.

## Diagnosis Source Order

1. Code context for the behavior under test: route/page, component, state holder, data loader/action, fixture, and async side effects.
2. Rendered UI and accessibility source that affects Maestro observability: labels, ARIA attributes, semantic HTML, framework semantics, and ready markers.
3. Browser/app/server logs around the failure window, including dev server output, console logs when available, network/auth/setup logs, and backend fixture logs.
4. Runtime hierarchy inspection for selector visibility and accessibility state.
5. Screenshots only as supplemental evidence when the previous sources do not explain the problem, or when the issue is specifically visual.

Screenshots are not a replacement for reading implementation context or runtime logs.
