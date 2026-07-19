---
name: dpdp-compliance
description: Audits and implements India's Digital Personal Data Protection (DPDP) Act 2023 and DPDP Rules 2025 compliance. Use when auditing a codebase for DPDP gaps, implementing consent management, Consent Manager platforms, data principal rights APIs, breach notification, retention/erasure, verifiable parental consent, employment/HR (Section 7(i)) controls, or cookie/SDK gating, answering questions about the Act, Rules, penalties, Data Protection Board, or enforcement timelines, or generating privacy notices, consent records, breach drafts, DPIA checklists. Triggers include DPDP, Digital Personal Data Protection, India data privacy, data fiduciary, data principal, consent manager, Significant Data Fiduciary, MeitY. Do not use for GDPR-only, CCPA-only, or other non-Indian privacy regimes, and do not use as a substitute for professional legal advice.
license: MIT
metadata:
  version: 1.1.1
  author: kesharipiyush24
  domain: privacy-compliance
  jurisdiction: India
  sources: G.S.R. 843(E)/844(E)/845(E)/846(E) (13 Nov 2025); G.S.R. 892(E) (10 Dec 2025); DPDP Act 2023 (India Code a2023-22)
---

# DPDP Compliance Skill

Help software teams comply with India's **Digital Personal Data Protection Act, 2023** (DPDP Act) and the **Digital Personal Data Protection Rules, 2025** (DPDP Rules).

## Legal status (state this honestly, never overstate)

- DPDP Act enacted 11 August 2023; DPDP Rules notified 13 November 2025 (G.S.R. 846(E), corrigenda G.S.R. 892(E) of 10 Dec 2025).
- Commencement is **phased** from 13 November 2025 (G.S.R. 843(E)):
  - **Now**: Section 1(2), Section 2, Sections 18-26 (Board), Section 35, Sections 38-43 (incl. rule-making), Section 44(1)/(3); Rules 1, 2, 17-21.
  - **+12 months (13 Nov 2026)**: Section 6(9), Section 27(1)(d); Rule 4 and First Schedule (Consent Manager registration).
  - **+18 months (13 May 2027)**: Sections 3-5, 6(1)-(8) and (10), 7-17, Section 27 except 27(1)(d), Sections 28-34 (inquiry, **appeals**, ADR, voluntary undertaking, **penalties**), 36, 37, Section 44(2); Rules 3, 5-16, 22-23 and linked Schedules.
  - Do **not** say appeals or penalties are in force before 13 May 2027.
- The Data Protection Board of India is established (HQ in the National Capital Region; G.S.R. 845(E) notifies four members — whether that count includes the Chairperson is not stated in the notification) — G.S.R. 844(E), 845(E).
- For exact dates and the penalty table, read `references/timelines-penalties.md`.

## Rules of engagement

1. **Always cite** the specific Section (Act) or Rule (Rules 2025) for every claim or finding, e.g. "Section 8(7)" or "Rule 8, Third Schedule".
2. **Always include a disclaimer** in reports and answers: "This is technical compliance guidance, not legal advice. Consult qualified counsel for legal decisions."
3. Never invent obligations. If unsure, read the reference files below before answering.
4. Respect the phased commencement: distinguish "in force now" vs "in force from 13 Nov 2026 / 13 May 2027" in every recommendation.

## Workflows

Pick the workflow that matches the user's request. Read only the reference files that workflow needs (progressive disclosure).

### 1. Audit a codebase for DPDP compliance

When asked to audit, review, or assess a codebase/product for DPDP compliance:

1. Read `references/audit-checklist.md` (and skim `examples.md` Example 3 for report shape).
2. Complete Step 0 scope (role map, exemptions, SDF, Third Schedule, CM/HR/cookie flags).
3. Map personal-data touchpoints — auth/signup, forms, DB schemas, analytics/SDK/cookies, logging, vendors, HR systems, exports.
4. Work through every applicable checklist section (A–M); record status (compliant / gap / not-applicable / needs-human-review) with file:line evidence.
5. Output a severity-ranked report (Critical / High / Medium / Low) citing Section/Rule, evidence, and a concrete fix. End with the disclaimer.

### 2. Implement compliance features

When asked to build or fix compliance functionality (consent flows, Consent Manager, rights APIs, retention jobs, breach handling, parental consent, HR/cookie controls):

1. Read `references/implementation-guide.md`.
2. Read the target repository's own conventions (framework, package manager, existing auth/user model) and follow them.
3. Implement using the patterns in the guide; use `assets/consent-record-schema.md` for consent data models.
4. In code comments and docs, cite the Section/Rule each feature satisfies.

### 3. Answer questions about the law

When asked what the Act/Rules say, definitions, obligations, penalties, timelines, or the Board:

1. For Act questions read `references/dpdp-act-2023.md`; for Rules questions read `references/dpdp-rules-2025.md`; for dates, penalties, and Board facts read `references/timelines-penalties.md`. Match tone of `examples.md` Examples 1-2, 4-5.
2. Quote or closely paraphrase the actual text, cite Section/Rule numbers, and note whether the provision is in force yet.
3. End with the not-legal-advice disclaimer.

### 4. Generate compliance artifacts

When asked to produce documents or templates:

- Privacy notice (Section 5, Rule 3): adapt `assets/privacy-notice-template.md`.
- Consent record data model (Section 6; notice versioning via Rule 3): adapt `assets/consent-record-schema.md`.
- Breach notification to Data Principals and the Board (Section 8(6), Rule 7): adapt `assets/breach-notification-template.md`.
- Data Protection Impact Assessment for Significant Data Fiduciaries (Section 10, Rule 13): adapt `assets/dpia-checklist.md`.

Fill placeholders with the user's actual product/company details; never leave `[PLACEHOLDER]` text in delivered artifacts without flagging it.

## Key concepts (quick reference)

- **Data Principal**: the individual the personal data is about (includes parents/guardians acting for children or persons with disability).
- **Data Fiduciary**: the entity that determines purpose and means of processing (analogous to a GDPR controller).
- **Data Processor**: processes on behalf of a Data Fiduciary under a valid contract.
- **Significant Data Fiduciary (SDF)**: notified class with extra obligations — DPO in India, annual DPIA and audit, algorithmic due diligence, possible data-localisation restrictions.
- **Consent Manager**: registered platform letting Data Principals give, manage, review and withdraw consent (Rule 4, First Schedule).
- Applies to digital personal data processed in India, and outside India if connected to offering goods/services to Data Principals in India (Section 3). No extra "sensitive data" category — one tier of personal data.

## Contents map

| Path | When to read |
| --- | --- |
| `references/audit-checklist.md` | Audits (sections A–M) |
| `references/implementation-guide.md` | Building features |
| `references/dpdp-act-2023.md` | Act Q&A (paraphrase; verify India Code for legal decisions) |
| `references/dpdp-rules-2025.md` | Rules/Schedules Q&A |
| `references/timelines-penalties.md` | Dates, Board, penalties |
| `examples.md` | Golden Q&A and audit finding shape |
| `assets/*` | Notice, consent schema, breach, DPIA templates |
