# CLAUDE.md

> **DIRECTIVE:** This file contains critical guidance that MUST be followed for all work.
> After context compaction, re-read this file and `.project` to restore phase context.

---

## SDLC Process (REQUIRED)

See [AGENTS.md](./AGENTS.md) for: phase paths, advance categories, data mutation policy, skills, tech stack, writing rules, and git conventions.

**"spec" trigger:** Any prompt starting with "spec" MUST initiate Phase 1. Treat "spec ..." as equivalent to `/phase-1 ...`. When `multi_worker: true`, spec also auto-runs `/start-story` after Phase 1.

**Story switching (CRITICAL — NEVER auto-switch stories, even with auto-accept):**
`auto` advance means auto-advance to the next **phase within the same story**. It NEVER means switch to a different story/ticket. When a story is complete:
1. Output the completion summary
2. **STOP. END YOUR RESPONSE. Do NOT claim, start, or begin the next story.**
3. Wait for the user to explicitly tell you which story to work on next
4. This rule applies even if auto-accept is enabled — auto-accept controls phase transitions, NEVER story transitions

**Full phase details:** See [software-development-guidance.md](.sdlc/software-development-guidance.md)

---

## Deliverable Location (REQUIRED)

**All phase deliverables MUST be written to:** `features/<story-folder>/`

**Folder naming:** `features/story-XXX-kebab-case-slug/` where XXX is the story number and slug is derived from the Asana task name.

Example: `features/story-014-str-automations/seed.md`

**NEVER write deliverables to the project root or a `docs/` directory.** The `features/` directory is the single source of truth for all SDLC artifacts.

**Multi-worker mode:** When using worktrees, deliverables still go in `features/<story-folder>/` within the worktree.

---

## Phase Deliverables (REQUIRED — every phase MUST produce its file)

All files below are written to `features/<story-folder>/`:

| Phase | Output File(s) | Scope |
|-------|---------------|-------|
| 1 | `seed.md` | All |
| 2 | `research.md` | Large/New |
| 3 | `expansion.md` | Large/New |
| 4 | `analysis.md` | Medium+ |
| 5 | `selection.md` | Large/New |
| 6 | `feature-spec.md` (Medium) OR `specification.md`, `architecture.md`, `api-design.md`, `database-schema.md`, `implementation-plan.md` (Large/New) | Medium+ |
| 6b | `security-review.md` | Medium+ |
| 6c | `ux-review.md` | Medium+ |
| 7 | `test-design.md` + runnable test code in `tests/`/`e2e/` (RED state) | All |
| 8 | Implementation code (all tests GREEN) | All |
| 8b | `code-review.md` | Medium+ |
| 11 | `predeploy-gate.md` | Medium+ |
| 9 | `refinement-report.md` | Large/New |
| 10 | `site-reliability.md` | Large/New |

**Every phase MUST also update:** `.project`, `backlog.md`, `development-tasks.md`, Asana task (add comment summarizing work)

**A phase is NOT complete until its output file exists in `features/<story-folder>/` and tracking docs are updated.**

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
| 11 (Pre-Deploy Gate) | Sonnet (default) | medium |
| 9 (Refinement) | Opus (always) | high |
| 10 (Operations) | Opus (always) | high |

**Model Enforcement (HARD GATE — no exceptions):**
- Before starting ANY phase work, check: does your current model match the phase's required model?
- **Opus doing Sonnet work (e.g., Phase 8):** Delegate ALL work to Sonnet subagents. Opus orchestrates only. This prevents ~15x cost overrun.
- **Sonnet doing Opus work (e.g., Phase 1, 9, 10):** Delegate ALL work to an Opus subagent. Never ask the user to switch models.
- **config.yaml override:** If `models.opus_allowed: true`, Opus may do Sonnet-default phases directly.

---

## Context Management (CRITICAL)

- `/clear` between context groups, not every phase (default: `grouped` strategy)
- Use `/next` to auto-determine when `/clear` is needed
- Use subagents for research (Phases 2-3) — never pollute main context
- After 2 failed corrections, `/clear` and restart with a better prompt
- Commit after each logical unit in Phase 8
- Never implement more than one function/endpoint per prompt

**Context groups:** Seed | Research (2,3) | Evaluation (4,5) | Design (6,[6b,6c,6d]) | Test (7) | Implementation (8,8b) | Deploy (11) | Polish ([9,10])

**After `/clear` — "continue", "next step", or `/next`:**
1. Read `.project` → Phase Routing section to find current phase and status
2. Read the agent persona for that phase
3. Begin or resume the phase — no re-explanation needed

---

## Asana Integration (Claude-specific)

See [AGENTS.md](./AGENTS.md) § Asana Integration for wrapper policy, script commands, hierarchy, and general Asana rules.

**Claude-specific MCP Tools:** `asana_search_tasks`, `asana_create_task`, `asana_update_task`, `asana_get_projects`, `asana_get_tasks`

**Long text arguments (REQUIRED):** For `update-notes` and `comment` with multi-line text, write to a temp file then pipe via stdin with `-` as the text arg:
```bash
cat > /tmp/asana-comment.txt << 'COMMENT'
Phase X completed. Summary...
COMMENT
cat /tmp/asana-comment.txt | cai asana-api.sh comment <task_gid> -
```

---

## Git (Claude-specific)

See [AGENTS.md](./AGENTS.md) § Git for commit format and check-in commands.

**Worktree git commands (REQUIRED):** Always use `git -C <path>` instead of `cd <path> && git ...`. This avoids the compound-command approval prompt triggered by bare repository attack prevention.

---

## Reference

- [AGENTS.md](./AGENTS.md) — Canonical SDLC policy (phase paths, skills, tech stack, advance categories, Asana, git)
- [software-development-guidance.md](.sdlc/software-development-guidance.md) — Phase details, gates, hooks, lessons learned
- [templates/](.sdlc/templates/) — File and config templates
