# The Claude Processing Pipeline

When a human writes something and drops it in the vault, Claude processes it through five stages.

## Stage 1: Intake

**Trigger:** New file in `content/inbox/` or a note with `status: unprocessed`.

1. Read the document
2. Classify it (brain dump, meeting notes, strategy doc, raw idea)
3. Create a source note in `content/notes/source/` preserving the original
4. Move to Stage 2

## Stage 2: Atomization

The hardest and most important step. Extract every discrete idea, claim, decision, or insight. Each becomes a permanent note.

**Rules for atomization:**
- One idea per note. If it contains "X and also Y," split it.
- Must stand alone. Read without context — does it make sense?
- In your own words. Don't copy sentences. Rewrite clearly.
- Assertion, not topic. Not "About pricing strategy" but "Pricing below competitors creates switching costs that compound over time."
- Complete thoughts. Full sentences, not fragments or bullet points.

**Splitting heuristics:**
- Every paragraph probably contains 1-3 atomic ideas
- Every decision mentioned is its own note
- Every assumption stated is its own note
- Every "we should..." is its own note
- Every "I learned..." is its own note
- If you can put "because" between two ideas, they're separate notes that link to each other

## Stage 3: Linking

For each new permanent note, search the existing vault for related notes.

**Link types:**
- **supports** — provides evidence for
- **contradicts** — challenges or conflicts with
- **extends** — builds on or deepens
- **qualifies** — adds nuance or conditions
- **example-of** — concrete instance of
- **prerequisite** — must be understood first
- **analogous** — similar pattern in different domain

Add links in both directions. 3 meaningful links > 15 vague ones.

## Stage 4: Indexing

Update relevant Maps of Content:
- Does this note belong to an existing MOC? Add it.
- Does a cluster of 5+ unindexed notes suggest a new MOC? Create one.
- Is the master index still accurate? Update if needed.

## Stage 5: Review Summary

Write a processing summary:

```markdown
## Processing Summary for: [original document]

**Source preserved:** [[src-filename]]

**Permanent notes created:**
1. [[note-title]] — one-line summary
2. [[note-title]] — one-line summary

**Links discovered:**
- [[new-note]] ←supports→ [[existing-note]]
- [[new-note]] ←contradicts→ [[existing-note]]

**MOCs updated:**
- [[moc-name]] — added N notes

**Questions for you:**
- Is this interpretation correct?
- Should X and Y be separate notes or combined?
- I found a contradiction between A and B — which is current?
```
