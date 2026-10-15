# Deploying cheats-mcp

This repo ships no server. It is client packaging for the hosted connector at `https://cheats.cloud/mcp`
(the server itself lives in the `cheats` repo, `functions/routes/db/mcpConnector.js`).

"Deploying" means publishing the repo and getting the listings updated:

1. Merge to `main` and push: `git push origin main`. Claude Code and Cursor read the plugins straight
   from GitHub (`/plugin marketplace add AaronTrotter/cheats-mcp`), so a push is the whole release for
   existing marketplace users.
2. If you changed a plugin, bump its version in both manifests so installed copies update:
   `claude-plugin/.claude-plugin/plugin.json` and `.claude-plugin/marketplace.json` (Claude Code),
   `cursor-plugin/.cursor-plugin/plugin.json` and `.cursor-plugin/marketplace.json` (Cursor).
3. Validate locally: `claude plugin validate ./claude-plugin`.
4. Listings (only when the connector URL, name, icon or copy changed): follow
   [`MARKETPLACE_SUBMIT.md`](MARKETPLACE_SUBMIT.md). The Claude Connectors Directory, Cursor Marketplace and
   cursor.directory are submitted by hand in their portals.
5. Record the change in `CHANGELOG.md`.

Do not push until `https://cheats.cloud/mcp` answers: `curl -i -X POST https://cheats.cloud/mcp` should return
`401` with a `WWW-Authenticate` header.
