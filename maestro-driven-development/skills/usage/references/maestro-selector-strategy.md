# Maestro Selector Strategy

Reliable Maestro flows need stable selectors that the selected platform adapter can actually resolve.

## Preference Order

1. User-visible text that is stable and meaningful.
2. Accessibility labels, identifiers, content descriptions, ARIA labels, or semantic names.
3. Platform IDs or resource IDs when the adapter supports them.
4. Framework semantics/test tags exposed so Maestro can query them.
5. Relative selectors such as `below`, `above`, `childOf`, or `containsChild`.
6. Coordinates, screenshots, and OCR only as documented last resort.

## Rules

- Do not rely on layout coordinates for normal flows.
- Do not assert on incidental implementation text if the product copy is unstable.
- Prefer semantic labels that also improve accessibility.
- Prefer existing user-visible and accessible surface before touching business or production code.
- After a flow action changes the screen, use Maestro hierarchy inspection to confirm the current element tree. CLI `maestro hierarchy` can expose element attributes and bounds when the platform exposes them; MCP `inspect_view_hierarchy` is the preferred interactive equivalent when available.
- Add IDs, labels, accessibility identifiers, ARIA labels, semantics, debug entry points, or fixtures only when existing hierarchy and UI source cannot make the behavior Maestro-observable. Any production-code observability change must be harmless, minimal, and must not expose secrets or degrade UX.
- Record any brittle selector in the plan and final report.
- Use state properties such as `enabled`, `checked`, `selected`, and `focused` when the behavior depends on interactive state and the adapter exposes that state.
- Use relative selectors such as `below`, `above`, `childOf`, or `containsChild` when repeated elements need context.
- Use `optional: true` only for optional cleanup or system dialogs; never use it around the behavior being tested.
