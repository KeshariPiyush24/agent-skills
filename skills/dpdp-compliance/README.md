# dpdp-compliance — DPDP Compliance Agent Skill

An [agent skill](https://agentskills.io) that helps AI agents **audit and implement DPDP compliance** against **India's Digital Personal Data Protection Act, 2023 (DPDP Act)** and the **DPDP Rules, 2025**: gap reports with Section/Rule citations, consent and data-rights features, legal Q&A, and compliance artifacts. Technical guidance only — not legal advice.

Works with Claude Code, Cursor, Codex, Gemini CLI, Windsurf, and 40+ other agents that support the open `SKILL.md` standard.

## Install

```bash
# From this monorepo (recommended)
npx skills add KeshariPiyush24/agent-skills -s dpdp-compliance

# Global
npx skills add KeshariPiyush24/agent-skills -g -s dpdp-compliance -y --copy
```

## What it does

- **Audit** — personal-data touchpoints → severity-ranked gap report (Sections A–M: consent, security, breach, erasure, rights, children, SDF, transfers, Consent Manager builders, HR/§7(i), cookies/SDKs, Rule 23 readiness)
- **Implement** — consent + cookie/SDK gating, rights APIs (with consent/§7(a) scope gate), breach, retention/legal-hold, parental consent, HR controls, Consent Manager platform patterns
- **Q&A** — Act, Rules 2025, penalties (up to Rs 250 crore), Board, phased enforcement (see `examples.md`)
- **Artifacts** — privacy notices, consent schemas, breach drafts, DPIA checklists

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
| `examples.md` | Golden Q&A and sample audit finding shapes |
| `references/dpdp-act-2023.md` | DPDP Act 2023 paraphrase by section (India Code provenance) |
| `references/dpdp-rules-2025.md` | DPDP Rules 2025 (corrigenda applied), by rule and schedule |
| `references/timelines-penalties.md` | Commencement dates, penalty table, Data Protection Board facts |
| `references/audit-checklist.md` | Obligations mapped to concrete code-level checks (A–M) |
| `references/implementation-guide.md` | Engineering patterns for consent, CM, rights, breach, retention, HR, cookies |
| `assets/` | Privacy notice, consent schema, breach notice, DPIA templates |

## Legal status of the law

- Act enacted 11 August 2023; Rules notified 13 November 2025 (G.S.R. 846(E); corrigenda G.S.R. 892(E))
- Phased commencement (G.S.R. 843(E)): Board/definitions/rule-making in force now; Consent Manager registration from 13 Nov 2026; core fiduciary duties, rights, appeals, and penalties from 13 May 2027

## Disclaimer

This skill provides technical compliance guidance, **not legal advice**. Consult qualified counsel for legal decisions.

## License

MIT
