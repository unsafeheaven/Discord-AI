---
name: Discord bot as standalone Python app
description: How Discord bots fit into this pnpm-workspace-based Replit template.
---

Discord bots aren't a listed `createArtifact` type — they have no web preview, so they don't go through the artifacts skill at all.

**How to build one:**
- Put the bot in a root-level `bot/` directory (Python + discord.py), independent of the pnpm workspace and its `artifacts/`/`lib/` structure.
- Install the Python runtime and packages via the package-management skill (`installProgrammingLanguage({ language: "python-3.11" })`, `installLanguagePackages`).
- Run it via a plain `configureWorkflow({ command: "cd bot && python main.py", outputType: "console" })` — not an artifact-managed workflow, since there's no `artifact.toml` for it.
- Needs a `DISCORD_TOKEN` secret from the user (create at discord.com/developers/applications, enable Message Content Intent) — no Replit integration provides Discord bot tokens (the Discord connector found via `searchIntegrations` is for user-account OAuth, not bot tokens).

**Why:** the artifacts system and its skills assume a web-servable preview; bots are a background service with no path-routed preview, so they sit outside that lifecycle entirely.

**OpenRouter `:free` model gotchas:** free-tier models get discontinued/renamed without notice (e.g. a 404 telling you to use the paid slug instead) and can return HTTP 200 with an empty/`None` message content (e.g. under load or content filtering). Always guard `response.choices[0].message.content` for `None`/empty before calling `.strip()`, and don't assume a `:free` slug that worked once will keep existing — recheck `https://openrouter.ai/api/v1/models` if a model starts erroring.
