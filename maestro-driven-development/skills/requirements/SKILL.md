---
name: requirements
description: Use when clarifying Maestro-supported Android, iOS, or Web feature requirements, UI changes, or bug fixes before implementation planning.
---

# Maestro Requirements Grill

## Overview

This skill turns fuzzy feature requests into precise, testable requirements. It asks one decision question at a time, recommends an answer, challenges vague terminology, checks existing docs/code before asking, and updates glossary documentation as decisions crystallize.

The Maestro-specific goal is to ensure each requirement can be verified through Maestro on a selected platform adapter or is explicitly marked as not covered by the default Maestro-only workflow.

## When to Use

Use before planning or coding any feature, UI change, or bug fix that is expected to be verified through Maestro.

Do not proceed to implementation planning until every requirement satisfies the done criteria below.

## Required Setup

1. Use one-question-at-a-time interview discipline. Do not batch unrelated decisions into one prompt.
2. Check for existing docs:
   - `CONTEXT.md`
   - `CONTEXT-MAP.md`
   - `docs/adr/`
   - existing feature specs or issue docs
3. If a question can be answered from the codebase or docs, inspect those instead of asking the user.
4. Challenge terms against the project's glossary or context docs when they exist. If the user's wording conflicts with a canonical term, stop and resolve the term before planning.
5. Record resolved terms in the feature requirements first. Update `CONTEXT.md` or another project glossary only when the project already uses that glossary pattern or the user asks for it.
6. If no glossary exists, do not create one just for exploration. Create `CONTEXT.md` only when the user approves or the project convention clearly requires it.

## Output Files

For each feature, create or update:

```text
docs/requirements/<feature>/requirements.md
docs/requirements/<feature>/edge-cases.md
```

Use this skill's [`templates/requirements.md`](templates/requirements.md) and [`templates/edge-cases.md`](templates/edge-cases.md) as the default shapes.

## Done Criteria for Each Requirement

Each requirement must include:

- Actor/user role.
- Target platform: Android, iOS, Web, or a documented multi-platform split.
- Trigger or entry point.
- Preconditions and data state.
- Primary success path.
- User-visible acceptance criteria.
- User-visible failure and recovery behavior when applicable.
- Context-relevant boundary conditions that could affect behavior, observability, risk, or verification.
- A Maestro-observable verification strategy.
- Stable selector strategy or a note that implementation must add identifiers, labels, semantics, or accessible names.
- Platform adapter constraints and any framework-specific notes.

If any item is missing, ask another question. If the user declines to handle it, record it as a non-goal, deferred item, or accepted risk only when the missing item is not one of the non-riskable required fields below.

### Non-riskable Required Fields

The current executable scope cannot proceed to implementation planning when any field below is missing:

- actor/user role,
- target platform or documented multi-platform split,
- trigger or entry point,
- user-visible acceptance criteria,
- Maestro-observable verification strategy,
- stable selector strategy or explicit implementation requirement to add labels, identifiers, semantics, or accessible names.

If the user declines one of these fields, remove that behavior from the executable scope or mark the whole feature as blocked. Do not convert these fields into an accepted risk for a runnable Maestro plan.

## CHECKPOINT - Requirements Gate

Do not proceed to implementation planning until this gate is satisfied:

1. Every requirement has all done-criteria fields above, or the missing field is explicitly recorded as a non-goal, deferred item, or accepted risk.
2. Every requirement is Maestro-observable on the selected platform adapter, or the requirements file records the non-observable coverage risk.
3. Every relevant boundary has expected behavior, Maestro coverage strategy, platform adapter impact, and status.
4. Stable selector needs are known, or the requirements file states that implementation must add identifiers, labels, semantics, or accessible names.
5. No unresolved term conflict remains between the user's wording and project glossary/context docs.
6. No non-riskable required field is missing from the executable scope.

If any item fails, stop in requirements grilling. Ask the next single decision question or record the blocker/risk before planning.

## Failure Routes

