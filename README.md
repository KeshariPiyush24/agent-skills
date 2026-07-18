# agent-skills

A collection of [agent skills](https://agentskills.io) for AI coding agents (Cursor, Claude Code, Codex, Gemini CLI, Windsurf, and 40+ others). Install from this repo once; pick the skills you need.

## Skills

| Skill | What it does | Install |
| --- | --- | --- |
| [`dpdp-agent-skill`](./skills/dpdp-agent-skill/) | India's DPDP Act 2023 & Rules 2025 — audit, implement, Q&A, compliance artifacts | `npx skills add KeshariPiyush24/agent-skills -s dpdp-agent-skill` |

## Install

```bash
# All skills in this repo
npx skills add KeshariPiyush24/agent-skills

# One skill only
npx skills add KeshariPiyush24/agent-skills -s dpdp-agent-skill

# Global (available in every project on this machine)
npx skills add KeshariPiyush24/agent-skills -g -y --copy
```

## Adding a skill to this repo

1. Create `skills/<skill-name>/` with a `SKILL.md` (YAML frontmatter: `name` must match the folder).
2. Put dense reference material under `skills/<skill-name>/references/` and templates under `assets/`.
3. Add a row to the table above.
4. Push to `main` — skills.sh and the CLI discover skills under `skills/*/SKILL.md`.

## License

MIT — see each skill folder for its LICENSE.
