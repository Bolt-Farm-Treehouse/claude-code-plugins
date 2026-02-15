---
description: Process a document into atomic vault notes
allowed-tools: Read, Write, Glob, Grep, Bash(git:*), AskUserQuestion
argument-hint: [paste text or describe the document]
---

A user wants to add a document to the Company Brain vault. Process it through the full Zettelkasten pipeline.

## Step 1: Get the Document

If the user provided text directly, use that. If they referenced a file or Google Doc, read its contents.

If no document was provided, use AskUserQuestion to ask:
"What document would you like to process? You can paste text directly, reference a file in the conversation, or describe where to find it."

## Step 2: Load the Atomizer Skill

Follow the atomizer skill instructions to process the document through all six stages:
1. Preserve the original as a source note
2. Read and classify
3. Extract atomic ideas into permanent notes
4. Link to existing vault knowledge
5. Update Maps of Content
6. Write a processing summary

## Step 3: Write to the Vault

Use the GitHub connector to create all files in the `Bolt-Farm-Treehouse/knowledge-base` repository under the `content/` directory.

Commit with the message prefix `[atomize]` for new permanent notes and `[intake]` for the source note.

## Step 4: Present Summary

Show the user:
- How many permanent notes were created
- One-line summary of each
- Links discovered to existing notes
- Any questions about interpretation or ambiguity

Keep it concise — the user can browse the vault for details.
