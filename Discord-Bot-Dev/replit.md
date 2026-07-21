# AllTheWayGaming Management Bot

A full-featured Discord moderation and utility bot built with Discord.js v14, running alongside an Express API server.

## Project structure

- `artifacts/api-server/` — Express HTTP server + Discord bot (started together)
  - `src/bot/` — All bot code
    - `commands/` — Slash commands (moderation, cases, management, utility, tickets, extras)
    - `events/` — Discord gateway event handlers
    - `database/db.ts` — PostgreSQL-backed database layer (all tables, helpers)
    - `utils/` — Shared utilities (embeds, permissions, time, automod, modAction, channelLogger)
    - `handlers/` — Command + event loaders
    - `deploy-commands.ts` — Registers slash commands with Discord
    - `index.ts` — `startBot()` entry point
  - `src/index.ts` — Starts Express server then calls `startBot()`

## Environment variables / secrets required

| Secret | Description |
|--------|-------------|
| `DISCORD_TOKEN` | Bot token from Discord Developer Portal |
| `DISCORD_CLIENT_ID` | Application ID from Discord Developer Portal |
| `DISCORD_GUILD_ID` | Guild ID to register slash commands to (dev mode) |
| `SESSION_SECRET` | Express session secret |
| `DATABASE_URL` | PostgreSQL connection string (auto-set by Replit) |

## Discord Developer Portal setup

Before the bot can connect you **must** enable Privileged Gateway Intents:

1. Visit https://discord.com/developers/applications
2. Select your application → **Bot**
3. Under **Privileged Gateway Intents**, enable:
   - ✅ Server Members Intent
   - ✅ Message Content Intent
4. Click **Save Changes**

## Running the bot

The workflow `artifacts/api-server: API Server` runs `pnpm --filter @workspace/api-server run dev`, which:
1. Builds with esbuild (`pnpm run build`)
2. Starts the server (`pnpm run start`)
3. The server starts Express then calls `startBot()` to log the Discord bot in

Slash commands are registered to `DISCORD_GUILD_ID` on every startup (guild-scoped = instant updates).

## Bot features

### Moderation
`/ban`, `/kick`, `/warn`, `/warnings`, `/clearwarnings`, `/timeout`, `/mute`, `/purge`, `/slowmode`, `/lockdown`, `/nickname`, `/role`

### Cases
`/case`, `/cases`, `/editcase`, `/deletecase`

### Management
`/setup` — configure all log channels, roles, and features  
`/modpanel` — interactive button-based mod panel  
`/dm` — DM a member with an embed (anonymous option)

### Utility
`/userinfo`, `/serverinfo`, `/avatar`, `/ping`, `/uptime`, `/botstats`, `/roleinfo`, `/help`

### Tickets
`/ticket` — create, close, claim, rename, add/remove users

### Extras
`/giveaway`, `/poll`, `/suggest`, `/afk`, `/announce`, `/reminder`, `/welcome`, `/reactionroles`, `/starboard`

### AutoMod
Automatic spam, bad-word, invite-link, external-link, mention-spam, and caps detection with configurable settings via `/setup`.

## User preferences

- Slash commands only (no prefix commands)
- PostgreSQL for all persistence (no SQLite / native modules)
- Static imports for all commands and events (esbuild-compatible)
