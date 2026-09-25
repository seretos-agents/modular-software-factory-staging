# modular-software-factory-staging

Metadata only — no plugin source, no binaries, no build pipeline live here. Claude Code (and other compatible agent CLIs) read this repo when a user runs `/plugin marketplace add seretos-agents/modular-software-factory-staging`.

## Layout

```
.claude-plugin/marketplace.json   # the plugin registry, single source of truth
.agents/plugins/marketplace.json  # Codex's mirror of the same plugins
.github/workflows/
  update-registry.yml             # thin receiver: repository_dispatch -> shared action, mode: direct
README.md                         # user-facing
AGENTS.md                         # this file
```

The upsert / commit logic itself is **not** implemented here — it's shared with the curated marketplace and lives once in [`modular-software-factory-dev`](https://github.com/seretos-agents/modular-software-factory-dev)'s `.github/actions/update-registry`. This repo's `update-registry.yml` just forwards the `repository_dispatch` payload to that action with `mode: direct`.

## Two marketplaces, one shared action

- **This repo (staging):** `mode: direct` — a registered plugin is committed straight to `main`, no review gate.
- **[modular-software-factory](https://github.com/seretos-agents/modular-software-factory) (main):** `mode: pr` — a registered plugin opens a review PR instead.

Both repos' `update-registry.yml` are near-identical thin wrappers; only the `mode` (and this repo's narrower `contents: write`-only job permission, since there's no PR to open) differs. See the shared action's `action.yml` for the full upsert/commit contract (schema, `icon`/`description_url`/`tags` semantics, idempotency). `changelog` is accepted by the payload contract but not forwarded by this repo's workflow — there is no PR body to render it into in `mode: direct`.

## marketplace.json schema

Same schema as the curated marketplace — see [modular-software-factory/AGENTS.md](https://github.com/seretos-agents/modular-software-factory/blob/main/AGENTS.md#marketplacejson-schema).

## How entries get added

```
plugin repo, release.yml after a successful build:
  POST /repos/seretos-agents/modular-software-factory-staging/dispatches
  event_type: plugin-release
  client_payload: { name, repo, version, category, description, icon?, description_url?, tags? }

this repo, update-registry.yml triggered by repository_dispatch:
  forwards the payload to seretos-agents/modular-software-factory-dev's
  update-registry action with mode: direct
    1. patches .claude-plugin/marketplace.json and .agents/plugins/marketplace.json
    2. commits straight to main and pushes -- no branch, no PR

entry is live immediately
```

## GitHub repo settings that matter

- `secrets.ECOSYSTEM_TOKEN` must exist and carry `repo` scope. It authenticates the `git push` in `mode: direct` (via `actions/checkout`'s `token:` input) -- the same secret name used across the whole ecosystem, so it only has to be set once at organisation level.

## Deliberately out of scope (for now)

Same list as [modular-software-factory](https://github.com/seretos-agents/modular-software-factory/blob/main/AGENTS.md#deliberately-out-of-scope-for-now): migrating the old `Seretos/agent-marketplace` entries, the app registry, the GitHub Pages catalog site, the Agent Plugins 1.0 standard, and updating the `agent-plugins` sender templates.
