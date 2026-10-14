---
name: polinate
description: "Run a fleet health sweep: check lanes, restart stuck workers, verify capacity."
version: 0.1.0
author: Guilherme Henrique da Silva (dshgu), Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [fleet, health, lanes, replenish, cron]
    related_skills: [fleet-mode, ai-fleet-manager]
---

# polinate Skill

On-demand fleet replenishment sweep. Mirrors the cron job that keeps lanes alive and ready.

## When to Use

- User invokes `/polinate` to manually refresh the fleet
- After a crash, network blip, or suspected lane stall
- Before launching a large fan-out to ensure all lanes are healthy

Don't use for: routine checks if the cron is running and healthy (cron already covers it).

## Prerequisites

- Manager profile with configured lane profiles (lane-*)
- Access to Hermes dispatcher / kanban system
- Cron job `fleet-replenish-sweep` may exist but is not required

## How to Run

User types:

```
/polinate
```

Agent runs the sweep and reports.

## Procedure

1. **Enumerate configured lanes** from the manager profile's lane profiles (`lane-*`).
2. **For each lane**, check liveness:
   - Query dispatcher for lane status (idle, busy, stuck, dead)
   - If lane has a `kanban` heartbeat older than 5 min → mark **stale**
   - If lane process not found → mark **dead**
3. **Restart stale/dead lanes**:
   - Send restart signal via dispatcher (or `hermes profile restart <lane>`)
   - Wait up to 30 s for healthy heartbeat
4. **Verify capacity**:
   - Count idle workers per lane vs pending `ready` cards on the board
   - Flag lanes where `pending > idle * 2` as **under-capacity**
5. **Report summary**:
   - Healthy lanes (count + names)
   - Restarted lanes (count + names)
   - Under-capacity lanes (count + names + pending/idle ratio)
   - Any lanes that failed to restart (escalate)

## Quick Reference

| Step | Action |
|------|--------|
| 1 | List all `lane-*` profiles |
| 2 | Check heartbeat + process liveness |
| 3 | Restart stale/dead |
| 4 | Compare idle workers vs ready cards |
| 5 | Print summary table |

## Pitfalls

- Dispatcher API for lane liveness may not expose all fields — fall back to kanban task timestamps (`updated_at` on running cards assigned to that lane).
- Restarting a lane with a running card may orphan that card; the dispatcher should re-queue it, but verify.
- Under-capacity heuristic (`pending > idle * 2`) is a starting rule; tune per project.
- No auth needed for local lanes; bridge lanes (claude, antigravity) require their CLI auth to be fresh.

## Verification

- Every configured `lane-*` appears in the report with a status.
- No lane stays "stale" or "dead" after the sweep unless restart failed (explicitly listed).
- User sees a clear table and can act on under-capacity lanes immediately.