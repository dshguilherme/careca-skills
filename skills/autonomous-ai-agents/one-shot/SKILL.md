---
name: one-shot
description: Interview-driven single-agent task execution with model/provider selection.
version: 0.1.0
author: Guilherme Henrique da Silva (dshgu), Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [orchestra, interview, single-agent, model-selection]
    related_skills: [interview, dev-plan, fleet-mode]
---

# one-shot Skill

Runs a single task through orchestra's interview skill to clarify the spec, then executes it with a user-selected model/provider in a goal loop (retries allowed). No kanban, no fleet fan-out.

## When to Use

- User invokes `/one-shot <task brief>` for a focused task that doesn't need decomposition
- When you want interview-driven clarity but single-agent execution
- Quick prototypes, spikes, research questions, code tasks with clear boundaries

Don't use for: multi-step projects, tasks needing parallel workers, or anything requiring reviewer gates.

## Prerequisites

- `interview` skill installed (from orchestra)
- Access to model/provider list for selection step

## How to Run

User types:

```
/one-shot <initial task brief>
```

Agent runs the procedure below.

## Procedure

1. **Parse initial brief** from the message after `/one-shot`.
2. **Run interview skill** — interactive Q&A to sharpen the task into a precise, testable spec. The interview skill asks one question at a time until the spec is solid.
3. **Present model/provider options** — list available profiles (e.g., `lane-claude`, `lane-gemini`, `lane-mistral`, `manager`, `researcher`, etc.) and ask the user to pick one.
4. **Execute in goal loop** — run the clarified task with the chosen model/profile using `goal_mode: true` (retries until the judge agrees it's complete or budget exhausted).
5. **Return result** — final output, artifacts, and any follow-up questions.

## Quick Reference

| Step | Action |
|------|--------|
| 1 | Parse brief |
| 2 | Interview → precise spec |
| 3 | Ask model/provider |
| 4 | Goal-loop execute |
| 5 | Deliver result |

## Pitfalls

- Interview skill must be available on the profile; if not, fall back to a manual clarification pass.
- Model/provider list is dynamic — read from current profile config, not hardcoded.
- Goal loop budget (default 20 turns) caps retries; user can extend by re-invoking.
- No kanban board created — no persistence across sessions beyond the chat history.
- If the task turns out to need decomposition, user should switch to `/fleet-mode`.

## Verification

- Interview completes with a written spec the user agrees covers the task.
- Model/provider selection recorded.
- Goal loop terminates with `schema_valid: true` or explicit user stop.
- Final answer delivered in the chat (no board cards to check).