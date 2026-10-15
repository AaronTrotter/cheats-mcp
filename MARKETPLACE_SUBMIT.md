# Marketplace submission checklist

**Best path for Cheatsheet:** list the **hosted connector** first (maximum visibility across
Claude products). Optional IDE **plugins** package the same URL for one-click install.

Packaging is on `main`. Hosted connector annotations are live on production after the cheatsheet
app deploy that added `readOnlyHint` / `destructiveHint`.

## Shared listing copy

| Field | Value |
| --- | --- |
| Server URL | `https://cheats.aarontrotter.com/mcp` |
| Name | Cheatsheet |
| One-liner | Search and manage your Cheatsheet cheats, tasks, and notes from your AI client. |
| Description | Cheatsheet’s hosted MCP connector gives Claude, Cursor, and other MCP clients live access to your account: cheats, Tasks, Brain, Projects, Pennies, Hours, and Dues. Sign in with OAuth (or paste a scoped API key). Untick sections on the consent screen to limit what the client can reach. |
| Docs | https://cheats.aarontrotter.com/api-docs/connect-mcp |
| Privacy | https://cheats.aarontrotter.com/privacy-policy |
| Support | cheats@aarontrotter.com |
| Homepage | https://cheats.aarontrotter.com |
| Icon | `assets/cheatsheet-icon-512.png` (square PNG, for the Claude directory's custom icon upload); `assets/cheatsheet-lockup-onwhite.svg` for plugin listings |
| Auth | OAuth 2.0 with dynamic client registration |
| Categories | Productivity, Notes, Developer tools |

## Priority order

1. **Claude Connectors Directory** — primary; everybody on Claude can find and connect.
2. **Cursor Marketplace** + **cursor.directory** — Cursor users.
3. **Claude Plugin Directory** — optional Claude Code one-click install of the same connector.

## 1. Claude Connectors Directory (do this first)

1. Open https://claude.ai/directory/manage → **Submit new** → **MCP connector** (not plugin).
2. Connection: paste `https://cheats.aarontrotter.com/mcp` (or pick an existing custom connector).
3. Tools: confirm every tool has a title and read-only / destructive annotations (no flags).
4. Listing: use the shared copy above; upload `assets/cheatsheet-icon-512.png` as the custom icon (Claude may otherwise show a cached or parent-domain favicon).
5. Auth: OAuth with dynamic client registration.
6. Test & launch: add a reviewer test account (Pro, with sample data) and confirm you’ve exercised the tools as a custom connector.
7. Compliance acknowledgements → submit.

## 2. Cursor Marketplace

1. Open https://cursor.com/marketplace/publish
2. Submit repository: `https://github.com/AaronTrotter/cheatsheet-mcp`
3. Manifest: `.cursor-plugin/marketplace.json` (plugin in `cursor-plugin/`)

## 3. cursor.directory

1. Open https://cursor.directory/plugins/new
2. Sign in with GitHub.
3. Paste `https://github.com/AaronTrotter/cheatsheet-mcp` → Scan repo (detects root `.mcp.json`).
4. Publish Plugin.

## 4. Claude Plugin Directory (optional)

Only after the connector is submitted. Same portal → **Plugin bundle**.

1. Repository: `AaronTrotter/cheatsheet-mcp`
2. Plugin path: `claude-plugin`
3. Branch: `main`
4. **Validate** → fix blocking findings → submit.
5. Optional locally: `claude plugin validate ./claude-plugin`

## After listings go live

Update the Marketplaces section on
https://cheats.aarontrotter.com/api-docs/connect-mcp with the concrete directory URLs.
