---
description: Sync Cowork session artifacts to the vault
allowed-tools: Read, Write, Glob, Grep, Bash(git:*), AskUserQuestion
argument-hint: [optional: specific file or session to sync]
---

Scan the Cowork workspace folder for documents and artifacts created during Claude sessions that should be added to the Company Brain vault.

## Step 1: Load Skills

Read the cowork-sync skill and the discernment skill.

## Step 2: Scan Workspace

List all files in the Cowork workspace folder. Compare against the sync history in the vault to find new files.

## Step 3: Evaluate and Process

For each new file, read its content and run through discernment. Process accepted files through the atomizer.

## Step 4: Report

Summarize what was found and what was added to the vault.
