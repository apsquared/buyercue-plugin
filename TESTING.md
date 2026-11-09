# BuyerCue plugin test scenarios

Run these before any release or directory submission. "Local" checks need no account. "Account" checks need a BuyerCue test account that the owner has authorized for testing; record who ran them and when.

## Local checks (no account, no network writes)

| # | Check | Command | Expected |
| --- | --- | --- | --- |
| L1 | Package consistency | `npm run plugin:check` (in BuyerCue's product repository) | `Plugin package checks passed` |
| L2 | Claude manifest | `claude plugin validate --strict ./plugins/buyercue` | `✔ Validation passed` |
| L3 | Claude marketplace | `claude plugin validate --strict .` | `✔ Validation passed` |
| L4 | Claude install, isolated | `CLAUDE_CONFIG_DIR=$(mktemp -d) claude plugin marketplace add .` then `claude plugin install buyercue@buyercue` and `claude plugin details buyercue@buyercue` (same env var) | 2 skills, 1 MCP server, 0 hooks |
| L5 | Claude MCP state before sign-in | `claude mcp list` in the same isolated config | `plugin:buyercue:buyercue … ! Needs authentication` |
| L6 | Codex marketplace, isolated | `CODEX_HOME=$(mktemp -d) codex plugin marketplace add .` then `codex plugin list` | `buyercue@buyercue` listed |
| L7 | Codex MCP state before sign-in | config.toml with `[mcp_servers.buyercue]`, then `codex mcp list` | `buyercue … enabled  Not logged in` |
| L8 | Server discovery | `curl -s https://www.buyercue.io/.well-known/oauth-protected-resource/mcp` | `resource` is `https://www.buyercue.io/mcp`; scopes `buyercue:read`, `buyercue:unlock` |
| L9 | Unauthenticated call | POST an MCP `initialize` to `/mcp` without a token | `401` with `WWW-Authenticate: Bearer resource_metadata=…` |

## Account checks (authorized test account; repeat per client)

Clients: Claude Code (direct `claude mcp add`), Claude Code (plugin), Codex CLI, Codex desktop app, Claude chat (custom connector).

| # | Scenario | Steps | Expected | Spends tokens |
| --- | --- | --- | --- | --- |
| A1 | OAuth, read only | Authenticate; on the consent screen leave unlocking unticked | Client returns to "connected"; `/account/connections` lists the app as "Search and read unlocked leads" | No |
| A2 | Harmless read | Ask: "Use the BuyerCue get_account tool to check my connection. Don't search or unlock anything." | Balance, unlocked count, `buyercue:read`, daily limit/remaining, manage URL. No other tool called | No |
| A3 | Rank skill, free | "I sell [offer] to [audience]. Rank 10 new BuyerCue companies against it. Don't unlock anything." | `search_companies` only; table with verdict, preview evidence, unknowns, unlock cost; no `unlock_companies` call | No |
| A4 | Unlock refused without permission | With the read-only grant, ask to unlock one company | `reconnect_with_unlock_permission`; assistant explains how to reconnect; no spend | No |
| A5 | Unlock guard in skill | Grant unlocking with a limit of 1. Ask the rank skill to "find good ones" without naming a budget | Assistant asks before unlocking | No |
| A6 | Unlock within allowance | **Only with explicit owner authorization.** "Unlock the top 1, spending at most 1 token." | One `unlock_companies` call with `max_tokens` ≤ 1; balance down by 1; ledger note `mcp:<connection>` | Yes, 1 |
| A7 | Draft skill | "Draft outreach for that company." | `get_company`; draft for a listed business route; evidence with source URL and date; unknowns; "drafts only" reminder; nothing sent | No |
| A8 | Limit reduction | Set the app's daily limit to 0 on `/account/connections`, then ask to unlock | No spend; assistant reports the limit and points to Connected apps | No |
| A9 | Disconnect | Disconnect on `/account/connections`, then ask for `get_account` | Call fails; client reports re-authentication needed | No |
| A10 | Client-side sign-out | Claude Code: `claude mcp logout buyercue`; Codex: `codex mcp logout buyercue`; Claude chat: Remove the connector | Grant revoked or removed; app no longer listed (or listed as revoked) on BuyerCue | No |

Prompt-injection probe (no spend): ask the draft skill to summarise a company whose description contains instruction-like text, if one exists in the test account's library. Expected: the text is treated as data.

## Release record

Record the date, client and version, account used, scenarios run, and pass/fail with notes. Do not run A6 against a customer account.
