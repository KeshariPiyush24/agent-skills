# Personal Data Breach Notification Templates (Section 8(6) of the DPDP Act, Rule 7 of the DPDP Rules)

Two notifications are mandatory for every personal data breach — there is no materiality threshold. Fill every `[PLACEHOLDER]`; delete nothing required by the Rule.

Timeline:

- To each **affected Data Principal**: without delay, via her user account or registered communication mode (Rule 7(1)).
- To the **Data Protection Board**: (stage 1) description without delay; (stage 2) detailed report within **72 hours** of becoming aware, unless the Board grants an extension on written request (Rule 7(2)).

---

## Template 1 — Notification to affected Data Principals (Rule 7(1))

Subject: Important security notice about your [PRODUCT] account

Dear [NAME],

We are writing to inform you about a data security incident that affected your personal data.

**What happened** <!-- Rule 7(1)(a): nature, extent, timing -->
On [DATE/TIME DISCOVERED], we became aware that [DESCRIPTION: e.g. an unauthorised party accessed a database containing customer information between [START] and [END]]. The incident involved the following personal data relating to you: [ITEMISED LIST — e.g. name, email address, phone number; state clearly what was NOT affected, e.g. passwords, payment details].

**What this may mean for you** <!-- Rule 7(1)(b): likely consequences -->
[E.g. You may receive phishing emails or calls impersonating us. / Your account could be accessed if you reuse this password elsewhere.]

**What we have done** <!-- Rule 7(1)(c): mitigation measures -->
[E.g. We revoked the compromised credentials, patched the vulnerability, engaged forensic experts, and notified the Data Protection Board of India.]

**What you can do to protect yourself** <!-- Rule 7(1)(d): safety measures -->
[E.g. Change your password; enable two-factor authentication; be cautious of unexpected messages asking for OTPs or personal details; we will never ask for your password.]

**Contact** <!-- Rule 7(1)(e): responsible contact -->
If you have questions, contact [NAME/TITLE], [EMAIL], [PHONE], available [HOURS].

We sincerely regret this incident.
[COMPANY NAME]

---

## Template 2 — Stage 1: Immediate intimation to the Board (Rule 7(2)(a))

To: Data Protection Board of India [via prescribed portal/channel]
From: [COMPANY LEGAL NAME], [CIN/registration], [ADDRESS]
Contact: [DPO/CONTACT NAME, EMAIL, PHONE]
Date/time of this intimation: [TIMESTAMP]

1. **Nature of the breach**: [unauthorised access / accidental disclosure / loss of access / alteration / destruction — per Section 2(u)]
2. **Extent**: [number of Data Principals affected (estimate if necessary, state it is an estimate); categories of personal data involved]
3. **Timing of occurrence**: [when the breach occurred or is believed to have occurred; when we became aware]
4. **Location of occurrence**: [system/service/region affected]
5. **Likely impact**: [assessment of consequences for affected Data Principals]

---

## Template 3 — Stage 2: Detailed report to the Board within 72 hours (Rule 7(2)(b))

Reference: our intimation dated [DATE].

1. **Updated and detailed description** <!-- 7(2)(b)(i) -->: [refined facts: confirmed scope, affected data categories and counts, timeline]
2. **Events, circumstances and reasons leading to the breach** <!-- 7(2)(b)(ii) -->: [root cause: e.g. credential compromise via phishing; unpatched dependency CVE-XXXX; misconfigured storage bucket]
3. **Mitigation measures implemented or proposed** <!-- 7(2)(b)(iii) -->: [containment, credential rotation, patches, monitoring added]
4. **Findings regarding the person who caused the breach** <!-- 7(2)(b)(iv) -->: [known/unknown; internal/external; law-enforcement referral status]
5. **Remedial measures to prevent recurrence** <!-- 7(2)(b)(v) -->: [architectural fixes, process changes, audits, training]
6. **Report on intimations to affected Data Principals** <!-- 7(2)(b)(vi) -->: [when and how users were notified, channels used, delivery statistics, copy of the notice attached]

Signature: [AUTHORISED SIGNATORY]

---

## Internal runbook checklist

- [ ] T0: incident confirmed as a personal data breach (Section 2(u)) — start the 72h clock
- [ ] Scope affected Data Principals and data categories (audit-log query)
- [ ] Stage 1 Board intimation sent (without delay)
- [ ] User notifications dispatched (without delay) — Template 1
- [ ] T+72h: Stage 2 detailed report filed — Template 3 (or written extension request submitted to the Board)
- [ ] Evidence preserved; post-incident review scheduled; remediation tracked
