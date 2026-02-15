---
description: Run all ingestion sources to bring the vault up to date
allowed-tools: Read, Write, Glob, Grep, Bash(git:*), AskUserQuestion
argument-hint: [optional: specific sources to include/exclude]
---

Run the full daily sweep — scan all ingestion sources (Drive, ClickUp, Meetings, Cowork), deduplicate, run discernment, and process accepted content into the vault.

## Step 1: Load the Daily Sweep Skill

Read the daily-sweep skill for the full orchestration process.

## Step 2: Pre-Flight

Check connector status and read sweep state files to determine what's changed since the last run.

## Step 3: Run All Sources

Execute each ingestion source in order:
1. Google Drive
2. ClickUp
3. Meeting Transcripts
4. Cowork Artifacts

Collect all new items into a unified queue.

## Step 4: Batch Discernment

Deduplicate across sources, then run batch discernment on all items.

## Step 5: Process and Report

Atomize accepted content, create fleeting notes for borderline items, and produce the consolidated sweep report.
