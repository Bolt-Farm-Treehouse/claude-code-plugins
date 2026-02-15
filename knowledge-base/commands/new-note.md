---
description: Create a new note in the vault
allowed-tools: Read, Write, Glob, Grep, AskUserQuestion
argument-hint: [note type: fleeting, permanent, decision, or literature]
---

Help the user create a new note in the Company Brain vault.

## Step 1: Determine Note Type

If the user specified a type (fleeting, permanent, decision, literature), use that. Otherwise, use AskUserQuestion:

"What kind of note are you creating?"
Options:
- Fleeting — Quick thought or idea to capture now
- Permanent — A refined, standalone idea
- Decision — Recording a decision with context and reasoning
- Literature — Summarizing an external source (article, meeting, conversation)

## Step 2: Gather Content

Use AskUserQuestion to get the note content. Tailor the question to the note type:

- **Fleeting:** "What's the thought or idea?" (keep it simple)
- **Permanent:** "What's the idea? State it as a clear assertion." Then ask for 2-3 paragraphs explaining it.
- **Decision:** Ask for: what was decided, what options were considered, why this was chosen, and what follows from it.
- **Literature:** Ask for: what source, key ideas, and their reactions to it.

## Step 3: Create the Note

Generate the note following the vault-knowledge skill templates. Place it in the correct directory:
- Fleeting → `content/notes/fleeting/`
- Permanent → `content/notes/permanent/`
- Decision → `content/decisions/`
- Literature → `content/notes/literature/`

Use the correct naming convention and frontmatter from the vault-knowledge skill.

## Step 4: Link (for Permanent and Decision notes)

Search the vault for related notes and add bidirectional links. Present the links found to the user.

## Step 5: Confirm

Tell the user the note was created. Include:
- The note title and location
- Any links added
- For fleeting notes: "This will be reviewed and either promoted or archived."
