# Lane-to-Role Mapping for Firing Squad

Maps the user's actual lane profiles to Thinker / Spotter / Queen roles.

## Current Lane Roster (from ai-fleet-manager)

| Lane | Provider | Model | Family | Capabilities |
|---|---|---|---|---|
| `lane-claude` | bridge → claude.exe | Opus-class | Anthropic | Reasoning, code, review, vision (verified) |
| `lane-antigravity` | bridge → agy -p | Gemini Pro-class chain | Google | Agentic, vision (verified), reasoning |
| `lane-kimi` | Moonshot | kimi-k3 (1M ctx) | Moonshot | Complex reasoning, critic, vision (unverified) |
| `lane-freeai` | freeaiapi | claude-sonnet-5 / claude-opus-5.5 | Anthropic | Complex/critic/escalation, vision (verified) |
| `lane-vibe` | bridge → vibe -p | Mistral Large 4 / GLM-5.3 | Mistral / Z.ai | QC, coding, vision (unverified) |
| `lane-nvidia` | NVIDIA | nemotron-3-ultra-550b | NVIDIA | Free reasoning, large context, vision (unverified) |
| `lane-nous-laguna` | Nous | solar-mini4:free | Upstage | Free, 524k ctx, tools, text only |
| `lane-mistral` | Mistral API | mistral-large-4 | Mistral | Paid API, text only |

## Role Assignment Rules

**Thinker** (high abstraction, long context, architecture/math):
- Primary: `lane-kimi` (kimi-k3, 1M ctx, paid)
- Secondary: `lane-freeai` (claude-sonnet-5, free pool 100k/day)
- Tertiary: `lane-claude` (Opus-class, subscription)

**Spotter** (vision + aesthetic critique):
- Primary: `lane-claude` (vision verified via bridge)
- Secondary: `lane-antigravity` (vision verified, Gemini Pro-class)
- Tertiary: `lane-freeai` (vision verified, claude-sonnet-5/opus-5.5)

**Queen** (flagship arbitration, different family from Thinker+Spotter):
- If Thinker=kimi, Spotter=claude → Queen=antigravity (Google)
- If Thinker=freeai, Spotter=antigravity → Queen=claude (Anthropic)
- If Thinker=kimi, Spotter=freeai → Queen=antigravity (Google)
- Fallback: `lane-nvidia` (Nemotron 3 Ultra, free, different vendor)

## Diversity Enforcement

- Thinker, Spotter, Queen MUST be from three different model families (Anthropic, Google, Moonshot, NVIDIA, Mistral, Z.ai)
- Bridge lanes count as their provider family
- If <3 families available with required capabilities, skill blocks with `needs_input`

## Vision Verification Status

| Lane | Vision Verified? | Notes |
|---|---|---|
| `lane-claude` | Yes | `claude.exe -p` with image input works |
| `lane-antigravity` | Yes | `agy -p` with image input works |
| `lane-freeai` | Yes | `claude-sonnet-5` / `opus-5.5` vision works |
| `lane-kimi` | Unknown | Test before assigning as Spotter |
| `lane-vibe` | Unknown | Test before assigning as Spotter |
| `lane-nvidia` | Unknown | Test before assigning as Spotter |

## Quick Selection Cheat Sheet

```
# Typical balanced squad (3 families: Moonshot, Anthropic, Google)
Thinker: lane-kimi
Spotter: lane-claude
Queen:   lane-antigravity

# When kimi unavailable (3 families: Anthropic, Google, NVIDIA)
Thinker: lane-freeai
Spotter: lane-antigravity
Queen:   lane-claude

# When freeai pool exhausted (3 families: Moonshot, Google, NVIDIA)
Thinker: lane-kimi
Spotter: lane-antigravity
Queen:   lane-nvidia
```