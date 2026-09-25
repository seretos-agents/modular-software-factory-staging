# modular-software-factory-staging

## Quick install

**Claude Code:**

```
/plugin marketplace add seretos-agents/modular-software-factory-staging
/plugin install <plugin-name>@modular-software-factory-staging
```

The agent fetches the plugin from its own repo at the version pinned here.

## What's inside

The full list of plugins is in [`.claude-plugin/marketplace.json`](.claude-plugin/marketplace.json) (Claude Code) and [`.agents/plugins/marketplace.json`](.agents/plugins/marketplace.json) (Codex). Each entry points at its own repository, where the plugin's own README explains what it does and how to use it.

## How plugins get added

Plugin repos publish via GitHub `repository_dispatch` → CI commits straight to `main` here (see [AGENTS.md](AGENTS.md)) — no PR, no review. If you'd rather review before it's live, use [modular-software-factory](https://github.com/seretos-agents/modular-software-factory) instead.

## Status

This registry starts empty — it replaces the old `Seretos/agent-marketplace`, but its existing entries have not been migrated over yet (deliberately, as a separate follow-up). App downloads and a GitHub Pages catalog site, both part of the old marketplace, are also out of scope for now.
