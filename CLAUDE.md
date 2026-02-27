# CLAUDE.md

> **DIRECTIVE:** This file contains critical guidance that MUST be followed for all work.
> After context compaction, re-read this file and `.project` to restore phase context.

---

## SDLC Process (REQUIRED)

**Every request to spec, design, or build MUST follow the phased process. No exceptions.**

**"spec" trigger:** Any prompt starting with "spec" MUST initiate the SDLC process starting at Phase 1 (Seed). Treat "spec ..." as equivalent to `/phase-1 ...`. When `multi_worker: true`, spec also auto-runs `/start-story` after Phase 1 (claim + worktree + Story Status row) — one command spins up a full workstream.

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

**Parallel processing (DEFAULT):** When multiple stories exist, batch non-conflicting stories and run them in parallel using worktree-isolated Task subagents. Each agent follows the full SDLC path for its scope. Sequential is the fallback, not the norm. See [software-development-guidance.md](.sdlc/software-development-guidance.md) § Parallel Backlog Processing.

**Multi-worker mode:** When `orchestration.multi_worker: true`, multiple workers (humans, AI sessions) advance different stories simultaneously. Each story gets a worktree for all phases. Use `/next STORY-ID` to advance a specific story, `/next --claim` to pick up the next unclaimed story. See [software-development-guidance.md](.sdlc/software-development-guidance.md) § Multi-Worker Protocol.

**Full phase details:** See [software-development-guidance.md](.sdlc/software-development-guidance.md)
**Agent personas:** See [AGENTS.md](./AGENTS.md)

---

## Model Policy (CRITICAL)

| Phase | Model | Effort |
|-------|-------|--------|
| 1 (Seed) | Opus (always) | high |
| 2-5 | Sonnet (default), Opus for large/complex | medium |
| 6 (Design) | Opus (default), Sonnet ok for small | high |
| 7 (Test Design) | Sonnet (default) | medium |
| 8 (Implementation) | Sonnet (default), Opus requires approval | medium |
| 8b (Code Review) | Sonnet (default) | medium |
| 9 (Refinement) | Opus (always) | high |
| 10 (Operations) | Opus (always) | high |

**Orchestrated phases (sub-agents run in parallel, orchestrator synthesizes):**
- **2 (Research):** market-scout (Sonnet, low), library-miner (Sonnet, low), field-reporter (Sonnet, low)
- **3 (Expansion):** pragmatist (Sonnet, medium), futurist (Sonnet, medium), optimizer (Sonnet, medium)
- **4 (Analysis):** technical (Sonnet, medium), business (Sonnet, medium), risk (Sonnet, medium)
- **8b (Code Review):** architect (Opus, high), skeptic (Sonnet, medium), simplifier (Sonnet, low), rule-reviewer (Sonnet, low), qa (Sonnet, low, frontend only), browser-tester (Sonnet, medium, frontend only)

**Subagents:** Use Sonnet with `effort: low` for research and review subagents. Use Sonnet with `effort: medium` for complex subagent tasks.

**Model Enforcement (HARD GATE — no exceptions):**
- Before starting ANY phase work, check: does your current model match the phase's required model?
- **Opus doing Sonnet work (e.g., Phase 8):** You MUST NOT write implementation code directly. Delegate ALL work to Task subagents with \`model: "sonnet"\`. The Opus agent orchestrates only — dispatches tasks, verifies results, commits code. This prevents ~15x cost overrun.
- **Sonnet doing Opus work (e.g., Phase 1, 9, 10):** STOP. Inform the user: "Phase X requires Opus. Please switch models." Do not attempt the work at reduced quality.
- **Orchestrated phases (2, 3, 4, 8b):** The orchestrator MUST specify \`model: "sonnet"\` (or \`model: "opus"\` for 8b-architect) on every Task subagent launch. Never inherit the orchestrator's model.
- **config.yaml override:** If \`models.opus_allowed: true\`, Opus may do Sonnet-default phases directly. This is the ONLY exception.
- Each agent persona file contains a Model Gate section — read it before starting work.

---

## Context Management (CRITICAL)

- `/clear` between context groups, not every phase (default: `grouped` strategy)
- Use `/next` to auto-determine when `/clear` is needed
- Use subagents for research (Phases 2-3) — never pollute main context
- After 2 failed corrections, `/clear` and restart with a better prompt
- Commit after each logical unit in Phase 8
- Never implement more than one function/endpoint per prompt

**Compaction:** When compacting, always preserve: modified files list, current phase & status, test commands, key file paths, and pending decisions.

**Context groups:** Seed | Research (2,3) | Evaluation (4,5) | Design (6,[6b,6c]) | Test (7) | Implementation (8,8b) | Polish ([9,10])

**After `/clear` — "continue", "next step", or `/next`:**
1. Read `.project` → Phase Routing section to find current phase and status
2. Read the agent persona for that phase
3. Begin or resume the phase — no re-explanation needed

---

## Asana Integration

**Project naming:** `sdlc-<directory-name>`

**Sections:** Backlog → Ready → In Progress → Done

**MCP Tools:** `asana_search_tasks`, `asana_create_task`, `asana_update_task`, `asana_get_projects`, `asana_get_tasks`

**Section moves:**
```bash
~/projects/coding-ai-config/scripts/asana-api.sh move <task_gid> <section_gid>
```

---

## Skills

| Skill | Purpose |
|-------|---------|
| `/new-project` | Full project setup |
| `/phase-1` | Concept & Seed |
| `/phase-2` | Research Coordinator |
| `/phase-3` | Expansion Coordinator |
| `/phase-4` | Analysis Coordinator |
| `/phase-5` | Pragmatic Executive |
| `/phase-6` | Systems Architect |
| `/phase-6b` | Security Reviewer |
| `/phase-6c` | UX Review |
| `/phase-7` | Test Design |
| `/phase-8` | Implementation |
| `/phase-8b` | Code Review |
| `/phase-9` | Refinement |
| `/phase-10` | Operational Resilience |
| `/next` | Advance to next phase (auto/confirm/gate) |
| `/sync-backlog` | Asana → backlog.md |
| `/start-story` | Begin story work |
| `/complete-story` | Mark story done |
| `/council` | LLM Council review |

---

## Git

**Commit format:** `phase <N>: <description>`

**Feature updates:** `phase <N>: [feature-name] <description>`

---

## Tech Stack (Default)

- **Backend:** Python 3.12+, FastAPI, PostgreSQL, SQLAlchemy 2.0, Alembic, Pydantic, PyJWT, pwdlib, pytest
- **Frontend:** React 19, Vite, TypeScript, Tailwind v4, TanStack Query v5, Zustand v5, Zod v4
- **Infra:** Docker, Docker Compose

---

## Writing Rules

- Bullets over prose
- Tables for structured data
- No filler words
- Max 3 nesting levels

---

## Reference

- [software-development-guidance.md](.sdlc/software-development-guidance.md) — Full phase details, gates, hooks, lessons learned
- [AGENTS.md](./AGENTS.md) — Complete agent guidance with examples
- [templates/](.sdlc/templates/) — File and config templates
