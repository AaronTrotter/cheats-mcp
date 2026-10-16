# Marketplace submission checklist

**Best path for Cheats:** list the **hosted connector** first (maximum visibility across
Claude products). Optional IDE **plugins** package the same URL for one-click install.

Packaging is on `main`. The hosted connector is live on production with `title`, `readOnlyHint`
and `destructiveHint` on every tool (`annotations.title` included, which the directory checks), an
OAuth challenge on both `GET` and `POST /mcp`, and RFC 9728 / 8414 metadata on the root and
`/mcp`-suffixed `.well-known` paths. It deploys from the `cheats` repo (see its DEPLOY.md; the
automatic deploy on push to `master` is currently failing, so deploy by hand).

## Shared listing copy

| Field | Value |
| --- | --- |
| Server URL | `https://cheats.cloud/mcp` |
| Name | Cheats |
| One-liner | Your snippets, notes and project boards, searchable and editable from Claude. |
| Description | Cheats is your personal library of code snippets, commands and notes. With this connector, Claude can search and read everything in it, and on a Pro plan it can also add, edit and delete entries. It also reaches the rest of your account: Notes and lists, Brain (guidance and memories you keep for Claude), your Projects board, logged Hours, Pennies (your investment log) and Dues (money owed to you). You sign in with OAuth, and on the consent screen you choose which sections Claude can reach. A Free account connects read-only, up to 20 requests a day. Your content stays private to you, except for Projects you choose to share. |
| Docs | https://cheats.cloud/api-docs/connect-mcp |
| Privacy | https://cheats.cloud/privacy-policy |
| Support | support@cheats.cloud |
| Homepage | https://cheats.cloud |
| Icon | `assets/cheats-icon-512.png` (square PNG, for the Claude directory's custom icon upload); `assets/cheats-lockup-onwhite.svg` for plugin listings |
| Auth | OAuth 2.0 with dynamic client registration |
| Categories | Productivity, Notes, Developer tools |

## Use cases, prerequisites and read/write (directory form)

Shown to reviewers only, not on the public listing.

| # | Use case | Example prompt |
| --- | --- | --- |
| 1 | Look up a cheat or note | Search my Cheats for the git rebase cheat and show me the steps. |
| 2 | Capture a note or list | Add a list called "Renew domains" to my Cheats notes with one item each for example.com, example.org and example.net. |
| 3 | Track time and earnings | Log 2 hours on the Acme website project today for "Homepage fixes", and show how much I've earned this month. |

- **Prerequisites:** A Cheats account at cheats.cloud. A Free account connects read-only, up to
  20 requests a day: Claude asks for read and write, and the consent screen grants a Free account
  the read half. Write tools (add, update, delete) need a Pro plan. Sign-in is OAuth, so no API key
  or admin permission is needed. On the consent screen the user can untick app sections (Cheats,
  Notes, Brain, Projects, Pennies, Hours, Dues) to limit what the connector reaches, and can change
  this later on their User page. Reviewers: use the supplied Pro test account, which needs a git
  rebase cheat and an Hours project called "Acme website" with an hourly rate for the prompts above.
- **Read/write capabilities:** **Read and write.** The form checks this against the tool
  annotations and rejects "Read only", because 26 tools are annotated as writing.
- Keep the example prompts away from Pennies and Dues so reviewers are not pointed at private
  financial data.

## Priority order

1. **Claude Connectors Directory** — primary; everybody on Claude can find and connect.
2. **Cursor Marketplace** + **cursor.directory** — Cursor users.
3. **Claude Plugin Directory** — optional Claude Code one-click install of the same connector.

## 1. Claude Connectors Directory (do this first)

1. Open https://claude.ai/directory/manage → **Submit new** → **MCP connector** (not plugin).
2. Connection: paste `https://cheats.cloud/mcp` (or pick an existing custom connector).
3. Tools: every tool should show a title and read-only / destructive annotations with no warnings. If the form still says "Missing title annotation", the Cheats app deploy has not finished; check the GitHub Actions run and re-add the connector.
4. Listing: use the shared copy above; upload `assets/cheats-icon-512.png` as the custom icon. Public URL: https://raw.githubusercontent.com/AaronTrotter/cheats-mcp/main/assets/cheats-icon-512.png (Claude's favicon lookup showed the wrong image for `aarontrotter.com`, so set the icon explicitly).
5. Auth: OAuth with dynamic client registration.
6. Use cases: paste the table above; choose Read and write.
7. Test & launch: create a dedicated reviewer test account (Pro, with sample data, none of your real Pennies or Dues) and confirm you’ve exercised the tools as a custom connector.
8. Compliance acknowledgements → submit.

## 2. Cursor Marketplace

1. Open https://cursor.com/marketplace/publish
2. Submit repository: `https://github.com/AaronTrotter/cheats-mcp`
3. Manifest: `.cursor-plugin/marketplace.json` (plugin in `cursor-plugin/`)

## 3. cursor.directory

1. Open https://cursor.directory/plugins/new
2. Sign in with GitHub.
3. Paste `https://github.com/AaronTrotter/cheats-mcp` → Scan repo (detects root `.mcp.json`).
4. Publish Plugin.

## 4. Claude Plugin Directory (optional)

Only after the connector is submitted. Same portal → **Plugin bundle**.

1. Repository: `AaronTrotter/cheats-mcp`
2. Plugin path: `claude-plugin`
3. Branch: `main`
4. **Validate** → fix blocking findings → submit.
5. Optional locally: `claude plugin validate ./claude-plugin`

## After listings go live

Update the Marketplaces section on
https://cheats.cloud/api-docs/connect-mcp with the concrete directory URLs.
