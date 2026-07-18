# dpdp — DPDP Compliance Agent Skill

An [agent skill](https://agentskills.io) that helps any AI coding agent make software compliant with **India's Digital Personal Data Protection Act, 2023 (DPDP Act)** and the **DPDP Rules, 2025**.

Works with Claude Code, Cursor, Codex, and 40+ other agents that support the open `SKILL.md` standard.

## What it does

- **Audit** — scans a codebase for personal-data touchpoints and produces a severity-ranked DPDP gap report with Section/Rule citations
- **Implement** — guides or builds compliance features: consent management, data-principal rights APIs (access, correction, erasure), breach notification, retention/deletion jobs, verifiable parental consent
- **Q&A** — answers questions about the Act, the Rules 2025, penalties (up to Rs 250 crore), the Data Protection Board, and the phased enforcement timeline
- **Artifacts** — generates privacy notices, consent record schemas, breach notification drafts, and DPIA checklists

## Install

```bash
npx skills add kesharipiyush24/dpdp
```

Or point your agent at this repo's `SKILL.md` directly.

## Usage examples

Ask your agent things like:

- "Audit this repo for DPDP compliance"
- "Add a DPDP-compliant consent flow to our signup"
- "What are the breach notification requirements under the DPDP Rules?"
- "When do the DPDP obligations actually come into force?"
- "Generate a privacy notice for our app"

## Contents

| Path | Purpose |
| --- | --- |
| `SKILL.md` | Entry point: workflows and rules of engagement for the agent |
| `references/dpdp-act-2023.md` | The DPDP Act 2023, structured by section |
| `references/dpdp-rules-2025.md` | The DPDP Rules 2025 (with Dec 2025 corrigenda applied), by rule and schedule |
| `references/timelines-penalties.md` | Commencement dates, penalty table, Data Protection Board facts |
| `references/audit-checklist.md` | Obligations mapped to concrete code-level checks |
| `references/implementation-guide.md` | Engineering patterns for consent, rights APIs, breach, retention |
| `assets/` | Ready-to-adapt templates (privacy notice, consent schema, breach notice, DPIA) |

## Legal status of the law

- Act enacted 11 August 2023; Rules notified 13 November 2025
- Phased commencement: Board and definitions in force now; consent-manager registration from 13 Nov 2026; core obligations from 13 May 2027

## Disclaimer

This skill provides technical compliance guidance, **not legal advice**. Consult qualified counsel for legal decisions.

## License

MIT
