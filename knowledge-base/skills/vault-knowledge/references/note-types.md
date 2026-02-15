# Note Type Templates

Complete templates and rules for every note type in the vault.

## Fleeting Note

Quick thought, observation, or idea captured in the moment. Temporary — Claude reviews these and either promotes them to permanent notes or archives them.

```markdown
---
type: fleeting
created: YYYY-MM-DDTHH:MM:SS
author: name
status: unprocessed
tags: []
---

# Title

Content here — write fast, don't worry about quality.
```

## Literature Note

Summary of an external source — article, book, meeting, conversation. Write in your own words. Capture what matters to *you*.

```markdown
---
type: literature
created: YYYY-MM-DDTHH:MM:SS
author: name
source: "Source Title"
source_url: "URL"
source_type: article | book | meeting | conversation | video | podcast
status: unprocessed
tags: []
---

# Title

## Key Ideas
Summary in your own words.

## Relevant Quotes
- "Quote" (p. page)

## My Reactions
What you think about this.
```

## Permanent Note

A single, atomic, refined idea written in complete sentences. This is the core unit. One idea per note. Must stand alone.

**Rules:**
- Contains exactly ONE idea
- Makes sense without reading the source
- Written in your own words (not copy-pasted)
- Title is an assertion, not a topic ("Pricing below competitors creates switching costs" not "About pricing")
- Body is 2-5 paragraphs of clear prose (no bullet points)

```markdown
---
type: permanent
created: YYYY-MM-DDTHH:MM:SS
author: name | "claude" | "claude/name"
source_notes:
  - "[[source-note]]"
status: draft | active
tags: []
links: []
---

# Assertion title — a clear statement of the idea

The idea, explained in 2-5 paragraphs. Written in complete sentences.
No bullet points. No fragments. Should read like a paragraph from a
well-written essay.

## Related
- [[related-note]] — why it's related
- [[another-note]] — why it's related
```

## Map of Content (MOC)

An index note that organizes permanent notes around a topic. Not just a list of links — it tells a story about how the ideas connect.

```markdown
---
type: moc
created: YYYY-MM-DDTHH:MM:SS
updated: YYYY-MM-DDTHH:MM:SS
author: name
status: active
tags: []
---

# Topic Name

2-3 sentence overview of this topic area and why it matters.

## Core Concepts
- [[note]] — one-line summary
- [[note]] — one-line summary

## How These Connect
Prose explaining the relationships between the notes above.
This is what makes a MOC valuable — not the links, but the narrative.

## Open Questions
- What don't we know yet?

## Related Maps
- [[other-moc]]
```

## Source Note

Original document preserved as-is. Never modify after creation.

```markdown
---
type: source
created: YYYY-MM-DDTHH:MM:SS
author: name
original_format: google-doc | markdown | pdf | email
original_url: "URL"
processed: false
processed_date: null
permanent_notes: []
tags: []
---

# Original Document Title

Full original content, preserved as-is.
```

## Decision Record

High-value knowledge capturing what was decided and why.

```markdown
---
type: decision
created: YYYY-MM-DDTHH:MM:SS
author: name
status: active | superseded
superseded_by: "[[newer-decision]]"
participants: []
tags: []
---

# Decision: What was decided

## Context
What situation led to this decision?

## Options Considered
1. **Option A** — pros/cons
2. **Option B** — pros/cons

## Decision
What we decided and why.

## Consequences
What follows from this decision? What are we committing to?

## Review Date
When should we revisit this?
```
