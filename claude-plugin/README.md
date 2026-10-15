# cheatsheet Claude Code plugin

Registers the hosted `https://cheats.cloud/mcp` connector as a Claude Code plugin.
No local Node process and no API key file — Claude Code talks to the Streamable HTTP endpoint
and completes Cheats' OAuth sign-in / consent flow. Once connected, Claude can search and
manage your cheats, Tasks, Brain, and other Cheats sections according to the scopes you
approve on the consent screen. Full setup notes live on
[Connect an MCP Client](https://cheats.cloud/api-docs/connect-mcp).

## Install

```
/plugin marketplace add AaronTrotter/cheatsheet-mcp
/plugin install cheatsheet@cheatsheet-mcp
```

Then run `/mcp` (or use a cheatsheet tool) and finish the browser sign-in when prompted.

## Manual config (no plugin)

```bash
claude mcp add --transport http cheatsheet https://cheats.cloud/mcp
```

Or in `.mcp.json`:

```json
{
  "mcpServers": {
    "cheatsheet": {
      "type": "http",
      "url": "https://cheats.cloud/mcp"
    }
  }
}
```

Claude Code requires `"type": "http"` on URL entries. Leave headers blank so OAuth can run.

## Cursor

Use [`cursor-plugin/`](../cursor-plugin/README.md) instead of this one. Same hosted URL; Cursor's
plugin format and marketplace manifest live there.

## Updating

Bump `version` in [`.claude-plugin/plugin.json`](.claude-plugin/plugin.json) and in this repo's
[`.claude-plugin/marketplace.json`](../.claude-plugin/marketplace.json) when you change anything
here.
