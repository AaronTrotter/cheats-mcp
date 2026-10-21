# Changelog

All notable changes to this repository are documented here. The format follows
[Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and the repository version follows
[Semantic Versioning](https://semver.org/spec/v2.0.0.html).

Note that the Claude Code and Cursor plugins under [`claude-plugin/`](claude-plugin/) and
[`cursor-plugin/`](cursor-plugin/) each carry their own version number in their plugin and
marketplace manifests, because that is what a plugin update on someone else's machine compares
against. They move only when something inside that plugin directory changes, so they do not track
the repository version. Each entry below records where the plugins stood at that release.

## [Unreleased]

### Added

- **Hosted connector:** every Projects card now carries a cost `estimate`: a `light`/`medium`/
  `heavy` tier worked out from the card itself (no model call), the AI agent it suits, and the
  tokens, dollars and credits that agent would likely spend, including whether the job fits its
  context window. `search_project_tasks` takes `cost` (a list of tiers) and `sort` (`newest`,
  `cheapest`, `costliest`) and returns `totals` for the cards it returns, so "do all the light
  cards and say what it will cost" is one call. `add_project_task` and `update_project_task`
  take `costTier`, `agentId` and `effort` to pin them by hand (`null` unpins on update). The
  estimate names the effort level to run the agent at (`estimate.effort`, e.g. `high`).
- **Hosted connector:** `search_project_tasks` and `get_project_task` take `runner`, the vendor of
  the calling model (e.g. `Anthropic`). Each estimate then adds `forRunner`: the closest agent from
  that vendor, the effort to run it at and what it would cost, so a card whose best fit is another
  vendor's model (Grok, say) still tells Claude which Claude model to use. The connect
  instructions tell clients to pass it.
- **Hosted connector:** `search_ai_agents` and `get_ai_agent` read the agent list the estimates
  choose from: the official list plus any agents the user added for themselves on the Agents page,
  minus the ones they switched off. Each agent has `scope` (`official`/`mine`), `efforts`,
  strengths, context window, prices and credits. Both sit in a new `agents` section, with its own
  checkbox on the connect screen and the API key picker, so the connector now has 49 tools, 23 of
  them read tools. A key narrowed to some sections before this release doesn't include `agents`
  until it is reconnected or edited; card estimates work either way.

### Fixed

- **Hosted connector:** `search_cheats`'s `type` filter now works. It is documented as a type
  name such as `git`, but the name was matched against the search index's type ids, so any type
  filter returned nothing. It now looks the name up (case-insensitively), and an unknown type
  returns no results.

### Changed

- **Breaking (hosted connector):** `search_brain` now ranks its results and returns snippets.
  Query words are matched one by one, in any language, so a whole sentence or a single broad word
  still finds the right entries, best match first. Each result now has a short `snippet` instead
  of the entry's full `text`, plus `keywords`, `textLength` and `matchedTerms`. The response
  adds `total`. New `limit` (default 8, up to 25) and `full` (return full text) arguments.
  Anything that read `text` from `search_brain` should now call `get_brain`, or pass
  `full: true`. The REST endpoint `/mcp/searchBrain` is unchanged.
- **Hosted connector:** clearer tool descriptions for the AI.
  - `search_cheats` now says it also returns other people's public cheats. Each result and
    `get_cheat` carry `mine`, true for the cheats this account created and can edit or delete.
  - `get_cheat` also returns `ceased`, true when the id is an old revision that has since been
    edited or deleted.
  - `add_cheat` and `update_cheat` now say that `private: false` publishes the cheat to everyone.
    `update_cheat` says to keep the cheat's current visibility unless the user asks to change it.
  - `update_cheat`, `update_note`, `update_brain` and `update_project_task` now say which fields
    replace the old values, that they return a new id, and that the new id is the one to use from
    then on.
  - `update_note` and `update_brain` now say that leaving out `duration` makes the entry
    permanent, rather than keeping its expiry.
- **Hosted connector:** a Free account connecting over OAuth now gets read-only access, up to 20
  tool calls a day, instead of being refused. Every connection's instructions open with a short
  guide to what each section is for and what can only be done on the site.
- Plugin descriptions now mention notes, lists and project boards (Claude Code plugin 3.2.1,
  Cursor plugin 1.2.1).
- **Breaking (hosted connector):** the Cheats app has renamed its Tasks section to Notes, so the
  connector's tools are now `search_notes`, `get_note`, `add_note`, `update_note` and
  `delete_note` instead of `search_tasks`, `get_task`, `add_task`, `update_task` and
  `delete_task`. Arguments are unchanged, and a note is still either a `note` or a `list`. The
  REST endpoints behind them move from `/mcp/*Task` to `/mcp/*Note`, and `/mcp/searchNotes` and
  `/mcp/searchBrain` now return `{ notes, totalPages }` rather than `{ tasks, totalPages }`. An
  API key's section id is `notes` rather than `tasks`, and the page is `/notes`. The Projects
  card tools (`*_project_task`) are not renamed. This repository registers only the hosted URL,
  so nothing here calls the old names, but saved prompts or allowlists that name them need
  updating. The plugin READMEs, plugin keywords and the listing copy in
  [`MARKETPLACE_SUBMIT.md`](MARKETPLACE_SUBMIT.md) now say Notes. The Claude Code plugin is
  **3.2.0** and the Cursor plugin **1.2.0**. Entries below are left as written, since they were
  accurate at their release.
- Rebranded from Cheatsheet to Cheats: the hosted connector moves to `https://cheats.cloud/mcp` and support to
  `support@cheats.cloud`. The repository is now `cheats-mcp`, the marketplace is `cheats-mcp` and
  the plugin id is `cheats` (install with `/plugin install cheats@cheats-mcp`). The Claude Code plugin
  is **3.1.0** and the Cursor plugin **1.1.0**. The old `cheats.aarontrotter.com` origin keeps working.
- Hosted connector: the tools now state their real input limits, and the server no longer changes
  input without saying so. Titles, names and short descriptions keep apostrophes, quotes, commas,
  slashes, brackets and letters in any language (only `<`, `>` and control characters are
  removed); cheat type names and Pennies tickers keep the old strict set. A body over 100 lines,
  a line over 600 characters, or a title or name over its stated length is now rejected with an
  error naming the limit instead of being cut short, so the model can fix it and retry. The input
  schemas carry the limits (`min`/`max` lengths, a 3-letter pattern on currency codes, plain-text
  and list-format notes on body fields), and a write connection's `instructions` add one short
  paragraph of house rules. Nothing in this repository changes, and the plugin versions stay put.


### Removed

- Local MCP entry points: `live-proxy.js` (stdio → hosted HTTP proxy), `local-mcp-server/` (stdio
  REST re-implementation), and the Claude plugin's bundled `server.js` / npm dependencies. Every
  supported client can use the hosted Streamable HTTP connector with OAuth (or a bearer API key),
  so a local Node process is no longer needed or maintained here.

### Changed

- Claude Code plugin **3.0.0** is HTTP-only: `.mcp.json` uses `"type": "http"` and
  `https://cheats.aarontrotter.com/mcp`. No key file, no stdio proxy.
- Root README is a multi-platform connector guide (Claude, Claude Code, Cursor, ChatGPT, VS Code
  Copilot, Codex, Gemini CLI, Windsurf) instead of an Option A / Option B local-vs-hosted menu.
- Repository version **2.0.0** — client packaging only; no runtime Node dependencies.

### Added

- Per-client config snippets and a compatibility table in the README, covering the keys that
  differ between clients (VS Code `servers`, Gemini `httpUrl`, Windsurf `serverUrl`, Codex TOML).
- [`.codex/config.toml`](.codex/config.toml) for Codex CLI.
- A Cursor plugin under [`cursor-plugin/`](cursor-plugin/), plus a root
  [`.cursor-plugin/marketplace.json`](.cursor-plugin/marketplace.json), registering the same hosted
  URL for Cursor marketplace / local plugin installs.
- A Cursor-format `mcp.json` next to Claude Code's `.mcp.json` inside
  [`claude-plugin/`](claude-plugin/), so either host format can load the HTTP connector from that
  directory.

## [1.3.0] - 2026-09-07

Plugin version at this release: 2.0.0 (unchanged).

### Added

- Five Brain tools in `local-mcp-server/`, matching the hosted MCP Connector: `search_brain` and
  `get_brain` (read), `add_brain`, `update_brain` and `delete_brain` (write). Brain was already
  reachable through the task tools by passing `section` or `categories`, so these add no reach;
  they exist so an assistant's own memory is a tool it can find rather than an argument it has to
  know to pass, and they refuse an id belonging to a task, so deleting a `memory` can never
  quietly delete a note instead. `add_brain` defaults to the `memory` category.
- Eight Projects tools, also matching the hosted connector: `search_projects`,
  `search_project_tasks` and `get_project_task` (read), `add_project`, `add_project_task`,
  `update_project_task`, `move_project_task` and `delete_project_task` (write). Projects is the
  user's Kanban board, and `move_project_task` is the one to use for a progress update because it
  keeps the card's id, where `update_project_task` is an append-only revision that returns a new
  one.
- Renaming a project, deleting a whole project and card sharing are deliberately not exposed. A
  rename has no caller asking for it, deleting a project ceases every card on it at once, and
  sharing hands a card to people outside the account.

### Changed

- The tool set is now thirty tools rather than seventeen. Both READMEs and the tool table are
  updated to match.
- **Breaking:** the task tools now cover Tasks only. Tasks and Brain have diverged into separate
  sections, so `search_tasks`, `get_task`, `add_task`, `update_task` and `delete_task` no longer
  reach a Brain entry, and the brain tools do not reach a task. An id addressed through the wrong
  section's tool comes back as a 404 instead of quietly working, and a category from the wrong
  section is a 400.
- **Breaking:** `search_tasks` drops its `section` and `categories` arguments for a single
  `category` (`note` or `list`), matching `search_brain`'s shape. Anything that previously called
  it with `section: 'brain'` or a Brain category should call the matching brain tool instead.
- API keys can now be limited to particular sections of the app on `/user`. This script is
  unaffected in what it registers (all thirty tools, since nothing tells a key its own sections),
  but a call outside a narrowed key's sections now returns 403. The hosted connector does narrow
  its tool list to match the key, so prefer it when that matters.
- `add_task` and `update_task` now accept only `note` and `list`. `update_task` and `update_brain`
  carry the item's existing category forward when none is given, so an edit that omits the field
  can no longer move an item between sections.

## [1.2.0] - 2026-09-06

Plugin version at this release: 2.0.0 (unchanged).

### Added

- Five Pennies tools in `local-mcp-server/`, matching the hosted MCP Connector: `search_pennies`
  and `get_penny_summary` (read), `add_penny`, `fill_penny` and `void_penny` (write). Pennies is
  the user's own private log of investment orders across crypto, stocks, ETFs, bonds, commodities
  and CFDs, plus the portfolio derived from it.
- `get_penny_summary` takes an optional `taxYear`, whose boundaries follow the user's own country
  rather than assuming a calendar year, so a UK tax year correctly runs 6 April to 5 April.

### Changed

- The tool set is now seventeen tools rather than twelve. Both READMEs and the tool table are
  updated to match.

### Notes

- Pennies was deliberately absent from the API surface until now, on the grounds that a private
  financial log should not be reachable by a key. It is now reachable on the same terms as
  everything else: a read-only key can read it but cannot change it, so issuing read-only keys is
  how an integration is kept out of it.
- A logged order is immutable, so there is no `update_penny`. `fill_penny` confirms that a pending
  limit order executed, and `void_penny` is the only way an order is removed.
- `live-proxy.js` and the Claude Code plugin needed no change: the proxy forwards `tools/list` and
  `tools/call` rather than re-implementing them, so it picks up the new tools automatically.

## [1.1.1] - 2026-09-06

Plugin version at this release: 2.0.0 (unchanged).

### Changed

- Documentation only: no tool, argument or behaviour in this repository changed. Cheatsheet has
  split Tasks and Brain into two Firestore collections (`tasks` and `brain`) so that each carries
  its own Free-tier cap of 5 items, instead of the two sharing one allowance between them. The task
  tools are unaffected, because the split sits behind one shared set of REST endpoints and every
  tool still addresses an item by id or by category. `local-mcp-server/README.md` and the
  tool-registration comment in `local-mcp-server/index.js` both described the old single-collection
  model and now describe the new one, including the per-page cap. The 1.1.0 entry below is left as
  written, since it was accurate at that release.

## [1.1.0] - 2026-09-05

Plugin version at this release: 2.0.0 (unchanged).

### Added

- `local-mcp-server/index.js` now registers the six Task and Brain tools — `search_tasks`,
  `get_task`, `add_task`, `update_task`, `delete_task` and `get_guides` — bringing it to the same
  twelve tools the hosted MCP Connector exposes. Previously it covered cheats only, so switching a
  project between the local server and any of the hosted paths silently changed what an agent
  could reach. The task tools span both the Tasks and Brain pages, which are one collection
  server-side split by `category`; `search_tasks` takes a `section` argument to narrow to one.
- `get_guides` returns the user's `brief` and `rules` entries as one formatted block. The hosted
  connector sends that same text as its `instructions` on connect, but a stdio server has no
  equivalent hook, so this server exposes it as a tool to call explicitly.

### Changed

- Both READMEs now describe the full twelve-tool set instead of documenting the local server as
  cheats-only, and `local-mcp-server/README.md` is reframed around what it is actually for: the
  path to start from when you want to change the tool set, since it wraps the REST API rather than
  forwarding to the hosted connector.
- Corrected the README's note about the synthesized `url` field, which is added by every cheat tool
  except `delete_cheat` — including `get_revisions`, which was previously omitted.

## [1.0.0] - 2026-08-31

Plugin version at this release: 2.0.0.

### Added

- `live-proxy.js`, a stdio MCP proxy in front of the hosted `/mcp` Streamable HTTP connector, which
  loads the API key from a gitignored `.env.local` rather than requiring a shell environment
  variable that every process under the same OS user could read.
- `local-mcp-server/index.js`, a standalone stdio server wrapping the cheatsheet's REST `/mcp/*`
  API directly, kept as a customizable reference for projects that want their own variant.
- `claude-plugin/`, a Claude Code plugin bundling its own copy of the proxy, distributed through
  the `cheatsheet-mcp` marketplace in `.claude-plugin/marketplace.json`.
- Documentation for reaching the cheatsheet from claude.ai, Claude Desktop, and mobile as a custom
  connector, none of which need a local process.

### Changed

- The plugin reads its API key from a file in Claude Code's persistent plugin data directory
  instead of a bearer token in an environment variable, so the key survives `/plugin update` and
  stays readable only by the one script that needs it. Released as plugin 2.0.0.
