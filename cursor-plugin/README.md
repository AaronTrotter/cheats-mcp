# Cheats Cursor plugin

Registers the hosted `https://cheats.cloud/mcp` connector as a Cursor plugin.
No local Node process and no API key file — Cursor talks to the Streamable HTTP endpoint and
completes Cheats' OAuth sign-in / consent flow. Once connected, the agent can search and
manage your cheats, Tasks, Brain, and other Cheats sections according to the scopes you
approve. Full setup notes live on
[Connect an MCP Client](https://cheats.cloud/api-docs/connect-mcp).

## Install

1. Import this repo as a marketplace (Customize → Plugins), or copy this folder to
   `~/.cursor/plugins/local/cheats` and reload the window.
2. Install the **cheats** plugin.
3. When Cursor connects, complete the Cheats sign-in / consent screen.

## Manual config (no plugin)

Put this in `~/.cursor/mcp.json` or a project `.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "cheats": {
      "url": "https://cheats.cloud/mcp"
    }
  }
}
```

Leave headers blank for OAuth. For a pasted API key instead:

```json
{
  "mcpServers": {
    "cheats": {
      "url": "https://cheats.cloud/mcp",
      "headers": {
        "Authorization": "Bearer csk_live_..."
      }
    }
  }
}
```

Create that key at [User](https://cheats.cloud/user) → API Access. Prefer a separate,
narrowly-scoped key for Cursor.

## Updating

Bump `version` in [`.cursor-plugin/plugin.json`](.cursor-plugin/plugin.json) and in this repo's
[`.cursor-plugin/marketplace.json`](../.cursor-plugin/marketplace.json) when you change anything
here.
