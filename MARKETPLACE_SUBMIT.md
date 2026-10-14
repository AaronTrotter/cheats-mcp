# Marketplace submission checklist

Packaging is on `main` (`67b15ca`). Hosted connector annotations ship with the cheatsheet app
deploy from `bed36f3`. Wait until Firebase deploy finishes before Claude’s Tools step (it syncs
from the live URL).

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
| Icon | `assets/cheatsheet-lockup-onwhite.svg` in this repo (or `claude-plugin/assets/…` / `cursor-plugin/assets/…`) |
| Auth | OAuth 2.0 with dynamic client registration |
| Categories | Productivity, Notes, Developer tools |

## 1. Claude Connectors Directory

1. Open https://claude.ai/directory/manage → **Submit new** → **MCP connector**.
2. Connection: paste `https://cheats.aarontrotter.com/mcp` (or pick an existing custom connector).
3. Tools: confirm every tool has a title and read-only / destructive annotations (no flags).
4. Listing: use the shared copy above; upload the lockup icon.
5. Auth: OAuth with dynamic client registration.
6. Test & launch: add a reviewer test account (Pro, with sample data) and confirm you’ve exercised the tools as a custom connector.
7. Compliance acknowledgements → submit.

## 2. Claude Plugin Directory

1. Same portal → **Submit new** → **Plugin bundle**.
2. Repository: `AaronTrotter/cheatsheet-mcp` (or `https://github.com/AaronTrotter/cheatsheet-mcp`).
3. Plugin path: `claude-plugin`
4. Branch: `main` (default).
5. **Validate** → fix any blocking findings → submit for review.
6. Optional on your machine first: `claude plugin validate ./claude-plugin`

## 3. Cursor Marketplace

1. Open https://cursor.com/marketplace/publish
2. Submit repository: `https://github.com/AaronTrotter/cheatsheet-mcp`
3. Manifest: `.cursor-plugin/marketplace.json` (plugin in `cursor-plugin/`)

## 4. cursor.directory

1. Open https://cursor.directory/plugins/new
2. Sign in with GitHub.
3. Paste `https://github.com/AaronTrotter/cheatsheet-mcp` → Scan repo (detects root `.mcp.json`).
4. Publish Plugin.

## After listings go live

Update the Marketplaces section on
https://cheats.aarontrotter.com/api-docs/connect-mcp with the concrete directory URLs.