| Trigger | First action | Required record before continuing |
|---|---|---|
| Existing docs or code answer the question | Read the source instead of asking the user | Cite the source in the requirements notes |
| User term conflicts with project glossary | Stop and resolve the canonical term | Resolved term and rejected wording |
| Target platform is unclear | Ask which Maestro-supported adapter applies | Android, iOS, Web, multi-platform split, or blocker |
| A non-riskable required field is missing | Ask one focused decision question; if declined, remove from executable scope or block the feature | Answer, non-goal outside executable scope, or blocker |
| A riskable done-criteria field is missing | Ask one focused decision question | Answer, non-goal, deferred item, or accepted risk |
| User declines to decide a behavior | Do not infer product behavior | Explicit non-goal, deferred item, or accepted risk |
| Requirement is not Maestro-observable | Convert it to visible acceptance or mark risk | Visible criterion or Maestro-only coverage risk |
| Boundary is generic but irrelevant | Drop it instead of forcing coverage | No record needed unless the user asked about it |
| Relevant boundary lacks expected behavior | Ask one boundary-specific question | Expected behavior plus Maestro coverage status |
| Stable selector is unavailable | Require labels/resource IDs/semantics/accessibility attributes in implementation | Selector requirement in requirements or edge cases |
| User asks to start planning/coding early | Stop and run this gate first | Gate status and remaining open decisions |

## Platform Boundary Analysis

Analyze boundary conditions based on the actual requirement, selected platform adapter, project docs, current code, platform behavior, and existing automation. Use this skill's [`references/platform-boundary-analysis.md`](references/platform-boundary-analysis.md) as a thinking guide for deriving relevant risks and scenarios.

Only record boundaries that could affect user-visible behavior, Maestro observability, implementation risk, or verification confidence. If a common platform scenario is irrelevant, do not force it into the artifact.

For each relevant boundary, record the expected behavior, Maestro coverage strategy, selected adapter impact, and status: tested by Maestro, deferred, accepted risk, non-goal, or not applicable.

## Question Style

Ask one question at a time. Each question should include:

1. The precise decision being made.
2. Why it matters for Maestro or the selected platform adapter.
3. Your recommended answer.
4. 2-4 choices when useful.

Example:

```text
Question: What should happen when the user taps Save twice before the request completes?
Recommended answer: Disable the Save button and show a loading state until the request resolves. This gives Maestro a stable assertion and prevents duplicate submissions.
```

Do not ask questions whose answers are already available in current docs, code, logs, or artifacts. Read first, then ask only the remaining decision.

## Maestro Observability Rules

A requirement is Maestro-observable if it can be verified through:

- Visible text.
- Navigation/screen changes.
- Accessibility labels, identifiers, resource IDs, ARIA labels, or framework semantics.
- Deep links/test fixtures/fake data.
- Device, simulator, or browser state controlled by Maestro.
- A user-visible error/loading/empty state.

If a requirement is purely internal, convert it into a visible acceptance criterion or record it as a Maestro-only coverage risk.

## Documentation Rules

- Record canonical terms in the feature requirements; update `CONTEXT.md` only when project convention or user instruction supports it.
- Create ADRs sparingly. Only create one when all three are true: the decision is hard to reverse, surprising without context, and the result of a real trade-off. If any condition is missing, keep the decision in the feature requirements or plan instead.
- Keep glossary files as glossary only; put requirements and implementation decisions in feature docs.
- Discuss concrete platform scenarios when terms are fuzzy, choosing scenarios from the feature's actual behavior and selected adapter.
- Record declined boundaries as non-goals, deferred items, or accepted risks rather than silently dropping them.

## Do Not Do

- Do not batch unrelated product decisions into one question.
- Do not ask the user questions already answered by docs, code, logs, or artifacts.
- Do not create a glossary, ADR, fixture, or implementation plan just to make exploration look complete.
- Do not force generic platform edge cases into the artifact when they do not affect behavior, observability, risk, or verification confidence.
- Do not treat internal-only behavior as covered by Maestro unless it has a user-visible acceptance criterion.
- Do not move to implementation planning with unresolved term conflicts, missing selectors, unknown target platform, or unstated coverage risks.

## Verification Checklist

- [ ] One-question-at-a-time interview completed.
- [ ] Requirements file exists and contains acceptance criteria.
- [ ] Target platform and adapter are identified.
- [ ] Boundary analysis exists and covers relevant feature-specific scenarios.
- [ ] Every requirement is Maestro-observable or explicitly marked as a risk/non-goal.
- [ ] Stable selector needs are identified.
- [ ] Resolved glossary terms were captured in the feature requirements or approved project glossary without implementation details.
- [ ] No blocking open questions remain; unresolved items are explicitly non-goals, deferred items, or accepted risks.
