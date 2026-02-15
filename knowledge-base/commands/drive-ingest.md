---
description: Scan Google Drive for documents not yet in the vault
allowed-tools: Read, Write, Glob, Grep, Bash(git:*), AskUserQuestion
argument-hint: [optional: specific folder or search terms]
---

Scan Google Drive (My Drive, Shared Drive, and shared-with-me) for documents that haven't been added to the Company Brain vault yet.

## Step 1: Load Skills

Read the drive-ingest skill and the discernment skill to understand the full pipeline.

## Step 2: Build Vault Index

Check the vault's `content/notes/source/` directory for existing source notes to know what's already been ingested.

## Step 3: Scan Google Drive

Use the Google Drive MCP tools to search for recent or unprocessed documents across all three scopes:
- Shared Drive (Bolt Farm)
- My Drive
- Shared with me

If the user provided a specific folder or search terms, narrow the scan accordingly.

## Step 4: Deduplicate and Evaluate

Compare findings against the vault index. For new documents, run each through the discernment skill to decide: atomize, fleeting, or skip.

## Step 5: Process and Report

Process accepted documents through the atomizer pipeline and present a summary of what was found, what was added, and what was skipped.
