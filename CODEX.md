# CODEX.md

> **DIRECTIVE:** Follow [AGENTS.md](./AGENTS.md) as the primary SDLC policy.
> At session start and after context resets, re-read `AGENTS.md`, `config.yaml`, and `.project`.

---

## SDLC Process (Required)

- Treat prompts like "spec ..." as a Phase 1 start.
- Follow the scope path and phase gates defined in `AGENTS.md`.
- Read the phase persona from `.sdlc/agents/phase-X-*.md` before each phase.
- Produce required phase deliverables in `features/<story-folder>/`.
- Keep `.project`, `backlog.md`, `development-tasks.md`, and Asana synchronized at every transition.

## Execution Notes For Codex

- Codex does not rely on slash commands; execute the same workflow through plain-language directives.
- Treat `spec ...` and `/spec ...` as equivalent.
- Treat `next STORY-XXX` and `/next STORY-XXX` as equivalent.
- Use `skills/*/SKILL.md` as procedural references when a phase or workflow is requested.
- For platform-specific behavior, prefer:
  - `.sdlc/skills/next/codex.md`
  - `.sdlc/skills/spec/codex.md`
- For phase advancement, follow the same `advance` semantics (`gate`, `confirm`, `auto`) from agent personas.
- Respect model-tier gates in `AGENTS.md` and `config.yaml`.

## Required Files

`AGENTS.md` | `config.yaml` | `.project` | `backlog.md` | `development-tasks.md`

## References

- [AGENTS.md](./AGENTS.md)
- [software-development-guidance.md](.sdlc/software-development-guidance.md)
- [agents/README.md](.sdlc/agents/README.md)
- [skills/](.sdlc/skills/)
