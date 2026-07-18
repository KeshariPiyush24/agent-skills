# DPDP Implementation Guide

Engineering patterns for building DPDP-compliant features. Before implementing anything: read the target repository's own conventions (framework, ORM, auth model, package manager) and follow them — the snippets here are framework-neutral pseudocode to adapt, not to paste. Cite the satisfied Section/Rule in code comments and docs.

## 1. Consent management (Sections 5-6, Rule 3)

### Data model

Use `assets/consent-record-schema.md` for the full schema. Core principles:

- **Purpose-scoped**: one consent record per (data principal, purpose), never a single "accepts privacy policy" boolean. Typical purposes: `service_delivery`, `marketing_email`, `marketing_sms`, `analytics`, `personalization`, `third_party_sharing`.
- **Versioned notice**: store the notice version (and language) shown when consent was captured — Section 6(10) puts the burden of proof on you.
- **Append-only**: record grants and withdrawals as immutable events; derive current state.

### Capture flow

1. Show the Rule 3 notice: itemised data description, purposes, and links to withdraw / exercise rights / complain to the Board. Standalone, plain language, available in English + Eighth Schedule languages the product supports (Section 5(3)).
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
- If in a Third Schedule class (e-commerce >= 2 crore users, gaming >= 50 lakh, social media >= 2 crore): scheduled job erases data 3 years after `max(last_approach_at, rights_exercise_at, rules_commencement)`.
- **48-hour warning** (Rule 8(2)): a job that, at T-48h (or earlier), notifies the user that data will be erased unless she logs in or makes contact. Logging in resets the clock.
- Even outside the Third Schedule, Section 8(7)(a) requires erasure once the specified purpose is no longer served — adopt a documented retention schedule per data category.
- Retention floor: keep processing logs + personal data 1 year (Rule 8(3), Rule 6(1)(e)) in the segregated compliance store.

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

## Rollout priority (greenfield compliance project)

1. Security safeguards baseline (highest penalty, immediate risk) — Section 6 above.
2. Consent data model + capture/withdrawal flows.
3. Breach runbook + affected-user scoping.
4. Erasure pipeline + retention schedule.
5. Rights APIs (access/correction), grievance channel, publications.
6. Children's data flows if applicable.
7. SDF program if notified.

Target full compliance before **13 May 2027** (core obligations in force).
