# CLAUDE.md

> **DIRECTIVE:** This file contains critical guidance that MUST be followed for all work.
> After context compaction, re-read this file and `.project` to restore phase context.

## SDLC Process (REQUIRED)

**Every request to spec, design, or build MUST follow the phased process. No exceptions.**

1. Read the agent persona from `agents/phase-X-*.md` (via AGENTS.md)
2. Adopt that persona's role and approach
3. Produce the deliverables for that phase
4. Update `.project`, `backlog.md`, and Asana
5. Hand off to next phase based on scope

**Phase paths:**

| Scope | Path |
|-------|------|
| Trivial | → 8 → Done |
| Small | 1 → 7 → 8 → Done |
| Medium | 1 → 4 → 6 → [6b, 6c] → 7 → 8 → 8b → Done |
| Large/New | 1 → 2 → 3 → 4 → 5 → 6 → [6b, 6c] → 7 → 8 → 8b → [9, 10] → Done |

**Code gates:** No implementation code until Phase 8. Tests must be RED before Phase 8 starts.

## Model Policy (CRITICAL)

| Phase | Model |
|-------|-------|
| 1 (Seed) | Opus (always) |
| 2-7 | Opus (default), Sonnet ok for trivial/small |
| 8 (Implementation) | Sonnet (default), Opus requires approval |
| 8b (Code Review) | Opus (default) |
| 9 (Refinement) | Opus (always) |
| 10 (Operations) | Opus (always) |

## Context Management (CRITICAL)

- `/clear` between context groups, not every phase (default: `grouped` strategy)
- Use `/next` to auto-determine when `/clear` is needed
- Use subagents for research (Phases 2-3) — never pollute main context
- After 2 failed corrections, `/clear` and restart with a better prompt
- Commit after each logical unit in Phase 8
- Never implement more than one function/endpoint per prompt

**Context groups:** Seed | Research (2,3) | Evaluation (4,5) | Design (6,[6b,6c]) | Test (7) | Implementation (8,8b) | Polish ([9,10])

**After `/clear` — "continue", "next step", or `/next`:**
1. Read `.project` → Phase Routing section to find current phase and status
2. Read the agent persona for that phase
3. Begin or resume the phase — no re-explanation needed

## Asana

Project: `sdlc-<directory-name>` | Sections: Backlog → Ready → In Progress → Done

## Git

Commit format: `phase <N>: <description>`

## Reference

See [AGENTS.md](./AGENTS.md) and linked [software-development-guidance.md](../coding-ai-config/software-development-guidance.md)
