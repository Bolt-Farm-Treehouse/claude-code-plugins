---
name: cowork-sync
description: >
  This skill should be used when the user says "sync my Cowork outputs",
  "add my Claude artifacts to the vault", "vault my session work",
  "sync artifacts", "what did I create in Cowork that's not in the vault?",
  or wants to capture documents, analyses, and deliverables created in
  Claude/Cowork sessions into the Company Brain vault.
version: 0.1.0
---

# Cowork Artifact Sync

Scan the Cowork outputs folder for documents, analyses, reports, and other artifacts created during Claude sessions. Route vault-worthy content through discernment and into the knowledge base.

## Prerequisites

- Access to the Cowork workspace folder (mounted at the session's output path)
- The vault lives at `Bolt-Farm-Treehouse/knowledge-base` on GitHub
- Existing source notes in `content/notes/source/`

## What Counts as a Cowork Artifact

Cowork sessions produce files in the workspace folder. These are the types worth scanning:

### High Value
- **Strategy documents** (.docx, .md) — Analyses, plans, recommendations
- **Research reports** — Competitive analyses, market research, data analyses
- **Decision support** — Pro/con analyses, frameworks, evaluations
- **Process documentation** — SOPs, workflows, guides created or refined with Claude
- **Data analyses** (.html dashboards, .xlsx with insights) — Conclusions and methodology

### Medium Value
- **Email drafts and communications** — If they contain decisions or strategic reasoning
- **Meeting prep documents** — If they capture context or strategy
- **Presentations** (.pptx) — If they contain original analysis or strategy (not just formatting)

### Low Value (Usually Skip)
- **One-off calculations** — Quick math or data lookups
- **Format conversions** — Files that were just reformatted
- **Temporary working files** — Intermediate outputs with no standalone value
- **Code scripts** — Unless they encode a process or methodology worth documenting

## Process

### Step 1: Scan the Workspace

List all files in the Cowork workspace folder. For each file:
1. Note the filename, type, size, and last modified date
2. Read the file content (or a summary for large files)
3. Check if this file has been previously synced

### Step 2: Build Sync History

Check `content/.sweep-state/cowork-sweep.md` in the vault for the list of previously synced files (tracked by filename and hash). Any file not in the sync history is **new**.

### Step 3: Evaluate Each New File

For each new file, determine if it's worth evaluating:
- Skip binary files that can't be meaningfully atomized (images, raw data files)
- Skip very small files (<50 words) unless they're clearly decision records
- For documents, read the full content

### Step 4: Route Through Discernment

Pass each candidate to the discernment skill with:

```yaml
source: cowork
content_type: artifact
title: "{filename}"
author: "claude/keagan"
created: "{file modified date}"
content: "{file content or summary}"
context: "Created during a Cowork session. File type: {extension}"
```

### Step 5: Process Based on Verdict

- **atomize** → Hand off to the atomizer skill
  - Set `original_format: cowork-artifact` in the source note
  - Set `author: "claude/keagan"` since Cowork artifacts are collaborative
  - Preserve the original file content in the source note
- **fleeting** → Create a fleeting note summarizing what was created and why it might matter
- **skip** → Log as skipped

### Step 6: Update Sync History

After processing, update `content/.sweep-state/cowork-sweep.md`:

```yaml
---
type: system
last_sweep: YYYY-MM-DDTHH:MM:SS
files_synced:
  - filename: "report.docx"
    synced_date: YYYY-MM-DDTHH:MM:SS
    verdict: atomize
    notes_created: 5
  - filename: "quick-calc.xlsx"
    synced_date: YYYY-MM-DDTHH:MM:SS
    verdict: skip
---
```

### Step 7: Report

```markdown
## Cowork Sync Report — {date}

### Workspace Scanned
- Total files found: {n}
- Previously synced: {n}
- New files to evaluate: {n}

### Results
| File | Type | Verdict | Action |
|------|------|---------|--------|
| {filename} | .docx | atomize | → {n} permanent notes |
| {filename} | .md | fleeting | → Fleeting note created |
| {filename} | .xlsx | skip | → Data file, no knowledge content |

### Artifacts Atomized
- "{document title}" → {n} permanent notes about {topic}
```

## Special Handling

- **HTML dashboards**: Extract the analysis methodology and conclusions, not the code
- **Presentations**: Extract the narrative and key points, not the slide formatting
- **Spreadsheets**: Extract any analysis conclusions, formulas as methodology notes, and data interpretations — not raw data
- **Multi-file projects**: If multiple files clearly belong to the same project, package them as one source document for atomization
