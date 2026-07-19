# DPDP Compliance Skill — Examples

Golden inputs/outputs agents should match. Always end real answers with the not-legal-advice disclaimer.

---

## Example 1 — Timeline Q&A (must not say appeals are live)

**User:** When do DPDP obligations and penalties actually come into force?

**Correct answer shape:**

- Board / definitions / rule-making: **13 November 2025** (G.S.R. 843(E) — Sections 1(2), 2, 18-26, 35, 38-43, 44(1)/(3); Rules 1, 2, 17-21).
- Consent Manager registration: **13 November 2026** (Section 6(9), Section 27(1)(d), Rule 4).
- Core fiduciary duties, Data Principal rights, Board inquiry (except 27(1)(d)), **appeals (Section 29)**, and **penalties (Section 33)**: **13 May 2027** (Sections 3-5, 6(1)-(8)/(10), 7-17, 27 except 27(1)(d), 28-34, 36-37, 44(2); Rules 3, 5-16, 22-23).
- Cite `references/timelines-penalties.md`. Do **not** claim appeals or penalties are enforceable before 13 May 2027.

---

## Example 2 — Breach notification Q&A

**User:** What are the breach notification requirements under the DPDP Rules?

**Correct answer shape:**

- Section 8(6) + **Rule 7**: on becoming aware of **any** personal data breach (no materiality threshold):
  - Intimate each affected Data Principal **without delay** (Rule 7(1) content: nature/extent/timing, likely consequences, mitigation, safety steps, contact).
  - Intimate the Board **without delay** with a description, then a **detailed report within 72 hours** (extendable by the Board on written request) covering facts/causes, mitigation, perpetrator findings, remediation, and user-intimation report (Rule 7(2)).
- In force with core obligations from **13 May 2027**. Template: `assets/breach-notification-template.md`.

---

## Example 3 — Sample audit finding (output format)

**User:** Audit this repo for DPDP compliance.

**Finding excerpt agents should emulate:**

```markdown
### [CRITICAL] No personal-data-breach user notification path — Section 8(6), Rule 7 (penalty exposure: up to Rs 200 crore)
- Evidence: no incident-notification job or template under `ops/` or `apps/web`; `grep -i breach` returns only dependency CVEs
- Requirement: On becoming aware of a personal data breach, intimate each affected Data Principal without delay with Rule 7(1) particulars, and intimate the Board without delay plus a detailed report within 72 hours (Rule 7(2))
- Fix: Add affected-user scoping query from audit logs; implement broadcast via user account + registered email/SMS using `assets/breach-notification-template.md`; add Board 72h runbook with clock starting at awareness
- Status of law: enforceable from 13 May 2027 — build before that date
```

Every finding must include: severity, Section/Rule cite, file:line (or proven absence), requirement, concrete fix. End the full report with the disclaimer.

---

## Example 4 — Consent Manager vs Fiduciary

**User:** We're building a consent dashboard for our SaaS users. Do we need to register as a Consent Manager?

**Correct answer shape:**

- A **Consent Manager** is a Board-registered platform enabling Data Principals to give/manage/review/withdraw consent across fiduciaries (Section 2(g), Section 6(7)-(9), Rule 4, First Schedule) — India company, net worth >= Rs 2 crore, non-readable routing, 7-year records, no subcontract, etc.
- A SaaS that only manages consent **for its own processing** as a Data Fiduciary does **not** need CM registration; it must still meet Sections 5-6 and Rule 3 (notice, purpose-scoped consent, withdrawal, provable records).
- If the product will sit between many unrelated fiduciaries as a single point of contact for principals, treat it as a CM build → audit Section J / implementation-guide §12; registration path from **13 Nov 2026**.

---

## Example 5 — Employment data

**User:** Does our employee HRIS need the same consent popups as the consumer app?

**Correct answer shape:**

- Employment-related processing may rest on **Section 7(i)** (legitimate use) rather than Section 6 consent — do not bolt consumer consent walls onto pure HR purposes without checking fit.
- Still need: employee/applicant notice, security (Section 8(5)/Rule 6), vendor contracts (Section 8(2)), retention/legal holds, grievance (Section 13). Sections 11-12 access/correction/erasure are gated to prior consent / Section 7(a) — escalate pure §7(i) DSARs for human/legal review.
- Use audit Section K and implementation-guide §11.
