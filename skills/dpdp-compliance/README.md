# dpdp-compliance — DPDP Compliance Agent Skill

Make your software ready for **India's data protection law** — with your AI coding agent doing the heavy lifting.

This is an [agent skill](https://agentskills.io): a set of instructions and reference material that teaches AI coding agents (Cursor, Claude Code, Codex, Gemini CLI, Windsurf, and 40+ others) how to audit, build, and explain compliance with the **Digital Personal Data Protection Act, 2023 (DPDP Act)** and the **DPDP Rules, 2025**.

> **Not legal advice.** This skill gives technical compliance guidance for engineers. For legal decisions, consult qualified counsel.

---

## What is the DPDP Act? (60-second primer)

India's first comprehensive data protection law. If your app, website, or backend processes the **digital personal data** of people in India (or serves goods/services to people in India from abroad), it applies to you.

In plain terms, the law says:

1. **Tell people** what data you collect and why (notice).
2. **Ask permission** before using it, purpose by purpose (consent) — unless a specific "legitimate use" applies.
3. **Protect it** (security safeguards — highest penalty tier: **Rs 250 crore** for failures).
4. **Report breaches** — to affected users and the Data Protection Board, with a detailed report within **72 hours** of becoming aware.
5. **Delete data** when it's no longer needed or when the user withdraws consent.
6. **Give users rights** — to access, correct, and erase their data, and to complain.
7. **Extra care for children** (under 18): verifiable parental consent, no tracking or targeted ads.

The rules are being enforced in phases — most obligations bite from **13 May 2027**, so teams shipping in India should be compliant before then.

## Key dates

| Date | What comes into force |
| --- | --- |
| **13 Nov 2025** | Definitions, the Data Protection Board, rule-making powers |
| **13 Nov 2026** | Consent Manager registration |
| **13 May 2027** | Everything else: notice, consent, security, breach reporting, user rights, children's data, penalties, appeals |

---

## Vocabulary (the words you'll keep seeing)

| Term | Plain-English meaning |
| --- | --- |
| **Data Principal** | The person the data is about — your user. Includes parents/guardians acting for children or persons with disability. |
| **Data Fiduciary** | The company that decides *why* and *how* data is processed — probably you. (Like a GDPR "controller".) |
| **Data Processor** | A vendor that processes data *on your behalf*, under contract — e.g. your cloud provider or email service. |
| **Significant Data Fiduciary (SDF)** | A company the government designates for extra obligations: an India-based Data Protection Officer, yearly impact assessments and audits, algorithm checks. |
| **Consent Manager** | A government-registered platform where users can give, review, and withdraw consent in one place. Needs Rs 2 crore net worth and Board registration. |
| **Data Protection Board (DPB)** | The regulator. Digital-first — complaints and hearings happen online. Established November 2025. |
| **DPIA** | Data Protection Impact Assessment — a structured risk review that SDFs must do every 12 months. |
| **Personal data breach** | Any unauthorised processing, disclosure, loss, or destruction of personal data. **Every** breach must be reported — there is no "too small to report". |
| **Verifiable parental consent** | Proving a parent is a real, identifiable adult before processing a child's data — e.g. via DigiLocker or existing verified records. A "I am over 18" checkbox is not enough. |
| **Section vs Rule** | "Section X" = the DPDP **Act** (the law passed by Parliament). "Rule Y" = the DPDP **Rules 2025** (how the government operationalises it). |

## Penalties at a glance

| Violation | Max penalty |
| --- | --- |
| Weak security safeguards | **Rs 250 crore** |
| Not reporting a breach | Rs 200 crore |
| Children's data violations | Rs 200 crore |
| SDF obligation failures | Rs 150 crore |
| Anything else in the Act/Rules | Rs 50 crore |

---

## Installation

Requires Node.js (for `npx`). Works with any agent supporting the open `SKILL.md` standard.

```bash
# Into the current project (recommended)
npx skills add KeshariPiyush24/agent-skills -s dpdp-compliance

# Globally (available in every project on your machine)
npx skills add KeshariPiyush24/agent-skills -g -s dpdp-compliance -y --copy
```

That's it. Your agent auto-discovers the skill — no configuration needed. It activates whenever you mention DPDP, Indian data privacy, consent, data fiduciary, and similar topics.

## Usage

Just talk to your agent naturally. Some starters:

```text
Audit this repo for DPDP compliance
Add a DPDP-compliant consent flow to our signup
What are the breach notification requirements under the DPDP Rules?
When do the DPDP penalties actually come into force?
Generate a privacy notice for our app
Design a consent record schema for our Postgres database
```

## Example use cases

**1. "Are we compliant?" — the audit**
Point the agent at your codebase and ask for a DPDP audit. It maps every place personal data flows (signup forms, databases, analytics SDKs, logs, vendors), checks each against the law across thirteen areas (consent, security, breach, erasure, rights, children, SDF, transfers, data quality, Consent Manager builders, HR, cookies, government information calls), and produces a severity-ranked report where every finding cites the exact Section/Rule, shows file-level evidence, and proposes a fix.

**2. "Build the consent flow" — implementation**
Ask the agent to add consent management. It builds purpose-scoped consent (not one big "accept all" checkbox), an append-only consent record you can use as legal proof, easy withdrawal, and propagation to your vendors — following your repo's existing framework and conventions.

**3. "We had an incident" — breach readiness**
Before you ever need it: the agent sets up the breach runbook — who to notify, what the notices must contain, the 72-hour report to the Board, and the audit-log query that answers "which users were affected?"

**4. "Do we need parental consent?" — children's data**
If minors can use your product, the agent implements age assurance and verifiable parental consent (e.g. DigiLocker), and disables tracking/targeted ads for child accounts.

**5. "What does the law actually say?" — Q&A**
Ask anything about the Act, the Rules, penalties, deadlines, or the Board. Answers quote the actual provisions with citations and tell you whether each one is in force yet.

**6. "Generate the paperwork" — artifacts**
Privacy notices, consent database schemas, breach notification drafts, DPIA checklists — adapted to your product, from templates aligned to the exact Rule requirements.

## What's inside

| Path | Purpose |
| --- | --- |
| `SKILL.md` | Entry point: the agent's workflows and rules of engagement |
| `examples.md` | Golden Q&A answers and a sample audit finding |
| `references/dpdp-act-2023.md` | The Act, section by section (paraphrase, pinned to India Code) |
| `references/dpdp-rules-2025.md` | The Rules 2025 with corrigenda applied, rule by rule + all 7 schedules |
| `references/timelines-penalties.md` | Enforcement dates, penalty table, Data Protection Board facts |
| `references/audit-checklist.md` | Code-level compliance checks, sections A–M |
| `references/implementation-guide.md` | Engineering patterns: consent, rights APIs, breach, retention, children, HR, cookies, Consent Manager platforms |
| `assets/` | Ready-to-adapt templates: privacy notice, consent schema, breach notice, DPIA checklist |

## Sources

Built from the official Gazette of India notifications: G.S.R. 843(E), 844(E), 845(E), 846(E) of 13 November 2025 and corrigenda G.S.R. 892(E) of 10 December 2025, plus the DPDP Act, 2023 (India Code). Every claim in the references carries a Section/Rule citation.

## FAQ

**Does this replace a lawyer?** No. It finds engineering gaps and builds compliant features fast, but legal sign-off needs qualified counsel.

**We're GDPR compliant — are we done?** Not automatically. DPDP differs: one tier of personal data (no "sensitive" category), children are under 18, breach reporting has no materiality threshold, notices must be available in 22 Indian languages, and penalties are structured differently.

**We're a small startup — does this apply?** Yes, unless the government specifically notifies an exemption for your class. Don't assume you're exempt.

**When do we actually need to be ready?** Core obligations are enforceable from **13 May 2027**. Building consent, security, and deletion correctly takes time — start early.

## Disclaimer

This skill provides technical compliance guidance, **not legal advice**. Consult qualified counsel for legal decisions.

## License

MIT
