# DPDP Implementation Guide

Engineering patterns for building DPDP-compliant features. Before implementing anything: read the target repository's own conventions (framework, ORM, auth model, package manager) and follow them — the snippets here are framework-neutral pseudocode to adapt, not to paste. Cite the satisfied Section/Rule in code comments and docs.

## 1. Consent management (Sections 5-6, Rule 3)

### Data model

Use `assets/consent-record-schema.md` for the full schema. Core principles:

- **Purpose-scoped**: one consent record per (data principal, purpose), never a single "accepts privacy policy" boolean. Typical purposes: `service_delivery`, `marketing_email`, `marketing_sms`, `analytics`, `personalization`, `third_party_sharing`.
- **Versioned notice**: store the notice version (and language) shown when consent was captured — Section 6(10) puts the burden of proof on you.
- **Append-only**: record grants and withdrawals as immutable events; derive current state.

### Capture flow

1. Show the Rule 3 notice: itemised data description, purposes, and links to withdraw / exercise rights / complain to the Board. Standalone, plain language; give the Data Principal the **option** to access the notice in English **or any** of the 22 languages in the Eighth Schedule to the Constitution (Section 5(3)) — plan localisation accordingly rather than only shipping languages the product already supports.
2. Unbundle: separate affirmative action per non-essential purpose. No pre-ticked boxes (Section 6(1)).
3. Do not block core service on consent for unnecessary purposes (consent must be "free" and "unconditional").
4. Persist the consent event with notice version, timestamp, mechanism, IP/user agent.

### Withdrawal (Sections 6(4)-(6))

- Withdrawal UI must be reachable with effort comparable to the grant — e.g. a "Privacy & consent" section in account settings with per-purpose toggles.
- On withdrawal, emit an event that: stops the purpose's processing (feature flags/suppression lists), propagates to Data Processors (vendor APIs, e.g. analytics user deletion, ESP suppression), and starts the erasure evaluation (Section 8(7)(a)) unless a legal retention exception applies.
- Withdrawal must not degrade unrelated service.

```text
POST /me/consents          -> grant  {purpose, notice_version, language}
DELETE /me/consents/:purpose -> withdraw
GET  /me/consents          -> current state + history
```

## 2. Data Principal rights APIs (Sections 11-14, Rule 14)

**Scope gate first:** Sections 11 and 12 apply where the Data Principal previously gave **consent** (including consent as referred to in Section 7(a)). For pure Section 7 legitimate uses (e.g. employment under Section 7(i), many State uses), do not blindly expose the same DSAR erasure/correction APIs without counsel — still implement grievance redressal (Section 13), security (Section 8(5)), and erasure where Section 8(7) applies.

Publish on the website/app how to exercise rights and which identifier is required (Rule 14(1)). Verify identity before serving any request (the request must come from the authenticated Data Principal or verified nominee).

### Access (Section 11)

`GET /me/data-summary` (or an async DSAR job) returning:

- summary of personal data being processed and the processing activities (11(1)(a));
- identities of all Data Fiduciaries and Data Processors the data has been shared with + description of what was shared (11(1)(b)) — this requires a maintained **sharing registry**: a static config or DB table listing every vendor/partner, data categories shared, and purpose. Keep it in code review scope so it stays current.

### Correction (Section 12(1)-(2))

Editable profile covering all stored PII, or a correction-request workflow with an SLA. Propagate corrections downstream (Section 8(3) accuracy duty when data feeds decisions or is disclosed).

### Erasure (Section 12(3), Section 8(7))

Implement a **deletion pipeline**, not a row delete:

