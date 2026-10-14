<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/cheatsheet-lockup-ondark.svg">
  <img alt="cheatsheet" src="assets/cheatsheet-lockup-onwhite.svg" width="340" height="56">
</picture>

# cheatsheet-mcp

Client packaging and install docs for the [cheatsheet](https://cheats.aarontrotter.com) hosted MCP
connector. Point any MCP-compatible client at:

```text
https://cheats.aarontrotter.com/mcp
```

That endpoint is Streamable HTTP with standard OAuth discovery
(`/.well-known/oauth-authorization-server`, `/.well-known/oauth-protected-resource`). Leave auth
headers blank and the client runs Cheatsheet's sign-in / consent flow; a scoped API key is minted
for you and listed under `/user` → API Access. Untick any app sections on the consent screen to
limit which tools the connector offers.

The server itself lives in the app repo
([`functions/routes/db/mcpConnector.js`](https://github.com/AaronTrotter/cheatsheet)). This repo only
packages how clients reach it. The same walkthrough from the web app's side is on
[Connect an MCP Client](https://cheats.aarontrotter.com/api-docs/connect-mcp).

There is no local MCP process to install. Older stdio proxies and the REST-backed local example
were removed — every major client below can use the hosted URL directly.

## Quick connect

| Client | How |
| --- | --- |
| **Claude** (claude.ai / Desktop / mobile) | Settings → Connectors → Add custom connector → URL above |
| **Claude Code** | [`claude-plugin/`](claude-plugin/README.md), or `claude mcp add --transport http cheatsheet https://cheats.aarontrotter.com/mcp` |
| **Cursor** | [`cursor-plugin/`](cursor-plugin/README.md), or `url` entry in `~/.cursor/mcp.json` |
| **ChatGPT** (web) | Settings → Apps & Connectors → enable Developer mode → Create connector → URL + OAuth |
| **VS Code + Copilot** | MCP: Add Server → HTTP → URL above |
| **Codex CLI** | Add an HTTP MCP server with the URL above (OAuth or bearer header) |
| **Gemini CLI** | `gemini mcp add --transport http cheatsheet https://cheats.aarontrotter.com/mcp` (or `npx mcp-remote` shim if your build still needs it) |
| **Windsurf / other IDE agents** | Same shape as Cursor: a config entry with `"url": "https://cheats.aarontrotter.com/mcp"` |

## Authentication

**Sign in with Cheatsheet (recommended).** Supported on Cursor, Claude connectors, Claude Code,
ChatGPT, Copilot, and other OAuth-aware MCP clients. No key to create beforehand.

**Paste an API key.** For clients that only accept a bearer token, create one at `/user` → API
Access and send `Authorization: Bearer <key>`. Use a separate, narrowly-scoped key per client so
you can revoke one without touching the others.

## Claude Code plugin

```
/plugin marketplace add AaronTrotter/cheatsheet-mcp
/plugin install cheatsheet@cheatsheet-mcp
```

Details: [`claude-plugin/README.md`](claude-plugin/README.md). Claude Code needs
`"type": "http"` on URL entries in `.mcp.json`.

## Cursor plugin

Import this repo as a Cursor marketplace, or copy [`cursor-plugin/`](cursor-plugin/) into
`~/.cursor/plugins/local/cheatsheet` and reload. Details:
[`cursor-plugin/README.md`](cursor-plugin/README.md).

Manual `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "cheatsheet": {
      "url": "https://cheats.aarontrotter.com/mcp"
    }
  }
}
```

## Claude (claude.ai / Desktop / mobile)

Settings → Connectors → Add custom connector:

- URL: `https://cheats.aarontrotter.com/mcp`
- Header: leave blank for OAuth, or `Authorization: Bearer <your key>`

claude.ai brokers the connector through your account, so it also works in the mobile apps.

## ChatGPT

1. Settings → Apps & Connectors → Advanced settings → enable **Developer mode**.
2. Create a connector with MCP server URL `https://cheats.aarontrotter.com/mcp` and
   authentication **OAuth**.
3. Complete the Cheatsheet consent screen when prompted.

## VS Code with GitHub Copilot

Command Palette → **MCP: Add Server** → HTTP → paste `https://cheats.aarontrotter.com/mcp` →
authenticate when prompted. Or add the same `url` block under MCP servers in your user/workspace
MCP config.

## Codex / Gemini / Windsurf

Same hosted URL. Prefer native HTTP / Streamable HTTP transport with OAuth when the client offers
it. If a client only supports stdio, a temporary shim such as
`npx -y mcp-remote https://cheats.aarontrotter.com/mcp` can bridge to the hosted connector — that
shim is an upstream tool, not something this repo ships.

## Marketplaces

The product to list is the **hosted connector** (`https://cheats.aarontrotter.com/mcp`). Plugins
in this repo are optional packaging for one-click install in Claude Code / Cursor — they point at
that same URL. Paste-ready fields and step order: [`MARKETPLACE_SUBMIT.md`](MARKETPLACE_SUBMIT.md).

| Priority | Marketplace | Submit | What to enter |
| --- | --- | --- | --- |
| 1 (primary) | **Claude Connectors Directory** | [claude.ai/directory/manage](https://claude.ai/directory/manage) → **MCP connector** | URL `https://cheats.aarontrotter.com/mcp` |
| 2 | **Cursor Marketplace** | [cursor.com/marketplace/publish](https://cursor.com/marketplace/publish) | This repository |
| 2 | **cursor.directory** | [cursor.directory/plugins/new](https://cursor.directory/plugins/new) | This repository (auto-detects root [`.mcp.json`](.mcp.json)) |
| 3 (optional) | **Claude Plugin Directory** | same Claude portal → Plugin bundle | Repo `AaronTrotter/cheatsheet-mcp`, path `claude-plugin` |

Docs and privacy for listings: [Connect an MCP Client](https://cheats.aarontrotter.com/api-docs/connect-mcp)
and [Privacy Policy](https://cheats.aarontrotter.com/privacy-policy).

## What this repo contains

| Path | Role |
| --- | --- |
| [`claude-plugin/`](claude-plugin/) | Claude Code marketplace plugin (HTTP URL) |
| [`cursor-plugin/`](cursor-plugin/) | Cursor marketplace plugin (HTTP URL) |
| [`.claude-plugin/marketplace.json`](.claude-plugin/marketplace.json) | Claude Code marketplace manifest |
| [`.cursor-plugin/marketplace.json`](.cursor-plugin/marketplace.json) | Cursor marketplace manifest |
| [`.mcp.json`](.mcp.json) | Root MCP config for cursor.directory auto-detect |
| [`assets/`](assets/) | Logos |

## Setup notes

- OAuth: skip creating a key; one is minted on consent and appears under `/user` afterward.
- API key path: create the key first, then paste it into the client's header / token field.
- Redirect URIs for desktop OAuth clients are handled by each client (e.g. Cursor uses
  `http://localhost:8787/callback` and `https://www.cursor.com/agents/mcp/oauth/callback`). The
  cheatsheet authorization server supports dynamic client registration, so new OAuth-aware
  clients can register themselves.
