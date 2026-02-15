---
name: clickup-ingest
description: >
  This skill should be used when the user says "sync ClickUp", "check ClickUp
  for updates", "pull from ClickUp", "ingest ClickUp docs", "what decisions
  were made in ClickUp", "grab ClickUp notes", or wants to extract knowledge
  from ClickUp tasks, documents, and comments into the Company Brain vault.
version: 0.1.0
---

# ClickUp Ingestion

Extract knowledge-worthy content from ClickUp — task descriptions, documents, comments containing decisions, and completed work with learnings — and route it into the Company Brain vault.

## Prerequisites

- ClickUp MCP connector must be active
- GitHub MCP connector must be active for vault read/write
- **Read `vault-knowledge/references/vault-connection.md` first** — it defines how to read from and write to the vault via GitHub API
- Run the pre-flight check from vault-connection.md before any vault operations
- The vault lives at `Bolt-Farm-Treehouse/knowledge-base` on GitHub (always accessed via API, never local filesystem)
- Existing source notes in `content/notes/source/`

## What's Worth Ingesting from ClickUp

Not everything in ClickUp belongs in the vault. Focus on these content types:

### High Value (Almost Always Ingest)
- **ClickUp Docs** — Long-form documents, SOPs, strategy docs, specs
- **Task comments that contain decisions** — "We decided to...", "Going with option B because..."
- **Completed tasks with rich descriptions** — Tasks that document *why* something was done, not just *what*
- **Decision logs or meeting note tasks** — Tasks used to track decisions

### Medium Value (Run Through Discernment)
- **Task descriptions with context** — Descriptions that explain rationale, constraints, or tradeoffs
- **Comments with learnings** — "Lesson learned:", "For next time:", "What worked:"
- **Epics/milestones with retrospective content** — Post-mortems, retros

### Low Value (Usually Skip)
- Simple status update comments ("Done", "Updated", "Fixed")
- Tasks with minimal descriptions (just a title and assignee)
- Routine operational tasks with no learning content

## Process

### Step 1: Build the Vault ClickUp Index

Search the vault's `content/notes/source/` for any source notes with `original_format: clickup` or `source_url` containing `clickup.com`. This is the "already ingested" list.

### Step 2: Scan ClickUp

Use ClickUp tools to scan for new content. Search in phases:

**Phase A — ClickUp Documents**
Use `clickup_search` with `asset_types: ["doc"]` to find documents.
Focus on recently created or updated docs.

**Phase B — Tasks with Decisions**
Search for tasks with comments. Look for tasks that are:
- Recently completed (last 7 days or since last sweep)
- Have substantial descriptions (>100 words)
- Have comments containing decision language

**Phase C — Task Comments**
For high-activity tasks, pull comments using `clickup_get_task_comments`.
Scan for decision indicators:
- "decided", "decision", "going with", "we chose", "approved"
- "learned", "lesson", "takeaway", "for next time"
- "changed approach", "pivoted", "reversed"

### Step 3: Deduplicate

For each piece of content found:
1. Check if its ClickUp URL matches any existing source note's `original_url`
2. Check if the task ID appears in any existing source note
3. If neither match → it's **new content**

### Step 4: Package for Discernment

For each new content item, create a package:

```yaml
source: clickup
content_type: doc | task | comment-thread
title: "{content title}"
url: "{clickup URL}"
author: "{who wrote it}"
created: "{date}"
content: "{full text}"
context: "{parent task or space for additional context}"
```

Route each package through the discernment skill.

### Step 5: Process Based on Verdict

- **atomize** → Hand off to the atomizer skill
  - For ClickUp Docs: treat like a full document
  - For task+comments: combine the task description and relevant comments into one source document
  - For comment threads: extract the decision or learning as the core content
- **fleeting** → Create a fleeting note capturing the key point
- **skip** → Log as skipped

### Step 6: Preserve Task Context

When atomizing ClickUp content, preserve important context in the source note:
- Link to the original ClickUp task/doc
- Who was involved (assignees, commenters)
- What project/space it belongs to
- Status at time of ingestion

### Step 7: Report

```markdown
## ClickUp Ingestion Report — {date}

### Scanned
- Documents: {n} checked
- Tasks (completed): {n} checked
- Comment threads: {n} scanned

### New Content Found: {n}
| Content | Type | Verdict | Action |
|---------|------|---------|--------|
| {title} | doc | atomize | → {n} permanent notes |
| {title} | task+comments | fleeting | → Fleeting note created |
| {title} | comment-thread | skip | → No vault-worthy content |

### Spaces Covered
- {space name}: {n} items scanned
```

## Sweep State

Store at `content/.sweep-state/clickup-sweep.md`:

```yaml
---
type: system
last_sweep: YYYY-MM-DDTHH:MM:SS
tasks_processed: n
docs_processed: n
comments_scanned: n
---
```

## Edge Cases

- **Very long comment threads**: Summarize the thread, focus on the final decision
- **Tasks with attachments**: Note the attachment exists but don't try to process binary files
- **Private tasks**: Respect access — only ingest what the authenticated user can see
- **Recurring tasks**: Only ingest if there's unique content, not repeated boilerplate
