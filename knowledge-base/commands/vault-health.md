---
description: Run a health check on the knowledge base
allowed-tools: Read, Glob, Grep
---

Run a full health audit on the Company Brain vault using the vault-review skill.

## Step 1: Scan the Vault

Use the GitHub connector to read the `Bolt-Farm-Treehouse/knowledge-base` repository. Scan all content directories:
- `content/notes/permanent/` — count notes, check for orphans
- `content/notes/fleeting/` — check for unprocessed items
- `content/notes/literature/` — check status fields
- `content/notes/source/` — check processed flags
- `content/maps/` — verify MOC coverage
- `content/inbox/` — check backlog
- `content/decisions/` — check for superseded decisions

## Step 2: Run All Checks

Follow the vault-review skill to perform all six health checks:
1. Orphan notes
2. Broken links
3. Contradictions
4. Staleness
5. MOC coverage
6. Unprocessed backlog

## Step 3: Generate Report

Present the health report in the format specified by the vault-review skill. Include the score, stats, issues by severity, and top 3 recommended actions.

Keep the report actionable — every issue should have a clear next step.
