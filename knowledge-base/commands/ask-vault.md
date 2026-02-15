---
description: Search and ask questions about vault knowledge
allowed-tools: Read, Glob, Grep, AskUserQuestion
argument-hint: [your question about the knowledge base]
---

A user wants to find information in or ask a question about the Company Brain vault.

## Step 1: Understand the Query

Parse the user's question. Determine if they're looking for:
- A specific note or topic
- Connections between ideas
- A summary of what the vault knows about something
- A decision record or its context
- Something they vaguely remember

## Step 2: Search the Vault

Use the GitHub connector to search the `Bolt-Farm-Treehouse/knowledge-base` repository.

Search strategy:
1. Search `content/notes/permanent/` for keyword matches in filenames and content
2. Search `content/maps/` for relevant MOCs that might index the topic
3. Search `content/decisions/` if the question involves a decision
4. Search `content/notes/literature/` for source material
5. Check `content/notes/source/` if they need the original document

## Step 3: Synthesize an Answer

Don't just list files. Read the relevant notes and synthesize a coherent answer that:
- Directly answers their question
- Cites which notes the answer comes from (using `[[wiki-links]]`)
- Surfaces related notes they might want to explore
- Flags any contradictions found across notes

## Step 4: Suggest Follow-ups

If the search reveals gaps, say so:
- "The vault doesn't have anything on X yet. Want me to create a note?"
- "There are related notes on Y and Z, but nothing directly on X."
- "I found a contradiction between [[note-a]] and [[note-b]] — which is current?"
