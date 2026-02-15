---
name: meeting-ingest
description: >
  This skill should be used when the user says "process meeting notes",
  "ingest meeting transcript", "what happened in the meeting?", "sync meeting
  recordings", "pull transcripts from Google Meet", "grab Zoom transcripts",
  "extract decisions from the meeting", or wants to extract knowledge from
  meeting transcripts and recordings into the Company Brain vault.
version: 0.1.0
---

# Meeting Transcript Ingestion

Extract decisions, action items, insights, and tribal knowledge from Google Meet and Zoom transcripts. Meeting transcripts are one of the richest sources of tribal knowledge because people say things in meetings they never write down.

## Prerequisites

- Google Drive MCP connector (Google Meet transcripts auto-save to Drive)
- The vault lives at `Bolt-Farm-Treehouse/knowledge-base` on GitHub
- Existing source notes in `content/notes/source/`

## Where Transcripts Live

### Google Meet
Google Meet auto-saves transcripts to Google Drive:
- Location: Usually in "My Drive" → "Meet Recordings" or the organizer's Drive
- Format: Google Doc (transcript) and/or .mp4 (recording)
- Search query: `mimeType = 'application/vnd.google-apps.document' and fullText contains 'Transcript'`
- Also try: `name contains 'Meeting' and modifiedTime > '{last_sweep_date}'`

### Zoom
Zoom cloud recordings may appear in:
- Google Drive (if auto-upload is configured)
- A shared Zoom folder
- Search for: `.vtt` files (captions) or documents named with "Zoom" + date patterns

### Manual Uploads
Users may also drop meeting notes or transcripts directly into:
- The vault inbox (`content/inbox/`)
- The Cowork workspace folder

## Process

### Step 1: Find New Transcripts

Search Google Drive for meeting transcripts not yet in the vault:

1. Search for Google Meet transcripts:
   ```
   fullText contains 'Transcript' and mimeType = 'application/vnd.google-apps.document'
   ```
2. Search for documents in known meeting folders
3. Search for recently modified docs with meeting-related names
4. Check `content/.sweep-state/meeting-sweep.md` to exclude already-processed transcripts

### Step 2: Extract Meeting Metadata

For each transcript, identify:
- **Date and time** of the meeting
- **Participants** (from the transcript speaker labels)
- **Meeting title/topic** (from the document title or calendar event)
- **Duration** (approximate from transcript length)

### Step 3: Intelligent Extraction

Transcripts are long and mostly filler. Extract only the valuable parts:

**Priority 1 — Decisions**
Look for language patterns:
- "Let's go with...", "We decided...", "The plan is..."
- "Agreed", "Approved", "Signed off on"
- "We're going to...", "Next step is..."
- Any resolution of a debate or discussion

**Priority 2 — Rationale and Context**
The *why* behind decisions:
- "The reason is...", "Because...", "The thinking is..."
- "We tried X and it didn't work because..."
- "The constraint is...", "We can't do X because..."

**Priority 3 — Action Items and Commitments**
- "I'll take care of...", "{name} will..."
- "By {date} we need to..."
- "The deadline is..."

**Priority 4 — Insights and Observations**
- "I noticed that...", "What's interesting is..."
- "The trend is...", "Guests are saying..."
- New information shared for the first time

**Priority 5 — Open Questions**
- "We still need to figure out..."
- "The question is..."
- "We don't know yet..."

### Step 4: Package Extracted Content

Don't atomize the raw transcript. Instead, create a structured extraction:

```markdown
# Meeting: {title} — {date}

## Participants
{list of speakers identified}

## Decisions Made
1. {decision} — rationale: {why}
2. {decision} — rationale: {why}

## Key Context Shared
- {insight or context that was shared}
- {tribal knowledge that came up}

## Action Items
- {person}: {action} by {date}

## Open Questions
- {unresolved question}

## Notable Quotes
- "{brief quote}" — {speaker} (only if it captures something significant)
```

### Step 5: Route Through Discernment

Pass the structured extraction (not the raw transcript) to the discernment skill.

The structured extraction will almost always score high because meetings inherently contain decisions and tribal knowledge. But discernment will still catch:
- Routine status meetings with no decisions
- Meetings that only covered topics already in the vault

### Step 6: Process Based on Verdict

- **atomize** → Hand the structured extraction to the atomizer
  - Also create the raw transcript as a source note for provenance
  - Each decision becomes its own permanent note or decision record
  - Key context becomes permanent notes
  - Action items become fleeting notes (they're time-bound)
- **fleeting** → Create a meeting summary fleeting note
- **skip** → Log as scanned but no new knowledge

### Step 7: Report

```markdown
## Meeting Ingestion Report — {date}

### Transcripts Found: {n}
| Meeting | Date | Participants | Verdict |
|---------|------|-------------|---------|
| {title} | {date} | {names} | atomize |

### Knowledge Extracted
- Decisions: {n} → Decision records created
- Insights: {n} → Permanent notes created
- Action items: {n} → Fleeting notes created
- Open questions: {n} → Flagged for follow-up

### Next Sweep
Last sweep: {timestamp}
```

## Sweep State

Store at `content/.sweep-state/meeting-sweep.md`:

```yaml
---
type: system
last_sweep: YYYY-MM-DDTHH:MM:SS
transcripts_processed: n
meetings_by_id:
  - doc_id: "{google drive doc id}"
    meeting_date: YYYY-MM-DD
    processed_date: YYYY-MM-DDTHH:MM:SS
    decisions_extracted: n
---
```

## Edge Cases

- **Very long meetings** (>1 hour transcript): Process in sections, focus on the last 20% where decisions usually happen
- **Multiple speakers with same name**: Use context clues to disambiguate
- **Meetings in multiple languages**: Note the language, process what's understandable
- **Poor audio quality transcripts**: Flag as low-confidence, create fleeting notes instead of permanent ones
- **Recurring meetings**: Track by meeting series to avoid re-processing the same standing agenda items
