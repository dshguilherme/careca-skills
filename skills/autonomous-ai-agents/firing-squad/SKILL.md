---
name: firing-squad
description: Parallel Thinker+Spotter critique with Queen arbitration from third model family.
version: 0.1.0
author: Guilherme Henrique da Silva (dshgu), Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [critique, thinker, spotter, queen, arbitration, multi-model]
    related_skills: [fleet-mode, one-shot, interview]
---

# firing-squad Skill

Runs a structured three-stage critique: (1) Thinker + Spotter from different model families independently critique a deliverable, (2) agreements extracted, (3) disagreements escalated to a Queen from a third family for final verdict.

## When to Use

- User invokes `/firing-squad <deliverable-path-or-content>` for design reviews, article critiques, code reviews, architecture validation
- Any artifact needing multi-perspective critique with binding resolution

Don't use for: trivial checks, single-perspective feedback, or when speed > thoroughness.

## Prerequisites

- Three lane profiles from **different model families** configured:
  - Thinker lane (high abstraction, long context) — e.g., `lane-opus`, `lane-gpt4o`
  - Spotter lane (vision + aesthetic) — e.g., `lane-gemini-vision`, `lane-gpt4o-vision`
  - Queen lane (flagship, arbitration) — e.g., `lane-opus`, `lane-nemotron-ultra`
- Families must differ: no two roles from the same provider family.

## How to Run

User types:

```
/firing-squad <path-to-file | URL | pasted content> [--focus design|content|formulation|all]
```

Agent runs the procedure below.

## Procedure

1. **Parse input** — read file/URL/content; note focus flag (default: all).
2. **Select lanes** — pick Thinker, Spotter, Queen from three distinct families. Present options if ambiguous; user confirms.
3. **Stage 1: Parallel critique** — spawn two independent critiques:
   - **Thinker** — deep structural/logical critique: architecture, math, reasoning, completeness, edge cases
   - **Spotter** — visual/aesthetic/presentation critique: formatting, diagrams, readability, UX, style consistency
   - Both receive identical artifact + focus scope. Run in parallel.
4. **Stage 2: Agreement extraction** — compare both critiques:
   - **Agreed points** — identical or compatible findings → write to `agreed.md`
   - **Disagreements** — conflicting or mutually exclusive findings → write to `disputed.md`
   - **Unique points** — valid points only one raised → write to `unique-thinker.md`, `unique-spotter.md`
5. **Stage 3: Queen arbitration** — present `disputed.md` + context to Queen:
   - Queen rules on each dispute: accepts Thinker, accepts Spotter, synthesizes new position, or rejects both
   - Output: `final-verdict.md` with ruling on every dispute + reasoning
6. **Assemble final critique** — merge `agreed.md` + `final-verdict.md` + unique points → `firing-squad-report.md`
7. **Deliver report** — show summary; attach full report.

## Quick Reference

| Stage | Actors | Output |
|-------|--------|--------|
| 1 | Thinker + Spotter (parallel) | `critique-thinker.md`, `critique-spotter.md` |
| 2 | Manager (diff) | `agreed.md`, `disputed.md`, `unique-*.md` |
| 3 | Queen (third family) | `final-verdict.md` |
| 4 | Manager (merge) | `firing-squad-report.md` |

## Model Family Diversity Rule

| Role | Example Families | Must Differ From |
|------|------------------|------------------|
| Thinker | Anthropic, OpenAI, Google, NVIDIA | Spotter, Queen |
| Spotter | Google (vision), OpenAI (vision), Anthropic | Thinker, Queen |
| Queen | Anthropic (Opus), NVIDIA (Nemotron), OpenAI (o1) | Thinker, Spotter |

If <3 families available, skill blocks with `needs_input`.

## Pitfalls

- Lane profiles must exist and be authenticated. Bridge lanes (claude.exe, antigravity) count as their provider family.
- Vision required for Spotter — text-only lanes cannot fill Spotter role.
- Queen must be a flagship-tier model; mid-tier models lack arbitration reliability.
- Large artifacts: chunk and run per section, then merge.
- No kanban board created — ephemeral parallel execution.

## Verification

- Three distinct model families used (logged in report header).
- `firing-squad-report.md` exists with: agreed points, disputed points with Queen rulings, unique points from each.
- User can trace every finding to Thinker, Spotter, or Queen.