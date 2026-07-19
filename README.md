# Agent Skills — starting with India's DPDP

[![MIT License](https://img.shields.io/badge/license-MIT-blue.svg)](./LICENSE)
[![Agent Skills](https://img.shields.io/badge/standard-agentskills.io-111111.svg)](https://agentskills.io)
[![Install via skills.sh](https://img.shields.io/badge/install-npx%20skills%20add-black.svg)](https://skills.sh/KeshariPiyush24/agent-skills)
[![DPDP Act 2023](https://img.shields.io/badge/India-DPDP%20Act%202023-FF671F.svg)](./skills/dpdp-compliance/)
[![Rules 2025](https://img.shields.io/badge/Rules-G.S.R.%20846(E)-138808.svg)](./skills/dpdp-compliance/references/dpdp-rules-2025.md)

> **Turn your AI coding agent into a DPDP compliance co-pilot** — audit codebases against India's Digital Personal Data Protection Act, 2023 & Rules 2025, ship consent/rights/breach features, and answer Act/Rules questions with real Section and Rule citations.

**Not legal advice.** Technical guidance for engineers. Consult qualified counsel for legal decisions.

---

## Install in 10 seconds

Works with **Cursor, Claude Code, Codex, Gemini CLI, Windsurf**, and 40+ agents that support the open [Agent Skills](https://agentskills.io) standard.

```bash
npx skills add KeshariPiyush24/agent-skills -s dpdp-compliance
```

Then ask your agent:

```text
Audit this repo for DPDP compliance
```

→ [skills.sh listing](https://skills.sh/KeshariPiyush24/agent-skills) · [Full skill docs](./skills/dpdp-compliance/) · [Demo output](./DEMO.md)

---

## Why this exists

India's DPDP regime is real:

| When | What |
| --- | --- |
| **Now** | Data Protection Board is live |
| **13 Nov 2026** | Consent Manager registration |
| **13 May 2027** | Notice, consent, security, breach reporting, user rights, **penalties** (up to **Rs 250 crore**) |

Most coding agents don't know G.S.R. 843–846 from a Medium summary. This skill was built from the **Gazette notifications** (Rules + corrigenda + commencement + Board) so agents cite provisions instead of inventing GDPR folklore.

## What you get

| Capability | What the agent does |
| --- | --- |
| **Audit** | Maps personal-data touchpoints → severity-ranked gap report with file:line evidence and Section/Rule cites |
| **Implement** | Consent flows, rights APIs, breach runbooks, retention/erasure, parental consent, HR/cookies patterns |
| **Q&A** | Act, Rules, Board, penalties, phased timelines — with citations and “is this in force yet?” |
| **Artifacts** | Privacy notice, consent schema, breach drafts, DPIA checklist |

## Skills in this repo

| Skill | What it does | Install |
| --- | --- | --- |
| [`dpdp-compliance`](./skills/dpdp-compliance/) | India's DPDP Act 2023 & Rules 2025 — end-to-end engineering compliance | `npx skills add KeshariPiyush24/agent-skills -s dpdp-compliance` |

## Try these prompts

```text
Audit this repo for DPDP compliance
Add a DPDP-compliant consent flow to our signup
What are the breach notification requirements under Rule 7?
When do DPDP penalties come into force?
Generate a privacy notice for our app
```

See sample agent output in [DEMO.md](./DEMO.md).

## Who it's for

- Engineers and eng leads shipping SaaS to India
- Startups preparing for **13 May 2027**
- Privacy / compliance engineers who want code-level checks
- Anyone building or integrating a **Consent Manager**

## Sources (credibility)

Built against official instruments:

- DPDP Act, 2023 (Act 22 of 2023 / India Code)
- G.S.R. 843(E), 844(E), 845(E), 846(E) — 13 November 2025
- Corrigenda G.S.R. 892(E) — 10 December 2025

## Star · share · contribute

If this saves you a weekend of Gazette spelunking:

1. ⭐ Star the repo (helps others find it)
2. Share the install one-liner with your team
3. Open an [issue](https://github.com/KeshariPiyush24/agent-skills/issues) with gaps or wishlist items

See [CONTRIBUTING.md](./CONTRIBUTING.md) and [SECURITY.md](./SECURITY.md).

## Changelog

See [CHANGELOG.md](./CHANGELOG.md) and [GitHub Releases](https://github.com/KeshariPiyush24/agent-skills/releases).

## License

MIT — see [LICENSE](./LICENSE) and each skill folder.

---

<p align="center">
  <a href="./skills/dpdp-compliance/">Read the DPDP skill docs</a> ·
  <a href="https://skills.sh/KeshariPiyush24/agent-skills">Install on skills.sh</a> ·
  <a href="./DEMO.md">See a sample audit</a>
</p>
