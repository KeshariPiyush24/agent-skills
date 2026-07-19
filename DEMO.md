# Demo — what the agent produces with `dpdp-compliance`

This is **illustrative sample output** (not from a specific proprietary codebase). After installing the skill, your agent should produce findings in this shape: severity, Section/Rule cite, evidence, requirement, concrete fix, and the not-legal-advice disclaimer.

## Prompt

```text
Audit this repo for DPDP compliance
```

## Sample finding (Critical)

```markdown
### [CRITICAL] No personal-data-breach user notification path — Section 8(6), Rule 7
(penalty exposure: up to Rs 200 crore)

- Evidence: no incident-notification job or template under `ops/` or `apps/web`;
  `grep -i breach` returns only dependency CVEs
- Requirement: On becoming aware of a personal data breach, intimate each affected
  Data Principal without delay with Rule 7(1) particulars, and intimate the Board
  without delay plus a detailed report within 72 hours of becoming aware (Rule 7(2))
- Fix: Add affected-user scoping query from audit logs; implement broadcast via user
  account + registered email/SMS using `assets/breach-notification-template.md`;
  add Board 72h runbook with the clock starting at awareness (not "full confirmation")
- Status of law: enforceable from 13 May 2027 — build before that date

---
This is technical compliance guidance, not legal advice. Consult qualified counsel
for legal decisions.
```

## Sample Q&A — timelines (must not invent “appeals are live now”)

**Prompt:** `When do DPDP penalties and appeals come into force?`

**Correct answer shape:**

- Board / definitions / rule-making: **13 November 2025** (G.S.R. 843(E))
- Consent Manager registration: **13 November 2026** (Section 6(9), Rule 4)
- Core duties, rights, **appeals (Section 29)**, **penalties (Section 33)**: **13 May 2027**
- Do **not** claim appeals or monetary penalties are enforceable before 13 May 2027

## Sample implement prompt

```text
Add a DPDP-compliant, purpose-scoped consent flow to our signup
(no pre-ticked boxes; withdrawal as easy as grant; append-only consent log)
```

The agent should follow `references/implementation-guide.md` and adapt
`assets/consent-record-schema.md` to your stack.

## Install and try it yourself

```bash
npx skills add KeshariPiyush24/agent-skills -s dpdp-compliance
```

More docs: [skills/dpdp-compliance/README.md](./skills/dpdp-compliance/README.md)
