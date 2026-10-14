---
name: your-words
description: Append intent-restatement instruction to the user's prompt.
version: 0.1.2
author: Guilherme Henrique da Silva (dshgu), Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [communication, clarification, intent-check]
    related_skills: []
---

# your-words Skill

When the user prefixes a request with `/your-words`, the agent treats the rest of the message as the task and **first** replies with a restatement of the user's intent and desired outcome in the agent's own words. No confirmation wait — the restatement is the first response, then the agent proceeds to the task.

## When to Use

- User types `/your-words <request>` to force an intent playback before work
- Any task where a quick alignment check adds value

## Behavior

User input:
```
/your-words <actual request>
```

## Expected outcome ##

Agent's **first response** (before any tool calls):
> What the agent is sure what the intent of the user is
> What the agent is not sure about the prompt and can be clarified
> What the agent thinks is a success for the task

Then the agent continues executing the actual request in the same turn.

## Verification

- The first assistant message after `/your-words` contains the two restatement sections.
- Tool calls may follow in the same turn after the restatement.

## Pitfalls

- Do not elaborate, advise, or propose alternatives in the restatement.
- If the request is ambiguous, the restatement will be wrong — the user corrects in the next turn.