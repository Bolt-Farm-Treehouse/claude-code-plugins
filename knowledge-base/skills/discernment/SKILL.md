---
name: discernment
description: >
  This skill should be used when evaluating whether content is worth adding to
  the knowledge base. Triggers include "is this vault-worthy?", "should we add
  this?", "triage this content", "evaluate for the vault", "discern", or when
  any ingestion skill needs to decide whether content should be atomized,
  captured as a fleeting note, or skipped entirely.
version: 0.1.0
---

# Discernment — The Vault Gatekeeper

Evaluate any piece of content and decide: does this belong in the Company Brain? Not everything is worth the effort of atomization. This skill prevents vault bloat by applying consistent criteria.

## The Core Question

**"Will someone on this team benefit from finding this knowledge six months from now?"**

If yes → it belongs in the vault.
If no → skip it.
If maybe → capture it as a fleeting note and let a human decide.

## Scoring Framework

Evaluate content across five dimensions. Each scores 0-3.

### 1. Decision Content (0-3)

Does this contain a decision, or the reasoning behind one?

- **3** — Explicit decision with rationale ("We chose X because Y")
- **2** — Implicit decision (action taken that implies a choice was made)
- **1** — Discussion of options but no clear decision
- **0** — No decision content

### 2. Tribal Knowledge (0-3)

Does this contain knowledge that lives in people's heads and would be lost if they left?

- **3** — Critical context only one or two people know ("The reason we do it this way is...")
- **2** — Institutional knowledge that's commonly known internally but undocumented
- **1** — General best practice that could be found elsewhere
- **0** — Publicly available information with no company-specific context

### 3. Reusability (0-3)

Will this be useful again in the future?

- **3** — Directly reusable (SOP, template, principle, framework)
- **2** — Useful as reference (case study, post-mortem, analysis)
- **1** — Marginally useful (might inform a future decision)
- **0** — One-time use, no future value

### 4. Uniqueness (0-3)

Is this already captured in the vault?

- **3** — Entirely new knowledge or perspective
- **2** — New angle on an existing topic
- **1** — Mostly overlaps with existing notes but adds minor detail
- **0** — Already in the vault (duplicate)

### 5. Clarity (0-3)

Is the content clear enough to extract knowledge from?

- **3** — Well-written, clear ideas, easy to atomize
- **2** — Understandable but needs interpretation
- **1** — Messy but has nuggets worth extracting
- **0** — Too vague, incomplete, or incoherent to be useful

## Verdict Thresholds

**Total Score (0-15):**

- **11-15: ATOMIZE** — High-value content. Run through the full atomizer pipeline.
- **6-10: FLEETING** — Worth capturing but not worth full atomization. Create a fleeting note with a summary and link to the source. Flag for human review.
- **0-5: SKIP** — Not vault-worthy. Log the skip reason for transparency.

## Override Rules

Regardless of score, ALWAYS atomize:

- Any document explicitly labeled as a decision record
- Post-mortems and retrospectives
- SOPs, processes, or documented workflows
- Strategy documents
- Onboarding materials
- Anything a human explicitly asks to add

Regardless of score, ALWAYS skip:

- Calendar invites with no agenda or notes
- Auto-generated notifications or status emails
- Documents that are just links to other documents
- Empty or placeholder documents
- Duplicate content already in the vault

## Input Format

When called by an ingestion skill, expect:

```yaml
source: google-drive | clickup | cowork | meeting-transcript | manual
content_type: document | task | comment-thread | transcript | artifact
title: "Document or content title"
author: "Who created it"
created: "When"
content: "The actual text content to evaluate"
context: "Any additional context (parent task, project, etc.)"
```

## Output Format

Return to the calling skill:

```yaml
verdict: atomize | fleeting | skip
score: {total}/15
breakdown:
  decision_content: {n}/3
  tribal_knowledge: {n}/3
  reusability: {n}/3
  uniqueness: {n}/3
  clarity: {n}/3
reasoning: "One-sentence explanation of the verdict"
override_applied: true/false
override_reason: "If an override rule was triggered"
```

## Batch Mode

When processing multiple items (e.g., during a daily sweep), present results as a table:

```markdown
## Discernment Results — {date}

| # | Title | Source | Score | Verdict | Reason |
|---|-------|--------|-------|---------|--------|
| 1 | {title} | Drive | 13/15 | ATOMIZE | Decision record with unique context |
| 2 | {title} | ClickUp | 7/15 | FLEETING | Some useful context but mostly operational |
| 3 | {title} | Cowork | 3/15 | SKIP | Duplicate of existing vault content |

### Items for Human Review
- Item #2: Scored borderline — contains some tribal knowledge about {topic}. Worth a full atomize?
```

## Principles

- **Err toward capturing** — A fleeting note costs almost nothing. Missing tribal knowledge is expensive.
- **Decisions are always valuable** — Even bad decisions teach something.
- **Context matters more than content** — A short comment explaining *why* is worth more than a long document explaining *what*.
- **The vault is for the team, not for one person** — Personal notes and drafts usually don't belong unless they contain team-relevant knowledge.
- **Duplicates are waste** — Check the vault before adding. But a *new perspective* on an existing topic is not a duplicate.
