---
name: atomizer
description: >
  This skill should be used when the user says "process this document",
  "add this to the vault", "atomize this", "break this down", "import this
  into the knowledge base", "process the inbox", or drops a document, Google Doc,
  meeting notes, brain dump, or any raw content that needs to be turned into
  atomic Zettelkasten notes in the Company Brain vault.
version: 0.1.0
---

# Atomize Document

Take any raw document and extract every discrete idea into standalone, linked permanent notes in the Company Brain vault.

## Prerequisites

The vault lives at `Bolt-Farm-Treehouse/knowledge-base` on GitHub. All content goes in the `content/` directory. Use the GitHub MCP connector to read and write files.

## Process

### Step 1: Preserve the Original

Create a source note at `content/notes/source/src-{slug}.md` with the full original content. This is the archival copy — never modify it after creation.

Frontmatter:
```yaml
---
type: source
created: {current ISO timestamp}
author: {original author}
original_format: {format}
processed: false
tags: []
---
```

### Step 2: Read and Classify

Read the entire document before extracting anything. Understand:
- What is this about overall?
- Who wrote it and why?
- What kind of document is it?

### Step 3: Extract Atomic Ideas

Go through the document and identify every discrete claim, decision, insight, definition, principle, observation, question, and assumption.

For each idea, create a permanent note at `content/notes/permanent/{timestamp}-{slug}.md`:

- Title is an **assertion**, not a topic
- Body is 2-5 paragraphs of clear **prose** (no bullet points)
- Link back to the source note
- Set `author: "claude/{original-author}"`
- Set `status: draft`

Use the timestamp format `YYYYMMDDHHMMSS` — increment seconds for multiple notes created in the same batch.

### Step 4: Link to Existing Knowledge

For each new note, search the vault for related permanent notes. Add bidirectional links. See `references/linking-guide.md` for relationship types and quality rules.

### Step 5: Update Maps of Content

Check if new notes belong in any existing MOC in `content/maps/`. If 5+ new notes cluster around an unlabeled theme, suggest creating a new MOC.

### Step 6: Write Processing Summary

Present to the user:
- What source was preserved
- What permanent notes were created (with one-line summaries)
- What links were discovered
- What MOCs were updated
- Questions for the user (interpretations to confirm, contradictions found)

## Quality Checks

Before finishing, verify each note:
- Contains exactly ONE idea
- Makes sense without reading the source
- Is written in clear prose, not copy-pasted
- Title is an assertion
- Links back to its source
- Has bidirectional links

## Additional Resources

- **`references/linking-guide.md`** — Link types, quality rules, and MOC update guidelines
