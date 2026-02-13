---
description: Submit a support ticket from your current context
allowed-tools: Read, Grep, Glob, AskUserQuestion, mcp__6dd7c83b-3056-455b-9913-d02083fd3760__clickup_create_task
---

A team member needs help. Gather context from the current session and create a support ticket in ClickUp.

## Step 1: Gather Context

Before asking the user anything, silently review the current conversation to understand:
- What the user was working on
- What went wrong or where they got stuck
- Any error messages, file names, or tool outputs that are relevant
- Which department or area this relates to

## Step 2: Ask for Ticket Details

Use AskUserQuestion to ask the user TWO things:

**Question 1: Title**
Ask: "What's a short title for this ticket?"
Provide 2-3 suggested titles based on the session context as options.

**Question 2: Urgency**
Ask: "How urgent is this?"
Options:
- ASAP — I'm completely blocked and can't work
- HIGH — This is urgent but I can work around it temporarily
- Medium — Needs attention but not time-sensitive
- Low — Minor issue, fix when convenient
- Weekly — Can wait until the next weekly review

## Step 3: Generate Description

Write a rich, detailed ticket description that includes:

1. **What they were doing** — summarize the task/workflow from session context
2. **What went wrong** — describe the issue, error, or blocker
3. **Steps to reproduce** (if applicable) — what led to the problem
4. **Relevant details** — file names, tool outputs, error messages, screenshots referenced
5. **Session context** — any other useful information from the conversation

The description should be detailed enough that someone reading it can understand and resolve the issue WITHOUT needing to talk to the submitter. This is the key value — automate the "explain what happened" part.

## Step 4: Create the ClickUp Task

Create the task using the ClickUp MCP tool with these parameters:

- **list_id**: "901113151846"
- **name**: The title from Step 2
- **markdown_description**: The generated description from Step 3
- **custom_fields**: Set the Impact field based on urgency:

Map the urgency selection to the Impact custom field (id: `aeca7983-76ef-46ec-bea6-d17c77791046`):

| Urgency | Impact option ID |
|---------|-----------------|
| ASAP | `48f4660e-d1bb-417c-a375-68528be5fad7` |
| HIGH | `ed2060b3-55c8-4763-b310-7e0b2ceeb4cc` |
| Medium | `61cbe203-5be4-44d6-8c67-201373fb27e9` |
| Low | `8758ac03-cef5-4223-a9ed-a5eee0655587` |
| Weekly | `a440e011-d4e8-4375-a1f9-f1a62961c651` |

Example custom_fields parameter:
```json
[{"id": "aeca7983-76ef-46ec-bea6-d17c77791046", "value": "<option_id_from_table_above>"}]
```

## Step 5: Confirm

Tell the user the ticket was created. Include:
- The ticket title
- The urgency level set
- A link to the ticket in ClickUp (from the task creation response)
- A brief summary of what was captured in the description

Keep it short and reassuring — "Got it, ticket created. Someone will pick this up."
