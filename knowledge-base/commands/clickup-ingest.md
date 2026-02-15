---
description: Pull knowledge from ClickUp tasks, docs, and comments into the vault
allowed-tools: Read, Write, Glob, Grep, Bash(git:*), AskUserQuestion
argument-hint: [optional: specific space, task, or search terms]
---

Extract knowledge-worthy content from ClickUp — task descriptions, documents, and comments containing decisions or learnings — and add it to the Company Brain vault.

## Step 1: Load Skills

Read the clickup-ingest skill and the discernment skill.

## Step 2: Scan ClickUp

Use ClickUp MCP tools to search for:
- ClickUp documents (recently created or updated)
- Completed tasks with rich descriptions
- Comments containing decisions or learnings

If the user specified a space, task, or search terms, narrow the scan.

## Step 3: Evaluate and Process

Run each piece of content through discernment. Process accepted content through the atomizer. Preserve ClickUp task context (project, assignees, links) in the source notes.

## Step 4: Report

Summarize what was found across ClickUp and what was added to the vault.
