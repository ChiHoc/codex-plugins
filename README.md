# Codex Plugins

Source workspace for Codex plugins maintained by Chiho.

Repository: [ChiHoc/codex-plugins](https://github.com/ChiHoc/codex-plugins)

This repository is a marketplace-style container: the root keeps marketplace metadata and each plugin lives in its own directory with its own manifest, MCP configuration, skills, and README.

## Current Plugins

| Plugin | Purpose | Entry points |
| --- | --- | --- |
| [Maestro Driven Development](maestro-driven-development/README.md) | Platform-aware Maestro workflow skills for Android, iOS, and Web development with Maestro-first TDD. | [`skills/workflow/SKILL.md`](maestro-driven-development/skills/workflow/SKILL.md), [`CONTEXT.md`](maestro-driven-development/CONTEXT.md) |

## Installation

Register the public marketplace source:

```sh
codex plugin marketplace add https://github.com/ChiHoc/codex-plugins.git
```

Or clone the repository first if you want to inspect or modify it locally:

```sh
git clone https://github.com/ChiHoc/codex-plugins.git
cd codex-plugins
codex plugin marketplace add "$PWD"
```

Then enable or install the plugin from the `Built by Chiho` marketplace in the target Codex environment.

For runtimes that do not support Codex plugin installation, open the plugin README directly and follow the `SKILL.md` documents from there. The `Maestro Driven Development` entry skill is [maestro-driven-development/skills/workflow/SKILL.md](maestro-driven-development/skills/workflow/SKILL.md).

## Maintaining Plugins

When adding or changing a plugin:

1. Put the plugin in its own top-level directory.
2. Add or update `.codex-plugin/plugin.json` inside that plugin directory.
3. Keep skill-owned templates and references next to the owning `SKILL.md`.
4. Update `.agents/plugins/marketplace.json` so the marketplace points at the correct local source.
5. Update this README only as a repository index; keep plugin-specific workflow details in the plugin README.