1. Mark account `pending_erasure`; verify no legal-hold exceptions (KYC/tax/employment retention laws — maintain an explicit retention-exception map).
2. Enumerate all stores: primary DB, replicas handled by TTL, caches, search indexes, object storage, analytics tools, data warehouse, message queues/dead letters, support ticketing, backups (deletion on backup expiry is acceptable practice; document the backup retention window).
3. Call processor deletion APIs; record completions.
4. Keep what Rule 8(3) requires: processing logs and associated personal data for 1 year minimum for Seventh Schedule purposes — segregate this "compliance retention" store with restricted access instead of blocking the erasure.
5. Write an erasure certificate/audit record (what, when, where, exceptions applied).

### Grievance redressal (Section 13, Rule 14(3))

A grievance channel (form/email) with ticket tracking; publish the response period (max 90 days — aim far lower); escalation path; remind users they must exhaust this channel before the Board.

### Nomination (Section 14, Rule 14(4))

Allow the user to record a nominee (name + contact + relationship) who may exercise rights on death/incapacity. A settings field plus a verification procedure for the nominee invoking it is sufficient; document the procedure.

## 3. Breach notification (Section 8(6), Rule 7)

Build the capability before you need it:

- **Detection**: security monitoring/alerts on unusual PII access (Rule 6(1)(c)).
- **Scoping query**: given a compromise window/system, enumerate affected Data Principals and data categories. Design audit logs so this query is answerable.
- **User notification**: a broadcast mechanism (in-app + registered email/SMS) that can send, without delay, the Rule 7(1) content: breach description (nature, extent, timing), likely consequences for the user, mitigation measures taken, safety steps the user should take, and a contact person. Template: `assets/breach-notification-template.md`.
- **Board notification**: runbook with two stages — immediate description (nature, extent, timing, location, likely impact), then within **72 hours** the detailed report (facts/circumstances/causes, mitigation, findings on the perpetrator, remediation to prevent recurrence, and a report of user intimations). Track the 72h clock from "becoming aware".
- Do NOT gate notification on severity assessment — the Rules have no materiality threshold; every personal data breach triggers Rule 7.

## 4. Retention and scheduled erasure (Rule 8)

- Track `last_approach_at` per user: update on login and any user-initiated contact for the specified purpose (Section 8(11): only user-initiated contact counts).
- If in a Third Schedule class (e-commerce >= 2 crore registered users in India, gaming >= 50 lakh, social media >= 2 crore — use Schedule “user” definition): scheduled job erases data 3 years after `max(last_approach_at, rights_exercise_at, rules_commencement)`.
- **48-hour warning** (Rule 8(2)): a job that, at T-48h (or earlier), notifies the user that data will be erased unless she logs in or makes contact. Logging in resets the clock.
- Even outside the Third Schedule, Section 8(7)(a) requires erasure once the specified purpose is no longer served — adopt a documented retention schedule per data category.

### Erasure vs legal-hold vs 1-year floor (decision table)

| Data slice | On consent withdrawal / purpose end / user erase request | Basis |
| --- | --- | --- |
| Profile / purpose data with no legal hold | **Erase** across DB, caches, indexes, object storage, processors | Section 8(7), 12(3) |
| Slice required by another law (tax, KYC/PMLA, labour, litigation hold) | **Retain** only that slice + documented `retention_exception` code; erase the rest | Section 8(7) proviso / law for the time being in force |
| Processing logs + associated personal data for Seventh Schedule purposes | **Retain >= 1 year** from processing in a segregated, access-restricted compliance store, then erase unless law/notification requires longer | Rule 8(3) |
| Logs needed to detect unauthorised access (security continuity) | **Retain >= 1 year** (unless longer required by law) | Rule 6(1)(e) |

Never block whole-account erasure because logs must be kept — segregate the compliance/security stores.

## 5. Verifiable parental consent (Section 9, Rule 10)

For services processing children's (<18) data:

1. **Age assurance**: capture age/DOB at signup or infer risk; if the user is a child, block processing until parental consent completes.
2. **Parent identification** (Rule 10) — implement at least one:
   - Parent is an existing user with reliable identity+age details on file: link child account to parent account, confirm records (Illustration Cases 1 and 3).
   - Parent is not a user: verify against identity/age details issued by an authorised entity, or a **virtual token** mapped to them — DigiLocker integration is the canonical route (Cases 2 and 4).
