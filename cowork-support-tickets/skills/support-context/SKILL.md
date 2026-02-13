---
name: support-context
description: >
  This skill should be used when the user says "I need help", "I'm stuck",
  "something is broken", "this isn't working", "I have an issue", "can someone
  help me", "submit a ticket", "create a ticket", "report a problem", or
  expresses frustration with a tool, workflow, or process. It provides guidance
  on gathering session context and creating well-structured support tickets.
version: 0.1.0
---

# Support Context Gathering

When a team member needs help, the goal is to capture enough context that the person resolving the ticket can understand and fix the issue without a back-and-forth conversation.

## What to Capture

Scan the current conversation for:

1. **Task context** — What was the user trying to accomplish? What tools or systems were they using?
2. **Failure point** — Where exactly did things go wrong? What was the expected vs actual outcome?
3. **Error details** — Any error messages, unexpected outputs, or tool failures in the session
4. **Files and artifacts** — Any file names, paths, documents, or data referenced in the conversation
5. **Steps taken** — What did the user already try? What troubleshooting was attempted?
6. **Environment** — Which department, system, or workflow area is affected?

## Writing the Description

Structure the ticket description in markdown:

```
## What I was doing
[1-2 sentences on the task/workflow]

## What went wrong
[Clear description of the issue]

## Steps to reproduce
1. [Step 1]
2. [Step 2]
3. [Where it breaks]

## Relevant details
- [Error messages, file names, tool outputs]
- [Any other context from the session]

## What I've already tried
- [Any troubleshooting attempts]
```

## Urgency Guidelines

Help the user pick the right urgency level:

- **ASAP** — Completely blocked, can't do any work, affects guests/customers immediately
- **HIGH** — Urgent issue, can work around it but needs attention today
- **Medium** — Needs fixing but not time-sensitive, no immediate customer impact
- **Low** — Minor annoyance, cosmetic issue, or small improvement needed
- **Weekly** — Can wait for the next weekly review, nice-to-have fix

## Reference

For the full IMPACT field mapping and ClickUp configuration, see `references/clickup-config.md`.
