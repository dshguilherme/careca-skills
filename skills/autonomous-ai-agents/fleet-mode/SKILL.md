---
name: fleet-mode
description: Start a project with orchestra methodology and fleet mode, asking for bee roles.
version: 0.1.0
author: Guilherme Henrique da Silva (dshgu), Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [orchestra, fleet, project-init, bee-roles]
    related_skills: [your-words, dev-plan, research]
---

# fleet-mode Skill

Initializes a project using the orchestra methodology with fleet mode. Prompts for bee role requirements upfront.

## When to Use

- User invokes `/fleet-mode` to start a new project with the fleet/orchestra workflow
- Any multi-agent project needing role classification before decomposition

Don't use for: single-agent tasks, quick lookups, or projects already planned.

## Prerequisites

- Hermes profile with fleet lanes configured (lane-*)
- Kanban board for the project (or one will be created)
- `orchestra` skills available (dev-plan, research, spec-interview, etc.)

## How to Run

User types:

```
/fleet-mode <project name or brief>
```

Agent runs the procedure below.

## Procedure

1. **Parse project context** from the user message after `/fleet-mode`.
2. **Ask for bee roles needed** — present the classification and let the user pick or describe:
   - **Workers** — simple coding, tool calls, file ops, short context
   - **Thinkers** — high abstraction, long context, architecture, math, research synthesis
   - **Spotters** — vision input, aesthetic critique, UI/fig review, diagram validation
   - **Queens** — flagship models (Opus, GPT-4o, Nemotron 3 Ultra) for hardest decisions
   - **Drones** — orchestration-capable models that spawn/manage other agents
3. **Create or select kanban board** for the project (tenant = project slug).
4. **Initialize orchestra flow**: run `dev-plan` → `spec-interview` (if software) or `research` + `plan` (if paper/article) → `design-vault` → `build` → `cite-check` → `polish`.
5. **Create starter cards** on the board with assignees mapped to the bee roles chosen.
6. **Report board URL and first card IDs** to the user.

## Quick Reference

| Step | Action |
|------|--------|
| 1 | Parse project brief |
| 2 | Ask bee roles (workers/thinkers/spotters/queens/drones) |
| 3 | Create/select kanban board |
| 4 | Launch orchestra pipeline (dev-plan or research→plan) |
| 5 | Spawn role-mapped starter cards |
| 6 | Hand off board + card IDs |

## Pitfalls

- Bee role classification is a **work in progress** — lanes/models not yet tagged in config. The skill prompts the user; mapping to actual lane profiles is manual for now.
- Don't assume every project needs all five roles. Most need workers + one thinker + one queen.
- Orchestra skills (`dev-plan`, `research`, `build`, etc.) must be installed on the profile.
- Kanban board creation requires write access to the profile's kanban home.

## Verification

- Kanban board exists with project tenant.
- At least one card in `todo` or `ready` with an assignee matching a requested bee role.
- User confirms the board and first cards look correct.