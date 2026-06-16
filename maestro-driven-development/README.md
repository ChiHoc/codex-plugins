# Maestro Driven Development Plugin

A Codex plugin source for Maestro-driven development skills. The toolkit helps agents clarify requirements, plan implementation, and implement with strict Maestro-only TDD for Maestro-supported Android, iOS, and Web targets across CC, Codex, Hermes, OpenClaw, and similar agent runtimes.

Author: Chiho.

## Scope

- Targets: Android, iOS, and Web surfaces that Maestro can launch, inspect, interact with, and run repeatable flows against.
- Frameworks: native Android, native iOS, React Native, Flutter, and browser frameworks when the selected platform adapter can provide enough preflight and diagnosis evidence.
- Platform adapters: Android, iOS, and Web adapters live under [`skills/usage/references/platform-adapters/`](skills/usage/references/platform-adapters/).
- During Maestro TDD, platform-native unit tests are prohibited as RED/GREEN/regression evidence; every behavior cycle must use Maestro flow evidence.
- Web support follows Maestro's current Chromium-based Web support and must be treated as Beta when planning risk.

## Plugin Structure

```text
.codex-plugin/
└── plugin.json

.mcp.json                              # Maestro MCP server config

skills/
├── workflow/                            # Entry orchestration skill
├── requirements/                        # Requirements + boundary analysis
│   ├── templates/
│   └── references/
├── planning/                            # Self-contained implementation planning with Maestro test plan
│   └── templates/
├── usage/                               # Maestro MCP/CLI preflight, platform adapters, YAML, selectors, debugging
│   ├── templates/
│   └── references/
│       └── platform-adapters/
├── execution/                           # Execute approved plans task-by-task
└── tdd/                                 # RED-GREEN-REFACTOR using Maestro flows

docs/
└── adr/
```

## Workflow

All runtimes can use this bundle by opening the relevant `SKILL.md` file and following its instructions. If a runtime supports named skill invocation, the same files may also be installed under that runtime's skill directory.

Support files are colocated inside the skill that owns them. Preserve the whole plugin root structure when distributing this toolkit; copying only a single `SKILL.md` may omit templates or references that the skill expects.

1. Start with [`workflow/SKILL.md`](skills/workflow/SKILL.md).
2. Clarify requirements with [`requirements/SKILL.md`](skills/requirements/SKILL.md) until every requirement is Maestro-observable or explicitly marked as a risk/non-goal.
3. Plan implementation with [`planning/SKILL.md`](skills/planning/SKILL.md) to produce a dedicated Maestro Test Plan, bite-sized implementation tasks, exact commands, and Maestro test flow drafts.
4. Select the target platform adapter from [`usage/references/platform-adapters/`](skills/usage/references/platform-adapters/) and follow [`usage/SKILL.md`](skills/usage/SKILL.md) for Maestro MCP/CLI environment preflight, local MCP service/tool availability checks, YAML authoring, selector strategy, debugging, and evidence collection.
5. Execute with [`execution/SKILL.md`](skills/execution/SKILL.md), following the approved requirements and Maestro Test Plan task-by-task.
6. During execution, apply [`tdd/SKILL.md`](skills/tdd/SKILL.md) inside each behavior cycle to write each Maestro flow first, watch it fail for the correct reason, implement minimally, then make it pass.
7. Diagnose failed or ambiguous Maestro TDD steps with platform diagnostics before using screenshots; screenshots are supplemental evidence only.

## Installation and Usage

This workspace is the source of truth for now. For Codex plugin installation, use this repository as the plugin root; `.codex-plugin/plugin.json` points Codex at `./skills/` and `.mcp.json` configures the Maestro MCP server as `maestro mcp`.

Plugin id: `maestro-driven-development`. When publishing or installing from a copied local plugin directory, use the directory name `maestro-driven-development` so it matches the manifest name.

For runtimes that do not support plugin installation, skill discovery, or named invocation, use the document links in the workflow directly. The entry document is [`skills/workflow/SKILL.md`](skills/workflow/SKILL.md).

| Need | Document |
| --- | --- |
| Full Maestro workflow | [`skills/workflow/SKILL.md`](skills/workflow/SKILL.md) |
| Requirements and boundary analysis | [`skills/requirements/SKILL.md`](skills/requirements/SKILL.md) |
| Implementation plan writing | [`skills/planning/SKILL.md`](skills/planning/SKILL.md) |
| Maestro MCP/CLI installation, platform adapters, YAML, selectors, debugging | [`skills/usage/SKILL.md`](skills/usage/SKILL.md) |
| Android adapter | [`skills/usage/references/platform-adapters/android.md`](skills/usage/references/platform-adapters/android.md) |
| iOS adapter | [`skills/usage/references/platform-adapters/ios.md`](skills/usage/references/platform-adapters/ios.md) |
| Web adapter | [`skills/usage/references/platform-adapters/web.md`](skills/usage/references/platform-adapters/web.md) |
| Task-by-task plan execution | [`skills/execution/SKILL.md`](skills/execution/SKILL.md) |
| Maestro RED-GREEN-REFACTOR discipline | [`skills/tdd/SKILL.md`](skills/tdd/SKILL.md) |
