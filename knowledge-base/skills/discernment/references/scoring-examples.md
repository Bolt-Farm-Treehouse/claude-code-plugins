# Discernment Scoring Examples

Real-world examples to calibrate scoring decisions.

## Example 1: ATOMIZE (Score: 14/15)

**Content:** A Google Doc titled "Why We Switched from Stripe to Square for Treehouse Payments"
- Decision Content: 3 — Explicit decision with detailed rationale
- Tribal Knowledge: 3 — Only the ops team knows the full story
- Reusability: 3 — Will inform future payment decisions
- Uniqueness: 3 — Not in the vault yet
- Clarity: 2 — Well-written but some sections ramble

**Verdict:** ATOMIZE — This is a decision record with irreplaceable context.

## Example 2: FLEETING (Score: 8/15)

**Content:** A ClickUp task comment: "Talked to the arborist — he said the oaks near Unit 3 need trimming before storm season. Budget around $2k. Let's do it in March."
- Decision Content: 2 — Implicit decision (going ahead with trimming)
- Tribal Knowledge: 2 — Specific vendor relationship and timing knowledge
- Reusability: 1 — Specific to this season but sets a pattern
- Uniqueness: 2 — No existing notes on tree maintenance
- Clarity: 1 — Casual comment, needs interpretation

**Verdict:** FLEETING — Worth capturing the maintenance pattern and vendor info, but not worth full atomization.

## Example 3: SKIP (Score: 2/15)

**Content:** A Google Doc that's just a bulleted list of links to other resources, no commentary.
- Decision Content: 0 — No decisions
- Tribal Knowledge: 0 — Just links
- Reusability: 1 — Might be useful as a reference list
- Uniqueness: 1 — Links may point to things already in vault
- Clarity: 0 — No actual content to extract

**Verdict:** SKIP — No knowledge to extract. The linked documents themselves may be worth ingesting separately.

## Example 4: OVERRIDE → ATOMIZE (Score: 5/15 but overridden)

**Content:** A rough, informal post-mortem in ClickUp comments about a guest complaint at the treehouse.
- Decision Content: 1 — Vague
- Tribal Knowledge: 1 — Some
- Reusability: 1 — Maybe
- Uniqueness: 1 — New incident
- Clarity: 1 — Very rough

**Verdict:** ATOMIZE (override) — Post-mortems are always atomized regardless of score. Even rough ones contain lessons.

## Example 5: OVERRIDE → SKIP (Score: 11/15 but overridden)

**Content:** A well-written Google Doc that's nearly identical to an existing vault permanent note.
- Decision Content: 3
- Tribal Knowledge: 3
- Reusability: 3
- Uniqueness: 0 — Already in the vault
- Clarity: 2

**Verdict:** Would score ATOMIZE, but uniqueness is 0 and vault check confirms duplicate. SKIP with note: "Duplicate of [[existing-note-title]]"