3. Record the verifiable-consent event (who consented, verification method, evidence reference) in the consent store.
4. For child accounts, enforce Section 9(3) flags: disable behavioural analytics, profiling and targeted advertising SDKs; disable tracking. Section 9(2): review features for detrimental effects.
5. Check Rule 12 / Fourth Schedule exemptions (healthcare, education, safety tracking, email-only accounts) before building — conditions are narrow and purpose-restricted.

For persons with disability with lawful guardians (Rule 11): verify the guardian's court/authority/committee appointment document before accepting consent.

## 6. Security safeguards baseline (Section 8(5), Rule 6)

Minimum bar (highest penalty tier, Rs 250 crore):

- TLS in transit; encryption at rest for DBs, backups, object storage holding PII.
- Mask/tokenise PII in internal tools, support views and non-prod environments.
- AuthZ on every PII endpoint; least-privilege DB roles; secrets in a vault.
- Audit logging of PII access with >= 1 year retention (Rule 6(1)(e)); monitoring + alerting.
- Tested backups (Rule 6(1)(d)).
- No PII in application logs, URLs, or client-side storage without need — add lint rules/log scrubbers.
- Vendor contracts must bind processors to the same safeguards (Rule 6(1)(f)); Data Processor engagement only under valid contract (Section 8(2)).

## 7. Publication requirements (quick list)

On the website/app, prominently:

- Business contact of DPO or answerable person (Section 8(9), Rule 9) — also in every rights-response.
- Means to exercise rights + required identifiers (Rule 14(1)).
- Grievance response period, max 90 days (Rule 14(3)).
- Privacy notice per Rule 3 (`assets/privacy-notice-template.md`).

## 8. Cross-border transfers (Section 16, Rule 15)

- Default: transfers allowed anywhere except countries the Central Government notifies as restricted (none at Rules publication; check current notifications).
- Comply with any Central Government order about making data available to foreign States (Rule 15) — contractually flow this to foreign processors.
- Sectoral localisation (RBI payment data etc.) still applies (Section 16(2)).
- SDFs: be architecturally ready to pin Government-specified data categories + traffic data to India regions (Rule 13(4)).

## 9. Section 5(2) migration for existing users

For personal data for which the Data Principal gave consent before the commencement of the Act's notice/consent provisions (Section 5(2)):

1. Ship a **migration notice** (email + in-app) covering Rule 3 content: itemised data, purposes, withdrawal/rights/Board links.
2. Record `notice_version=migration-<date>`, channel, and timestamp per principal.
3. Continue processing on the existing basis until withdrawal; on withdrawal, run the same cease-processing + erasure pipeline as for new consents.
4. Do not silently “re-consent” with a pre-ticked banner — if you need fresh consent for a new purpose, ask affirmatively and purpose-scoped.

## 10. Cookies, SDKs and device identifiers

1. Inventory every cookie, pixel, and SDK (tag-manager contents included).
2. Classify **essential** (auth, security, load-balancing) vs **consent-gated** (analytics, ads, personalisation, replay).
3. Block consent-gated tags until the matching purpose is granted; on withdrawal, disable tags and call vendor suppression/deletion APIs.
4. Disclose device/advertising IDs in the Rule 3 notice when used; treat them as personal data when they identify a principal.
5. Keep the inventory in the `sharing_registry` so Section 11(1)(b) responses stay accurate.

## 11. Employment / HR processing (Section 7(i))

