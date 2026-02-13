# Cowork Support Tickets

Create support tickets in ClickUp directly from your Claude Cowork session. When you're stuck, hit a bug, or need help — this plugin captures your session context and creates a detailed ticket so the right person can help you fast.

## How to Use

### Slash Command

Type `/submit-ticket` in any Cowork session.

### Natural Language

Just say things like:
- "I need help"
- "Something is broken"
- "I'm stuck"
- "Can someone help me with this?"

Claude will gather context from your current session, ask you for a title and urgency level, then create the ticket in ClickUp automatically.

## What Gets Captured

The plugin reviews your current conversation and automatically generates a ticket description that includes:
- What you were working on
- What went wrong
- Steps to reproduce (if applicable)
- Relevant error messages, files, or tool outputs
- What you've already tried

## Urgency Levels

| Level | When to use |
|-------|-------------|
| ASAP | Completely blocked, can't work, affects guests/customers |
| HIGH | Urgent, can work around it but needs attention today |
| Medium | Needs fixing but not time-sensitive |
| Low | Minor issue, fix when convenient |
| Weekly | Can wait for next weekly review |

## Where Tickets Go

All tickets are created in: **ClickUp → Ops Co → Cowork → Support Tickets**

The urgency level maps to the **Impact** custom field in ClickUp.

## Requirements

This plugin requires the ClickUp MCP connector to be active in your Cowork session. If you don't have it connected, ask your admin to set it up.

## Components

| Component | Name | Purpose |
|-----------|------|---------|
| Command | `/submit-ticket` | Main entry point for creating tickets |
| Skill | `support-context` | Guides context gathering and description generation |
