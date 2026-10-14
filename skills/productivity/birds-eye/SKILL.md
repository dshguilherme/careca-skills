---
name: birds-eye
description: Use for /birds-eye. Brief projects; act after all answers.
version: 1.0.0
author: Guilherme Henrique da Silva (dshguilherme), Hermes Agent
license: MIT
platforms: [windows]
metadata:
  hermes:
    tags: [briefing, fleet, decisions, status, manager]
    related_skills: [ai-fleet-manager, user-briefing, sprint-qc]
---

# /birds-eye — project overview and decision queue

## When to Use

- The user types `/birds-eye`.
- He asks for a bird's-eye view, an overview of all projects, or "what needs
  my attention".
- He says "one by one" / "next item" after such a brief (review mode, below).

## What the user wants (intent, in the manager's words)

Dr. Guilherme runs several projects at the same time through an AI fleet. He
cannot read every board, log and card. A few times a day he wants to step back
and see the whole picture in one screen:

- **Where each project stands**: progress, what moved since the last look, what
  runs now.
- **What happens next** without him: the next steps the fleet takes by itself.
- **What only he can decide**: a short, numbered queue of decisions, each with
  the facts he needs and a recommended option.

Then he works through that queue **one item at a time**, at his own pace. The
session is a meeting, not a work sprint: he is the decision-maker, the manager
is the chief of staff. He wants to keep control of what changes. So during the
review the manager **collects questions and answers; it does not act**. Only
when every open item has an answer does the manager execute all of them in one
batch and report the result.

What he hopes to get from this:

1. **No bottleneck on him.** Everything the fleet can decide or fix by itself
   stays out of his queue. Only real decisions reach him.
2. **No surprises.** No change lands while he is still thinking about the
   related item, and no action rests on a half-answered question.
3. **Trustworthy numbers.** Every figure in the brief is read fresh from the
   source at brief time, not remembered from earlier in the session.
4. **Low reading cost.** Plain ASD-STE100 English, tables, short lists. He
   reads it quickly and answers by number.

## Procedure

### 1. Gather (read-only, batch the calls)

Read fresh, at brief time:

- Board state per project: `hermes kanban boards list`, then counts and the
  open/blocked/running cards per board (or read `kanban/boards/*/kanban.db`
  read-only), plus cards completed since the last brief.
- `C:/Users/dshgu/projects/.apm/manager-log.md` tail (what actually happened).
- `C:/Users/dshgu/projects/.apm/HANDOVER.md` (per-project next steps; volatile).
- `.apm/progress.json` (per-project %) — refresh milestones that moved.
- `.apm/paid-usage.json` and live balance endpoints for any paid account you
  will mention (a remembered balance is often wrong).
- Night queue: `.apm/heavy-runner.log`, `.apm/queue/`, `logs/*.log` (UTF-16),
  `python .apm/sim_stats.py`.
- Blocked triage: `.apm/blocked-triage.json` (AUTO / MANAGER / USER).
- Cron health: `hermes -p manager cron list` (model, last run, status).

Verify every claim you are about to make. If a deliverable is involved (a CV,
a PDF, a results DB), check it against its hard rules before you call it ready.

### 2. Brief (one message)

Write in ASD-STE100 (see `user-briefing`). Structure:

1. One-line headline: how many decisions he owes.
2. **Projects table**: Project | Progress % | State now | Next steps. One row
   per board. Flag a project that has been idle.
3. **Needs your attention**: a numbered list. Each item = the facts, the effect
   of each option, and the recommended option FIRST. Only items that need his
   judgement, credentials, money, hardware or taste.
4. **Found and handled by me**: problems found during the gather that are
   within the manager's authority. Report them; do not make them his decisions.
5. **Fleet health**: one or two lines (bee one-shot rate, blocked count).

### 3. Review mode — collect, do not act

When he says "one by one" / "next item":

- Present the next item only, with fresh numbers for that item.
- Record his answer. **Do not change anything yet** — no config edits, no
  cards, no commits, no restarts — even when the answer looks clear.
- Read-only checks that make an item precise are fine (a balance probe, a
  catalog lookup). Report a new finding as a new numbered issue.
- If his answer raises a new question, add it to the open list.
- Keep a running list: answered items (with his answer) and open items. Show
  it when he asks, and at the end of every review step.
- Exceptions: a deliverable he explicitly asks to see (send the file with
  `MEDIA:`), and a safety issue that costs money or damages data in the next
  minutes — stop it, then report it.

### 4. Execute — only when every item is answered

1. Restate the answers as a short action list.
2. Execute them in one batch (parallel where independent).
3. Verify each action (smoke test, rebuild, query) — no "done" without
   evidence.
4. Log one line per decision in `manager-log.md`; update skills, `lanes.json`
   or the fact base where a decision is a lasting rule.
5. Report: what changed, what is verified, what is still open (and why).

Items he defers ("tomorrow", "later") go into the log and HANDOVER as open,
not into action.

## Pitfalls (seen in the first session, 2026-10-13/14)

- **Acting mid-review.** The manager edited configs, created lanes and cards
  while the user was still walking the queue. He corrected it: list the issues
  first, act after all information is given.
- **Stale facts in the brief.** A cron model was reported as DeepSeek after
  another session had moved it; a balance was quoted from memory. Re-read the
  source for every item.
- **A model name is not a price.** Nemotron Ultra is free on NVIDIA NIM and
  OpenRouter (`:free`) but paid on Nous. Check the live catalog before a
  routing answer.
- **Burying his decisions under fleet chatter.** Night-queue fixes, triage and
  lane swaps are the manager's job. Mention them under "handled by me".
- **Asking a question he cannot answer from the brief.** Give the numbers
  (balance, % used, reset date, cost per card) with the question.
- **Unclear deliverable location.** When he asks where to see something, copy
  it to a plain folder (e.g. `Downloads/<topic>-<date>/`) and send it inline.
