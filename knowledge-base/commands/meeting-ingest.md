---
description: Extract knowledge from meeting transcripts
allowed-tools: Read, Write, Glob, Grep, Bash(git:*), AskUserQuestion
argument-hint: [optional: specific meeting or date range]
---

Find meeting transcripts (Google Meet, Zoom) and extract decisions, context, and insights into the Company Brain vault.

## Step 1: Load Skills

Read the meeting-ingest skill and the discernment skill.

## Step 2: Find Transcripts

Search Google Drive for meeting transcripts not yet processed. If the user specified a meeting or date range, narrow the search.

## Step 3: Extract and Evaluate

For each transcript, extract the structured content (decisions, rationale, action items, insights). Run through discernment.

## Step 4: Process and Report

Atomize accepted content, focusing on decisions as decision records and insights as permanent notes. Summarize what was extracted.
