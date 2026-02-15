---
name: vault-review
description: >
  This skill should be used when the user asks "how's the vault?", "review the
  knowledge base", "vault health check", "check for orphan notes", "find
  contradictions", "what needs attention in the vault", "clean up the knowledge
  base", or requests any kind of maintenance, audit, or quality review of the
  Company Brain vault.
version: 0.1.0
---

# Vault Review

Perform a health audit on the Company Brain Zettelkasten and produce an actionable report.

## Prerequisites

The vault lives at `Bolt-Farm-Treehouse/knowledge-base` on GitHub. All content is in the `content/` directory. Use the GitHub MCP connector to read files and scan the vault.

## Health Checks

Run these checks in order:

### Check 1: Orphan Notes

Find permanent notes in `content/notes/permanent/` with zero incoming links. Classify each as:
- Seed of a new topic area (suggest linking)
- Poorly connected note (find its links)
- Low-value note (suggest archiving)

### Check 2: Broken Links

Find `[[wiki-links]]` that point to non-existent notes. Classify each as:
- Target was renamed (fix the link)
- Target was deleted (remove link or recreate note)
- Forward reference to something not yet written (flag it)

### Check 3: Contradictions

Find notes that make conflicting claims. Don't resolve them — flag them for human decision. Contradictions are where learning happens.

### Check 4: Staleness

Find notes with `status: active` that:
- Haven't been linked to recently
- Reference decisions that may have been superseded
- Contain time-sensitive language ("currently", "this quarter", "right now")

### Check 5: MOC Coverage

- Are there clusters of 5+ linked notes without a MOC?
- Are existing MOCs in `content/maps/` still accurate?
- Does the home page index still reflect the current state?

### Check 6: Unprocessed Backlog

- How many notes have `status: unprocessed`?
- How many items are sitting in `content/inbox/`?

## Output Format

Produce a vault health report:

```markdown
## Vault Health Report — {date}

### Score: {A/B/C/D/F}

### Stats
- Permanent notes: {n}
- Total links: {n}
- Average links per note: {n}
- Orphan notes: {n}
- Unprocessed backlog: {n}
- MOCs: {n}

### Issues
#### Critical
- {issue + recommended action}

#### Important
- {issue + recommended action}

#### Minor
- {issue + recommended action}

### Top 3 Actions
1. {highest priority}
2. {second priority}
3. {third priority}
```

## Principles

- Archive, never delete
- Suggest changes to human-written notes, don't overwrite
- Contradictions are features, not bugs
- A messy vault with great links > a pristine vault with no connections
