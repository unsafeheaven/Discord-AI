# Discord AI Persona Bot

A Discord bot with a chill/rude Gen Z persona that chats with server members, powered by an LLM via OpenRouter.

## Run & Operate

- `Discord Bot` workflow — runs `cd bot && python main.py`
- `pnpm --filter @workspace/api-server run dev` — run the API server (port 5000, unused by the bot; reserved for future web features)
- `pnpm run typecheck` — full typecheck across all packages
- `pnpm run build` — typecheck + build all packages
- Required secrets: `DISCORD_TOKEN` (Discord bot token), `OPENROUTER_API_KEY` (OpenRouter API key)

## Stack

- Bot: Python 3.11, discord.py, openai SDK (pointed at OpenRouter), python-dotenv
- Model: `openai/gpt-oss-20b:free` via OpenRouter
- pnpm workspaces, Node.js 24, TypeScript 5.9 (for the API server / mockup sandbox, currently unused by the bot)

## Where things live

```
bot/
  main.py           — entry point, bot init, cog loading
  cogs/
    chat.py         — on_message handler, mention + "servent" trigger, random replies
    commands.py     — slash commands (/ask, /roast, /vibe, /reset, /help)
  utils/
    ai.py           — OpenRouter client wrapper + persona system prompts
    history.py      — per-channel in-memory conversation history
  .env.example      — template for local dev
  README.md         — setup & invite instructions
```

`bot/` is a standalone Python app, not part of the pnpm workspace — it runs via its own `Discord Bot` workflow.

## Architecture decisions

- Two personas: friendlier for server admins, rude/dismissive for everyone else (`bot/utils/ai.py`).
- Conversation history is in-memory per channel, capped at 20 messages — resets on restart (privacy/simplicity over persistence).
- Triggers: @mention, the word "serv/servant/servent" anywhere in a message, slash commands, or a random 1-in-15 chance to chime in.
- Uses OpenRouter (not OpenAI directly) so a user-supplied `OPENROUTER_API_KEY` is enough — no Replit AI integration was available on this account tier.

## Product

- Users chat with the bot by mentioning it, saying "servent" + a message, or via `/ask`.
- `/roast`, `/vibe`, `/reset`, `/help` slash commands.
- Bot occasionally chimes in unprompted to feel like an active chat member.

## User preferences

_Populate as you build — explicit user instructions worth remembering across sessions._

## Gotchas

- Must enable **Message Content Intent** in the Discord Developer Portal or the bot can't read message text.
- Slash commands can take up to an hour to propagate globally on first sync (per-guild sync is instant).
- `_is_admin` only checks permissions for `discord.Member` — bot commands used in DMs treat the user as non-admin.

## Pointers

- Bot invite URL template: `https://discord.com/api/oauth2/authorize?client_id=CLIENT_ID&permissions=274878024704&scope=bot%20applications.commands`
- See `bot/README.md` for full setup steps
- See the `pnpm-workspace` skill for workspace structure, TypeScript setup, and package details (applies to `artifacts/`, not `bot/`)
