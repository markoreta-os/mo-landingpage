# GEMINI.md

> **DIRECTIVE:** This file contains critical guidance that MUST be followed for all work.
> After context compaction, re-read this file and `.project` to restore phase context.
> Read and follow [AGENTS.md](./AGENTS.md) for complete development guidance.

---

## SDLC Process (REQUIRED)

**Every request to spec, design, or build MUST follow the phased process. No exceptions.**

**"spec" trigger:** Any prompt starting with "spec" MUST initiate the SDLC process starting at Phase 1 (Seed).

1. Start with Phase 1 (Business Analyst persona)
2. Classify scope: trivial | small | medium | large | new_project
3. Follow the phase path based on scope
4. Adopt each agent persona from `.sdlc/agents/phase-X-*.md`
5. Produce the deliverables for that phase
6. Update `.project`, `backlog.md`, and Asana
7. Hand off to next phase based on scope

**Phase paths:**

| Scope | Path |
|-------|------|
| Trivial | → 8 → Done |
| Small | 1 → 7 → 8 → Done |
| Medium | 1 → 4 → 6 → [6b, 6c] → 7 → 8 → 8b → Done |
| Large/New | 1 → 2 → 3 → 4 → 5 → 6 → [6b, 6c] → 7 → 8 → 8b → [9, 10] → Done |

**Code gates:** No implementation code until Phase 8. Tests must be RED before Phase 8 starts.

---

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

---

## Skills

All phases and orchestration tasks are available as skills. Use `activate_skill` to load a phase's instructions.

| Skill | Phase / Purpose |
|-------|-----------------|
| `phase-1` | Concept & Seed |
| `phase-2` | Research Coordinator |
| `phase-3` | Expansion Coordinator |
| `phase-4` | Analysis Coordinator |
| `phase-5` | Pragmatic Executive |
| `phase-6` | Systems Architect |
| `phase-6b` | Security Reviewer |
| `phase-6c` | UX Strategist |
| `phase-7` | Principal Developer |
| `phase-8` | Senior Developer |
| `phase-8b` | Code Review Orchestrator |
| `phase-9` | Distinguished Engineer |
| `phase-10` | Operational Resilience |
| `next` | Advance to next phase |
| `council` | Invoke LLM Council |

---

## Key Files

`.project` | `config.yaml` | `seed.md` | `backlog.md` | `development-tasks.md`

---

## Reference

- [software-development-guidance.md](.sdlc/software-development-guidance.md) — Full phase details, gates, hooks, lessons learned
- [AGENTS.md](./AGENTS.md) — Complete agent guidance with examples
- [templates/](.sdlc/templates/) — File and config templates
