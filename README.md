# Bolt Farm Treehouse — Cowork Plugins

Internal plugin marketplace for the Bolt Farm Treehouse team.

## Available Plugins

| Plugin | Description |
|--------|-------------|
| [cowork-support-tickets](./cowork-support-tickets) | Create support tickets in ClickUp from your current Cowork session context |

## Installation

### Cowork (Claude Desktop)

Upload plugins via the **Plugins** sidebar in Claude Desktop.

### Claude Code CLI

Add this marketplace, then install plugins:

```
claude plugin marketplace add boltfarmtreehouse/claude-code-plugins
claude plugin install cowork-support-tickets
```

## Adding a New Plugin

1. Create a new directory at the repo root (e.g. `my-plugin/`)
2. Follow the standard plugin structure (`.claude-plugin/plugin.json`, commands, skills, etc.)
3. Add an entry to `.claude-plugin/marketplace.json`
4. Push to main
