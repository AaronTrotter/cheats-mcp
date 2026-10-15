<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/cheats-lockup-ondark.svg">
  <img alt="Cheats" src="assets/cheats-lockup-onwhite.svg" width="340" height="56">
</picture>

# cheats-mcp

Client packaging and install docs for the [Cheats](https://cheats.cloud) hosted MCP
connector. Point any MCP-compatible client at:

```text
https://cheats.cloud/mcp
```

That endpoint is Streamable HTTP with standard OAuth discovery
(`/.well-known/oauth-authorization-server`, `/.well-known/oauth-protected-resource`). Leave auth
headers blank and the client runs Cheats' sign-in / consent flow; a scoped API key is minted
for you and listed under `/user` → API Access. Untick any app sections on the consent screen to
limit which tools the connector offers.

The server itself lives in the app repo
([`functions/routes/db/mcpConnector.js`](https://github.com/AaronTrotter/cheats)). This repo only
packages how clients reach it. The same walkthrough from the web app's side is on
[Connect an MCP Client](https://cheats.cloud/api-docs/connect-mcp).

There is no local MCP process to install. Older stdio proxies and the REST-backed local example
were removed — every major client below can use the hosted URL directly.

## Quick connect

| Client | How |
| --- | --- |
| **Claude** (claude.ai / Desktop / mobile) | Settings → Connectors → Add custom connector → URL above |
| **Claude Code** | [`claude-plugin/`](claude-plugin/README.md), or `claude mcp add --transport http cheats https://cheats.cloud/mcp` |
| **Cursor** | [`cursor-plugin/`](cursor-plugin/README.md), or `url` entry in `~/.cursor/mcp.json` |
| **ChatGPT** (web) | Settings → Apps & Connectors → enable Developer mode → Create connector → URL + OAuth |
| **VS Code + Copilot** | MCP: Add Server → HTTP → URL above (config key is `servers`, see [snippets](#per-client-config-snippets)) |
| **Codex CLI** | [`.codex/config.toml`](.codex/config.toml) entry, then `codex mcp login cheats` |
| **Gemini CLI** | `gemini mcp add --transport http cheats https://cheats.cloud/mcp` (the settings key is `httpUrl`, not `url`) |
| **Windsurf** | `serverUrl` entry in `~/.codeium/windsurf/mcp_config.json` |
| **Other MCP clients** | Any client speaking Streamable HTTP + OAuth 2.1 (PKCE, dynamic client registration) |

## Authentication

**Sign in with Cheats (recommended).** Supported on Cursor, Claude connectors, Claude Code,
ChatGPT, Copilot, and other OAuth-aware MCP clients. No key to create beforehand.

**Paste an API key.** For clients that only accept a bearer token, create one at `/user` → API
Access and send `Authorization: Bearer <key>`. Use a separate, narrowly-scoped key per client so
you can revoke one without touching the others.

## Claude Code plugin

```
/plugin marketplace add AaronTrotter/cheats-mcp
/plugin install cheats@cheats-mcp
```

Details: [`claude-plugin/README.md`](claude-plugin/README.md). Claude Code needs
`"type": "http"` on URL entries in `.mcp.json`.

## Cursor plugin

Import this repo as a Cursor marketplace, or copy [`cursor-plugin/`](cursor-plugin/) into
`~/.cursor/plugins/local/cheats` and reload. Details:
[`cursor-plugin/README.md`](cursor-plugin/README.md).

Manual `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "cheats": {
      "url": "https://cheats.cloud/mcp"
    }
  }
}
```

## Claude (claude.ai / Desktop / mobile)

Settings → Connectors → Add custom connector:

- URL: `https://cheats.cloud/mcp`
- Header: leave blank for OAuth, or `Authorization: Bearer <your key>`

claude.ai brokers the connector through your account, so it also works in the mobile apps.

## ChatGPT

1. Settings → Apps & Connectors → Advanced settings → enable **Developer mode**.
2. Create a connector with MCP server URL `https://cheats.cloud/mcp` and
   authentication **OAuth**.
3. Complete the Cheats consent screen when prompted.

## VS Code with GitHub Copilot

Command Palette → **MCP: Add Server** → HTTP → paste `https://cheats.cloud/mcp` →
authenticate when prompted. Or add the same `url` block under MCP servers in your user/workspace
MCP config.

## Per-client config snippets

Config keys differ between clients, and the wrong one silently falls back to a different transport.
These are the exact shapes.

**VS Code** (`.vscode/mcp.json` or user MCP config). The top-level key is `servers`, not
`mcpServers`:

```json
{
  "servers": {
    "cheats": { "type": "http", "url": "https://cheats.cloud/mcp" }
  }
}
```

**Codex CLI** (`~/.codex/config.toml`, same as the checked-in [`.codex/config.toml`](.codex/config.toml)):

```toml
[mcp_servers.cheats]
url = "https://cheats.cloud/mcp"
```

Then run `codex mcp login cheats` to complete OAuth. For the API-key path add
`bearer_token_env_var = "CHEATS_API_KEY"` and export that variable.

**Gemini CLI** (`~/.gemini/settings.json`). `httpUrl` selects Streamable HTTP; plain `url` means
legacy SSE and will not work against this server:

```json
{
  "mcpServers": {
    "cheats": { "httpUrl": "https://cheats.cloud/mcp" }
  }
}
```

**Windsurf** (`~/.codeium/windsurf/mcp_config.json`). Remote servers use `serverUrl`:

```json
{
  "mcpServers": {
    "cheats": { "serverUrl": "https://cheats.cloud/mcp" }
  }
}
```

**Claude Desktop** does not read remote servers from `claude_desktop_config.json`; add the URL
under Settings → Connectors instead (see above).

**stdio-only clients.** If a client cannot speak HTTP at all, a shim such as
`npx -y mcp-remote https://cheats.cloud/mcp` can bridge to the hosted connector. That
shim is an upstream tool, not something this repo ships.

## Compatibility notes

What a client needs from the server, and where it stands (verified against production):

| Requirement | Status |
| --- | --- |
| Streamable HTTP at a single `/mcp` endpoint (no SSE-only transport) | Yes, stateless |
| `401` with `WWW-Authenticate: Bearer resource_metadata=…` | Yes |
| RFC 9728 protected-resource metadata at `/.well-known/oauth-protected-resource` | Yes |
| RFC 8414 authorization-server metadata | Yes |
| Dynamic client registration (RFC 7591) | Yes; `https` and `localhost` redirect URIs |
| PKCE `S256` | Required |
| Tool `title` plus `readOnlyHint` / `destructiveHint` annotations | Yes, on every tool |
| Refresh tokens | No: the access token is a long-lived, revocable API key (`/user` → API Access) |
| Custom-scheme redirect URIs (`cursor://`, `vscode://`) | Rejected; those clients use their `https` or `localhost` callbacks |
| ChatGPT `search` / `fetch` tools (deep research, company knowledge) | Not provided; tools are `search_cheats`, `get_cheat`, … so use ChatGPT Developer mode |

## Marketplaces

The product to list is the **hosted connector** (`https://cheats.cloud/mcp`). Plugins
in this repo are optional packaging for one-click install in Claude Code / Cursor — they point at
that same URL. Paste-ready fields and step order: [`MARKETPLACE_SUBMIT.md`](MARKETPLACE_SUBMIT.md).

| Priority | Marketplace | Submit | What to enter |
| --- | --- | --- | --- |
| 1 (primary) | **Claude Connectors Directory** | [claude.ai/directory/manage](https://claude.ai/directory/manage) → **MCP connector** | URL `https://cheats.cloud/mcp` |
| 2 | **Cursor Marketplace** | [cursor.com/marketplace/publish](https://cursor.com/marketplace/publish) | This repository |
| 2 | **cursor.directory** | [cursor.directory/plugins/new](https://cursor.directory/plugins/new) | This repository (auto-detects root [`.mcp.json`](.mcp.json)) |
| 3 (optional) | **Claude Plugin Directory** | same Claude portal → Plugin bundle | Repo `AaronTrotter/cheats-mcp`, path `claude-plugin` |

Docs and privacy for listings: [Connect an MCP Client](https://cheats.cloud/api-docs/connect-mcp)
and [Privacy Policy](https://cheats.cloud/privacy-policy).

## What this repo contains

| Path | Role |
| --- | --- |
| [`claude-plugin/`](claude-plugin/) | Claude Code marketplace plugin (HTTP URL) |
| [`cursor-plugin/`](cursor-plugin/) | Cursor marketplace plugin (HTTP URL) |
| [`.claude-plugin/marketplace.json`](.claude-plugin/marketplace.json) | Claude Code marketplace manifest |
| [`.cursor-plugin/marketplace.json`](.cursor-plugin/marketplace.json) | Cursor marketplace manifest |
| [`.mcp.json`](.mcp.json) | Root MCP config for cursor.directory auto-detect |
| [`.codex/config.toml`](.codex/config.toml) | Codex CLI config for the hosted URL |
| [`assets/`](assets/) | Logos |

## Setup notes

- OAuth: skip creating a key; one is minted on consent and appears under `/user` afterward.
- API key path: create the key first, then paste it into the client's header / token field.
- Redirect URIs for desktop OAuth clients are handled by each client (e.g. Cursor uses
  `http://localhost:8787/callback` and `https://www.cursor.com/agents/mcp/oauth/callback`). The
  Cheats authorization server supports dynamic client registration, so new OAuth-aware
  clients can register themselves.
