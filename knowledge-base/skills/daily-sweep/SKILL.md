---
name: daily-sweep
description: >
  This skill should be used when the user says "run the daily sweep",
  "sync everything", "update the vault", "what's new across all sources?",
  "run ingestion", "catch up the knowledge base", or wants to run all
  ingestion sources in sequence to bring the vault up to date. Designed
  to be run as a scheduled shortcut for automated daily ingestion.
version: 0.1.0
---

# Daily Sweep — Vault Ingestion Orchestrator

Run all ingestion sources in sequence to bring the Company Brain vault up to date. This is the master skill that coordinates Drive, ClickUp, Meetings, and Cowork ingestion with discernment gating.

## Overview

The daily sweep runs every ingestion source, deduplicates findings, routes content through discernment, and produces a consolidated report. Designed to keep the vault within one day of current.

## Execution Order

Run sources in this order (each depends on the vault being up-to-date from previous steps):

```
1. Google Drive     → New/updated documents
2. ClickUp          → Tasks, docs, comments with decisions
3. Meeting Transcripts → Decisions and context from calls
4. Cowork Artifacts  → Session outputs worth preserving
5. Discernment (batch) → Score and route all findings
6. Atomization       → Process accepted content
7. Vault Health (quick) → Verify nothing broke
```

## Process

### Step 1: Pre-Flight Check

Before running any ingestion:
1. Verify all MCP connectors are active (Google Drive, ClickUp)
2. Read sweep state files from `content/.sweep-state/` to determine last run times
3. Set the sweep window: `last_sweep_time` → `now`

If a connector is down, log the issue and skip that source (don't block the entire sweep).

### Step 2: Run Ingestion Sources

Execute each ingestion skill in sequence. For each source:
1. Call the skill's scan phase
2. Collect all new content items
3. Do NOT process yet — collect everything first for batch discernment

Collect results into a unified queue:

```yaml
sweep_queue:
  - source: google-drive
    items:
      - title: "..."
        content: "..."
        metadata: {...}
  - source: clickup
    items: [...]
  - source: meeting-transcript
    items: [...]
  - source: cowork
    items: [...]
```

### Step 3: Batch Discernment

Run all collected items through the discernment skill in batch mode.

Before scoring, do a cross-source deduplication pass:
- The same content might appear in both Drive and ClickUp
- A meeting transcript might reference a document also found in Drive
- Remove duplicates, keeping the richer version

Then score each unique item and sort by verdict:
- **ATOMIZE** queue
- **FLEETING** queue
- **SKIP** list

### Step 4: Process ATOMIZE Queue

For each item in the atomize queue:
1. Run the atomizer skill
2. Track: source → source note → permanent notes created → links discovered
3. If atomization fails on any item, move it to fleeting queue with a note

### Step 5: Process FLEETING Queue

For each item in the fleeting queue:
1. Create a fleeting note in `content/notes/fleeting/`
2. Include enough context that a human can later decide to promote or archive

### Step 6: Quick Vault Health Check

After all processing, run a lightweight health check:
- Any broken links created during this sweep?
- Any orphan notes that should be linked?
- Are new notes properly indexed in MOCs?

Don't run the full vault review — just check the new content.

### Step 7: Update Sweep State

Update all sweep state files with the current timestamp. Write a consolidated sweep log:

```yaml
# content/.sweep-state/sweep-log.md
---
type: system
sweep_date: YYYY-MM-DDTHH:MM:SS
duration_minutes: n
---

## Sweep Log — {date}

### Sources Scanned
- Google Drive: {n} items checked ({status})
- ClickUp: {n} items checked ({status})
- Meeting Transcripts: {n} found ({status})
- Cowork Artifacts: {n} found ({status})

### Results
- Total new items found: {n}
- Duplicates removed: {n}
- Atomized: {n} → {n} permanent notes
- Fleeting notes created: {n}
- Skipped: {n}

### Issues
- {any errors, skipped sources, or flags}
```

### Step 8: Consolidated Report

Present the full sweep report to the user (or log it if running automated):

```markdown
## Daily Sweep Report — {date}

### Summary
{One paragraph: what's new, what was added, what was skipped}

### By Source

#### Google Drive
- Scanned: {n} documents
- New: {n} | Atomized: {n} | Fleeting: {n} | Skipped: {n}
- Notable: {highlight the most significant new addition}

#### ClickUp
- Scanned: {n} tasks/docs/comments
- New: {n} | Atomized: {n} | Fleeting: {n} | Skipped: {n}
- Notable: {highlight}

#### Meeting Transcripts
- Processed: {n} transcripts
- Decisions captured: {n}
- Notable: {highlight}

#### Cowork Artifacts
- Scanned: {n} files
- New: {n} | Atomized: {n} | Fleeting: {n} | Skipped: {n}
- Notable: {highlight}

### Vault Impact
- New permanent notes: {n}
- New fleeting notes: {n}
- New source notes: {n}
- New links created: {n}
- MOCs updated: {n}

### Human Review Needed
- {items flagged for human decision}
- {contradictions found}
- {borderline discernment scores}

### Next Sweep
Scheduled for: {next run time}
```

## Scheduling

This skill is designed to run as a scheduled shortcut. Recommended cadence:

- **Daily** (recommended): Run every morning to catch previous day's activity
- **Twice daily**: Morning + evening for fast-moving periods
- **Weekly**: For teams with lower content volume

The shortcut command is `/daily-sweep` and can be scheduled via the Cowork shortcut scheduler.

## Error Handling

- If a single source fails, log the error and continue with other sources
- If discernment fails on an item, default to FLEETING (safe fallback)
- If atomization fails on an item, preserve it as a source note and flag for manual processing
- Never fail the entire sweep because one item errored
- Always produce a report, even if partial

## Performance

- Target: Complete a full sweep in under 10 minutes
- If the backlog is large (first run or after a long gap), process in batches of 20 items
- Prioritize recent content over older content when batching
