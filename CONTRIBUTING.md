# Contributing

Thanks for helping improve these agent skills.

## Quick ideas that help a lot

- File an [issue](https://github.com/KeshariPiyush24/agent-skills/issues) with a missed obligation, ambiguous cite, or bad agent output
- Improve a paraphrase against the official Gazette / India Code text (include the Section/Rule and a quote or link)
- Add a golden example to `skills/dpdp-compliance/examples.md`
- Fix typos, broken relative paths, or unclear README wording

## Ground rules

1. **Do not invent law.** Cite Act Sections or Rules 2025 Rules/Schedules. If unsure, open an issue instead of guessing.
2. **Keep the disclaimer.** Outputs and docs stay “technical guidance, not legal advice.”
3. **Progressive disclosure.** Put dense material in `references/` or `assets/`; keep `SKILL.md` lean.
4. **Match `name` to the folder** in each skill’s YAML frontmatter.
5. **One skill, one PR** when possible — easier to review.

## Repo layout

```text
skills/<skill-name>/
  SKILL.md          # required entry point
  README.md         # human docs
  examples.md       # golden prompts / outputs
  references/       # dense domain material
  assets/           # templates agents adapt
```

## Local check

After editing `dpdp-compliance`:

1. Skim `SKILL.md` workflows still point at existing files
2. Confirm dates/penalties match `references/timelines-penalties.md`
3. Run through one prompt from `DEMO.md` in your agent

## Security

See [SECURITY.md](./SECURITY.md). Do not open PRs that add credential scraping, exfiltration, or exploit payloads.

## License

By contributing, you agree your contributions are licensed under MIT (same as this repository).
