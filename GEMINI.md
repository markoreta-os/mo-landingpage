# GEMINI.md

> **DIRECTIVE:** Read and follow [AGENTS.md](./AGENTS.md) for complete development guidance.
> After context compaction, re-read this file and `.project` to restore phase context.

## Required Reading

1. Read [AGENTS.md](./AGENTS.md) completely
2. Read [software-development-guidance.md](../coding-ai-config/software-development-guidance.md)
3. Check `config.yaml` for project-specific settings

## SDLC Process (REQUIRED)

When asked to spec, design, or build features:
1. Start with Phase 1 (Business Analyst persona)
2. Classify scope: trivial | small | medium | large | new_project
3. Follow the phase path based on scope
4. Adopt each agent persona from `~/projects/coding-ai-config/agents/phase-X-*.md`

## Phase Paths

| Scope | Path |
|-------|------|
| Trivial | → 8 → Done |
| Small | 1 → 7 → 8 → Done |
| Medium | 1 → 4 → 6 → [6b, 6c] → 7 → 8 → 8b → Done |
| Large/New | 1 → 2 → 3 → 4 → 5 → 6 → [6b, 6c] → 7 → 8 → 8b → [9, 10] → Done |

**Code gates:** No implementation code until Phase 8. Tests must be RED before Phase 8 starts.

## Context Management (CRITICAL)

- Clear context between context groups, not every phase (default: grouped strategy)
- Use separate searches for research (Phases 2-3) — don't pollute main context
- After 2 failed corrections, start a new session with a better prompt
- Commit after each logical unit in Phase 8
- Never implement more than one function/endpoint per prompt

**Context groups:** Seed | Research (2,3) | Evaluation (4,5) | Design (6,[6b,6c]) | Test (7) | Implementation (8,8b) | Polish ([9,10])

**After clearing context — "continue" or "next step":**
1. Read `.project` → Phase Routing section to find current phase and status
2. Read the agent persona for that phase
3. Begin or resume the phase — no re-explanation needed

## Key Files

`.project` | `config.yaml` | `seed.md` | `backlog.md` | `development-tasks.md`
