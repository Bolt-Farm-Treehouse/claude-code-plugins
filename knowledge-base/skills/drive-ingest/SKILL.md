---
name: drive-ingest
description: >
  This skill should be used when the user says "check Drive for new docs",
  "sync Google Drive", "what's new on Drive", "ingest from Drive",
  "pull docs from shared drive", "scan Drive for vault updates", or wants to
  find Google Drive documents that haven't been added to the knowledge base yet.
  It scans My Drive, Shared Drives, and shared-with-me documents.
version: 0.1.0
---

# Google Drive Ingestion

Scan Google Drive for documents that haven't been processed into the Company Brain vault. Compares Drive contents against existing source notes to find what's new, then routes new documents through the discernment skill.

## Prerequisites

- Google Drive MCP connector must be active (uses `google_drive_search` and `google_drive_fetch` tools)
- The vault lives at `Bolt-Farm-Treehouse/knowledge-base` on GitHub
- Existing source notes live in `content/notes/source/`

## Process

### Step 1: Build the Vault Source Index

Before scanning Drive, build a list of what the vault already has. Search the vault's `content/notes/source/` directory for all source notes. Extract the `original_url` and `source` fields from each note's frontmatter. This is the "already ingested" list.

Also check `content/notes/source/` for any notes with `original_format: google-doc` to catch documents that were previously ingested without a URL.

### Step 2: Scan Google Drive

Search across three scopes, in this order:

**Scope A — Shared Drive (Bolt Farm)**
```
Search for recent documents modified in the last 7 days (or since last sweep).
Focus on Google Docs and PDFs.
```

**Scope B — My Drive**
```
Search for documents you own, modified recently.
Exclude personal/non-work content by filtering for known work folders.
```

**Scope C — Shared With Me**
```
Search for documents shared with keagan@boltfarmtreehouse.com.
Focus on recently shared or recently modified.
```

For each scope, use the Google Drive search tool with appropriate queries:
- `modifiedTime > '{last_sweep_date}'` to get recent changes
- `mimeType = 'application/vnd.google-apps.document'` for Google Docs
- Exclude trashed files

### Step 3: Deduplicate Against Vault

For each document found in Drive:
1. Check if its URL matches any `original_url` in the vault source index
2. Check if its title closely matches any existing source note title
3. If neither match → it's a **new document**
4. If URL matches but `modifiedTime` is newer than source note's `created` date → it's an **updated document**

### Step 4: Fetch and Preview New Documents

For each new or updated document:
1. Use `google_drive_fetch` to retrieve the document content
2. Generate a brief summary (2-3 sentences) of what the document contains
3. Note the document's metadata: title, author, last modified, sharing scope

### Step 5: Route Through Discernment

Pass each new document to the discernment skill (see `skills/discernment/SKILL.md`) with:
- The document content
- The document metadata
- The source: `google-drive`

The discernment skill will return a verdict: `atomize`, `fleeting`, or `skip`.

### Step 6: Process Based on Verdict

- **atomize** → Hand off to the atomizer skill with the full document
- **fleeting** → Create a fleeting note in `content/notes/fleeting/` with a quick summary
- **skip** → Log as skipped in the sweep report (no vault action)

### Step 7: Report

Present a summary to the user:

```markdown
## Drive Ingestion Report — {date}

### Scanned
- Shared Drive (Bolt Farm): {n} documents checked
- My Drive: {n} documents checked
- Shared With Me: {n} documents checked

### New Documents Found: {n}
| Document | Source | Verdict | Action |
|----------|--------|---------|--------|
| {title}  | {scope} | atomize | → Atomized into {n} permanent notes |
| {title}  | {scope} | fleeting | → Created fleeting note |
| {title}  | {scope} | skip | → Skipped (reason) |

### Updated Documents: {n}
| Document | What Changed | Action |
|----------|-------------|--------|

### Next Sweep
Last sweep timestamp saved. Next sweep will check for changes after {now}.
```

## Sweep State

Track the last sweep timestamp so subsequent runs only look at what's changed. Store this in the vault at `content/.sweep-state/drive-sweep.md`:

```yaml
---
type: system
last_sweep: YYYY-MM-DDTHH:MM:SS
documents_processed: n
---
```

## Edge Cases

- **Very large documents** (>10,000 words): Summarize first, then atomize in batches
- **Spreadsheets and slides**: Note their existence but flag for human review (not ideal for atomization)
- **Duplicate titles across scopes**: Use URL as the canonical identifier, not title
- **Documents with restricted access**: Log as inaccessible and skip
