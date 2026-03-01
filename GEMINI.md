# GEMINI.md

> **DIRECTIVE:** This file contains critical guidance that MUST be followed for all work.
> After context compaction, re-read this file and `.project` to restore phase context.
> Read and follow [AGENTS.md](./AGENTS.md) for complete development guidance.

---

## SDLC Process (REQUIRED)

**Every request to spec, design, or build MUST follow the phased process. No exceptions.**

**"spec" trigger:** Any prompt starting with "spec" MUST initiate the SDLC process starting at Phase 1 (Seed). When `multi_worker: true`, spec also auto-runs the start-story protocol after Phase 1 (claim + worktree) — one command spins up a full workstream.

1. Start with Phase 1 (Business Analyst persona)
2. Classify scope: trivial | small | medium | large | new_project
3. Follow the phase path based on scope
4. Adopt each agent persona from `.sdlc/agents/phase-X-*.md`
5. Produce the deliverables for that phase
6. Update `.project`, `backlog.md`, and Asana
7. **STOP after each phase** — check the advance category before continuing (see below)

**Phase paths:**

| Scope | Path |
|-------|------|
| Trivial | → 8 → Done |
| Small | 1 → 7 → 8 → Done |
| Medium | 1 → 4 → 6 → [6b, 6c] → 7 → 8 → 8b → Done |
| Large/New | 1 → 2 → 3 → 4 → 5 → 6 → [6b, 6c] → 7 → 8 → 8b → [9, 10] → Done |

**Code gates:** No implementation code until Phase 8. Tests must be RED before Phase 8 starts.

**Phase boundary behavior (CRITICAL — do NOT auto-advance):**
Each agent persona has an `advance` field. After completing a phase, you MUST check it:

| Advance | Behavior | Phases |
|---------|----------|--------|
| **gate** | **STOP.** Show deliverables. Wait for explicit user approval before doing anything else. Do NOT start the next phase. | 1, 8 |
| **confirm** | **STOP.** Show summary. Ask "Proceed to Phase X?" Wait for yes/no. | 2, 3, 4, 5, 6, 7, 9, 10 |
| **auto** | Proceed to the next **phase** immediately, no user input needed. | 6b, 6c, 8b |

**You must STOP and wait after every phase unless the advance category is `auto`.** Never chain phases together. The user decides when to continue.

**Story switching (CRITICAL — NEVER auto-switch stories, even with auto-accept):**
`auto` advance means auto-advance to the next **phase within the same story**. It NEVER means switch to a different story/ticket. When a story is complete (final phase reached, Asana/backlog updated):
1. Output the completion summary
2. **STOP. END YOUR RESPONSE. Do NOT claim, start, or begin the next story.**
3. The "Next:" suggestions in completion messages are for the **user to read and type** — they are NOT instructions for you to execute
4. Wait for the user to explicitly tell you which story to work on next
5. This rule applies even if there are Ready stories in the backlog — you do NOT pick them up automatically
6. This rule applies even if auto-accept is enabled — auto-accept controls phase transitions, NEVER story transitions

**Parallel processing (DEFAULT):** When multiple stories exist, batch non-conflicting stories and run them in parallel. Sequential is the fallback, not the norm. See [software-development-guidance.md](.sdlc/software-development-guidance.md) § Parallel Backlog Processing.

**Multi-worker mode:** When `orchestration.multi_worker: true`, multiple workers (humans, AI sessions) advance different stories simultaneously. Each story gets a worktree for all phases. Use `next STORY-ID` to advance a specific story, `next --claim` to pick up the next unclaimed story. See [software-development-guidance.md](.sdlc/software-development-guidance.md) § Multi-Worker Protocol.

**Deliverable paths (multi-worker — ALL phases):** When working on a story in multi-worker mode, ALL deliverables (\`seed.md\`, \`test-design.md\`, \`feature-spec.md\`, \`architecture.md\`, etc.) MUST be written to the **story's working directory** (worktree root), NEVER the main project root. Writing to the project root overwrites other stories' work. If you're in a worktree, write files relative to your current directory.

**Full phase details:** See [software-development-guidance.md](.sdlc/software-development-guidance.md)
**Agent personas:** See [AGENTS.md](./AGENTS.md)

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
| `spec` | SDLC entry point — start Phase 1 for new work |
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
| `sync-backlog` | Asana → backlog.md |
| `start-story` | Begin story work (claim + worktree) |
| `complete-story` | Mark story done (merge + cleanup) |
| `council` | Invoke LLM Council |

---

## Model Policy (CRITICAL)

| Phase | Model | Effort |
|-------|-------|--------|
| 1 (Seed) | Pro (always) | high |
| 2-5 | Flash (default), Pro for large/complex | medium |
| 6 (Design) | Pro (default), Flash ok for small | high |
| 7 (Test Design) | Flash (default) | medium |
| 8 (Implementation) | Flash (default) | medium |
| 8b (Code Review) | Flash (default) | medium |
| 9 (Refinement) | Pro (always) | high |
| 10 (Operations) | Pro (always) | high |

**Model Enforcement (HARD GATE — no exceptions):**
- Before starting ANY phase, check: does your current model match the phase's required model?
- **Pro doing Flash work (e.g., Phase 8):** Delegate ALL work to Flash. Pro orchestrates only.
- **Flash doing Pro work (e.g., Phase 1, 9, 10):** Delegate ALL work to a Pro sub-agent. Never ask the user to switch models.

---

## Asana Integration

**Project naming:** `sdlc-<directory-name>`

**Sections:** Backlog → Ready → In Progress → Done

**Commands:**
```bash
~/projects/coding-ai-config/scripts/asana-api.sh move <task_gid> <section_gid>
~/projects/coding-ai-config/scripts/asana-api.sh complete <task_gid>
~/projects/coding-ai-config/scripts/asana-api.sh find-project "sdlc-<project-name>"
~/projects/coding-ai-config/scripts/asana-api.sh find-section <project_gid> "In Progress"
```

---

## Git

**Commit format:** `phase <N>: <description>`

**Feature updates:** `phase <N>: [feature-name] <description>`

---

## Tech Stack (Default)

- **Backend:** Python 3.12+, FastAPI, PostgreSQL, SQLAlchemy 2.0, Alembic, Pydantic, PyJWT, pwdlib, pytest
- **Frontend:** React 19, Vite, TypeScript, Tailwind v4, TanStack Query v5, Zustand v5, Zod v4
- **Frontend testing:** Playwright (all UI verification). **NEVER use curl/HTTP to check frontend behavior** — curl tests the API, not what users see. If it's user-facing, use Playwright or a browser tool.
- **Infra:** Docker, Docker Compose

---

## Writing Rules

- Bullets over prose
- Tables for structured data
- No filler words
- Max 3 nesting levels

---

## Key Files

`.project` | `config.yaml` | `seed.md` | `backlog.md` | `development-tasks.md`

---

## Reference

- [software-development-guidance.md](.sdlc/software-development-guidance.md) — Full phase details, gates, hooks, lessons learned
- [AGENTS.md](./AGENTS.md) — Complete agent guidance with examples
- [templates/](.sdlc/templates/) — File and config templates