1. Map HR purposes that fit Section 7(i) (employment; safeguarding employer from loss/liability such as espionage, trade secrets, IP) separately from consent-based product/marketing uses on staff.
2. Provide an employee/applicant privacy notice (categories, purposes, retention, grievance contact) even when consent is not the ground — best practice supporting Section 8(4) accountability; Section 5 makes notice mandatory only where consent is the basis.
3. Minimise monitoring tools; bind ATS/payroll/benefits/background-check vendors with Section 8(2) contracts; register them for sharing disclosures.
4. Retention: align with labour/tax statutes via the legal-hold map; still honour Rule 6 security and Rule 8(3) log floors where applicable.
5. Rights: implement grievance (Section 13); gate customer-style Section 11/12 automation behind the consent/§7(a) check — escalate pure employment DSARs for human/legal review.

## 12. Building a Consent Manager (Rule 4, First Schedule) — from 13 Nov 2026

Only for products seeking Board registration as a Consent Manager:

1. **Eligibility**: India-incorporated company; net worth >= Rs 2 crore; technical/financial capacity; independent certification of interoperable give/manage/review/withdraw platform against Board standards (Part A).
2. **Privacy by routing**: design so personal data payloads shared between fiduciaries are **not readable** by the CM (Part B item 2) — encrypt for recipients, minimise CM-side PII to account/metadata needed to operate the service.
3. **Records**: store consents given/denied/withdrawn, preceding notices, and sharing events; expose to the principal (incl. machine-readable); retain **>= 7 years** (Part B items 3-4).
4. **No subcontract** of CM obligations; act in a fiduciary capacity toward the principal; enforce conflict-of-interest rules for promoters/directors/KMP (Part B items 6, 8-10).
5. **Publish** promoters, directors, KMP, senior management, every >2% shareholder, and related >2% body-corporate holdings (Part B item 11).
6. **Audit + Board reporting** (Part B item 12); **no control transfer** without prior Board approval (Part B item 13).
7. Register with the Board under Rule 4 before operating; fiduciaries onboarded to your platform must still meet their own Sections 5-6 duties.

## 13. Processor vs fiduciary in integrations

| Pattern | Typical role | Engineering consequence |
| --- | --- | --- |
| Cloud DB / email ESP / SMS gateway used only on your instructions | Data Processor | Section 8(2) contract + Rule 6(1)(f); you remain accountable (Section 8(1)) |
| Analytics/ad SDK that sets its own purposes / cross-site profiles | Often independent Data Fiduciary (or joint) | Purpose + notice/consent may be required for *their* processing; list in sharing registry as fiduciary where appropriate |
| Marketplace seller on your platform | Often separate Data Fiduciary for buyer data they determine | Clear role matrix; do not claim processor status if seller determines purposes |
| “AI API” fine-tuning on customer content for the vendor’s models | Likely fiduciary activity by vendor unless contract forbids and technically prevents | Contract + technical controls; disclose sharing |

Record the chosen role per integration in `sharing_registry.recipient_role`.

## 14. Rule 23 / Seventh Schedule readiness

Maintain a legal-ops runbook: who receives Central Government / authorised-person information orders; how to gather and furnish data in time; how to honour **non-disclosure** orders under Rule 23(2) (suppress principal notification); how to produce SDF-assessment metrics (Seventh Schedule item 3) if requested.

## Rollout priority (greenfield compliance project)

1. Security safeguards baseline (highest penalty, immediate risk) — see "6. Security safeguards baseline" in this guide.
2. Consent data model + capture/withdrawal flows + cookie/SDK gating.
3. Breach runbook + affected-user scoping.
4. Erasure pipeline + retention/legal-hold decision table.
5. Rights APIs (access/correction) with consent/§7(a) scope gate, grievance channel, publications.
6. Section 5(2) migration notice for existing users.
7. Children's data flows if applicable; HR/§7(i) controls if applicable.
8. Consent Manager registration program if building a CM (by 13 Nov 2026).
9. SDF program if notified.

Target full fiduciary compliance before **13 May 2027** (core obligations in force). Consent Manager registration path from **13 Nov 2026**.
