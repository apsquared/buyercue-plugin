# BuyerCue plugin for Claude Code and Codex

[BuyerCue](https://www.buyercue.io) finds software businesses with sourced, dated evidence that they pay for growth, and shows the public business contact routes behind each one. This plugin connects your assistant to BuyerCue's remote MCP server and adds two skills: rank companies against what you sell, and draft evidence-based outreach that you review and send yourself.

## Install

Claude Code:

```bash
claude plugin marketplace add apsquared/buyercue-plugin
claude plugin install buyercue@buyercue
```

Codex:

```bash
codex plugin marketplace add apsquared/buyercue-plugin
codex plugin add buyercue@buyercue
```

Installing does not sign you in. Authenticate the `buyercue` server from your app's MCP list (`/mcp`), sign in to BuyerCue in your browser, and choose whether the app may unlock companies and its daily token limit. Step-by-step instructions for each app: https://www.buyercue.io/connect

Don't want a plugin? Add the server directly instead; the setup page has the one-line command for each app. The skills are optional guidance on top of the same server.

## What's in this repository

```
.claude-plugin/marketplace.json   Claude Code marketplace "buyercue"
.agents/plugins/marketplace.json  Codex marketplace "buyercue"
plugins/buyercue/                 the plugin: two skills + one remote MCP server
TESTING.md                        release checks and test scenarios
```

Claude Code and Codex read the same `skills/` and `.mcp.json`, so the workflow instructions are written once. See [plugins/buyercue/README.md](plugins/buyercue/README.md) for what the plugin does, what data it sends and the rules its skills follow.

## Support

- Setup guide: https://www.buyercue.io/connect
- Contact: https://www.buyercue.io/contact
- Privacy: https://www.buyercue.io/privacy

This repository is published from BuyerCue's product repository. Open an issue here for plugin problems.
