# DPDP Compliance Audit Checklist

How to audit a codebase/product against the DPDP Act, 2023 and DPDP Rules, 2025. Work through every section. For each item record: **status** (compliant / gap / not-applicable / needs-human-review), **evidence** (file:line or absence of code), and **fix**. Rank findings Critical / High / Medium / Low, guided by the penalty exposure noted per section.

## Step 0 — Scope determination

Before auditing, establish:

1. **Does the Act apply?** (Section 3) — Is digital personal data processed in India, or outside India in connection with offering goods/services to Data Principals in India? Purely personal/domestic processing and data made publicly available by the Data Principal are out of scope.
2. **Role map** (per processing activity, not per company):
   - **Data Fiduciary** — determines purpose and means (most product companies for their own users).
   - **Data Processor** — processes only on behalf of a fiduciary under a valid contract (Section 8(2)).
   - **Joint / multi-party**: marketplaces, analytics SDKs, ad-tech, and AI APIs may be processors for some flows and independent fiduciaries for others — classify per purpose; do not assume one role for the whole stack.
3. **Exemptions** (Section 17): outsourcing processing of non-India Data Principals under contract (17(1)(d)), legal claims, courts, law enforcement, mergers, loan-default assessment, research/archiving/statistics per Second Schedule, and possible startup exemptions (17(3)). **Do not assume a startup is exempt** until a Central Government notification under Section 17(3) actually covers it.
4. **SDF status**: Has the entity been (or is it likely to be) notified as a Significant Data Fiduciary? If yes, include Section G.
5. **Third Schedule class**: Is it an e-commerce entity (>= 2 crore registered users in India), online gaming intermediary (>= 50 lakh), or social media intermediary (>= 2 crore)? Use the Third Schedule **“user”** definition (not just logged-in accounts). If yes, Rule 8(1) 3-year erasure clock applies.
6. **Product type flags**: (a) building a **Consent Manager** → include Section J; (b) processes **employee/applicant** data → include Section K; (c) web/app with non-essential cookies/SDKs → include Section L.

## Step 1 — Map personal-data touchpoints

Search the codebase for:

- **Collection points**: signup/login forms, checkout, KYC flows, contact forms, surveys, file uploads, mobile SDK inits, chat widgets
- **Storage**: DB schemas/migrations with columns like name, email, phone, address, dob, gender, aadhaar, pan, location, ip_address, device_id; object storage; caches; search indexes
- **Third parties**: analytics (GA, Mixpanel, Amplitude), ads, crash reporting, payment gateways, email/SMS providers, CRMs, cloud AI APIs, tag managers
- **Logging**: log statements, request logging middleware, error trackers — look for PII in logs
- **Flows out**: webhooks, exports, partner APIs, data warehouse ETL, backups, cross-border transfers (region config of cloud resources)
- **Identifiers**: cookies, localStorage/sessionStorage PII, device fingerprinting, advertising IDs, tracking pixels, SDK init configs
- **HR/employee systems** (if present): ATS, HRIS, payroll vendors, badge/access logs, monitoring/productivity tools

Useful greps: `email|phone|mobile|address|aadhaar|pan_number|date_of_birth|dob|gender|latitude|ip_address`, analytics SDK imports (`gtag|mixpanel|amplitude|segment|hotjar|clarity`), cookie consent libs, `console.log|logger.` near user objects, DB migration files, HRIS/payroll vendor names.

## Section A — Grounds, notice and consent (Sections 4-6, Rule 3) — penalty tier: Rs 50 crore (other provisions)

| # | Check | What to look for in code |
| --- | --- | --- |
| A1 | Every processing purpose rests on consent or a Section 7 legitimate use | Inventory purposes (service delivery, marketing, analytics, ads); flag any without a ground. Marketing and third-party analytics almost always need consent |
| A2 | Notice precedes/accompanies every consent request (Section 5(1), Rule 3) | Signup/onboarding UI shows an itemised description of personal data + purposes + links for withdrawal, rights and Board complaint, standalone and in plain language |
| A3 | Notice for pre-commencement processing (Section 5(2)) | Concrete migration: email/in-app notice to existing users covering itemised data + purposes + withdrawal/rights/Board links; continue-until-withdraw behaviour documented; notice versioning for the migration wave |
| A4 | Notice/consent available in English + Eighth Schedule languages (Sections 5(3), 6(3)) | i18n/localisation support for the notice and consent screens (22 scheduled languages) |
| A5 | Consent is free, specific, informed, unconditional, unambiguous, affirmative (Section 6(1)) | No pre-ticked boxes, no bundled consent, no consent walls for unnecessary data; purpose-scoped consent flags rather than a single boolean |
| A6 | Data minimisation — consent limited to necessary data (Section 6(1)) | Permissions requested by mobile apps (contacts, location) vs actual need; optional vs required form fields; consent record lists data items per purpose |
| A7 | Withdrawal as easy as giving (Section 6(4), Rule 3(c)) | A working "withdraw consent" UI reachable in as few steps as the grant; not buried in support email |
| A8 | On withdrawal, processing ceases incl. processors (Section 6(6)) | Withdrawal handler propagates to downstream processors/vendors (event, API call, suppression list) |
| A9 | Consent records provable (Section 6(10) — burden of proof on fiduciary) | Persistent consent log: who, when, what purposes, data items, notice version, mechanism, IP/user-agent; immutable/auditable |
| A10 | Consent Manager interoperability as a Data Fiduciary (Section 6(7)-(9), Rule 4) | If accepting CM-routed consent: honour give/manage/withdraw from registered Consent Managers (post-Nov 2026). If **building** a CM product, use Section J instead |

## Section B — Security safeguards (Section 8(5), Rule 6) — penalty tier: Rs 250 crore (highest)

| # | Check | What to look for |
| --- | --- | --- |
| B1 | Encryption/obfuscation/masking/tokenisation of personal data (Rule 6(1)(a)) | TLS everywhere; encryption at rest (DB, backups, object storage); masking of PII in UI/support tools; tokenisation of high-risk identifiers |
| B2 | Access control on computer resources (Rule 6(1)(b)) | AuthN/AuthZ on all endpoints handling PII; RBAC/least privilege for internal tools and DB access; secrets not hardcoded |
| B3 | Access logging, monitoring, review (Rule 6(1)(c)) | Audit logs on PII access; alerting for anomalous access; log review process |
| B4 | Continuity: backups and recovery (Rule 6(1)(d)) | Backup jobs, tested restores, ransomware resilience |
| B5 | Security-continuity log/data retention >= 1 year (Rule 6(1)(e)) | Log retention config >= 1 year for detecting unauthorised access; distinct from Rule 8(3) Seventh Schedule store |
| B6 | Processor contracts mandate safeguards (Rule 6(1)(f), Section 8(2)) | DPAs/contracts with vendors handling PII (needs-human-review if contracts not in repo) |
| B7 | Organisational measures (Rule 6(1)(g), Section 8(4)) | Security policy, dependency scanning, secret scanning, code review gates |

Also flag classic issues since they evidence inadequate safeguards: PII in logs, PII in URLs/query strings, unencrypted PII in localStorage, SQL injection surface, missing rate limits on auth, public S3 buckets, verbose error responses.

## Section C — Breach readiness (Section 8(6), Rule 7) — penalty tier: Rs 200 crore

| # | Check | What to look for |
| --- | --- | --- |
| C1 | Breach detection capability | Monitoring/alerting that could actually detect unauthorised access (ties to B3) |
| C2 | User notification mechanism | Ability to message affected users without delay via user account or registered channel (in-app + email/SMS) with the Rule 7(1) content |
| C3 | Board notification runbook | Incident-response doc covering: immediate description to the Board, and the 72-hour detailed report (events, causes, mitigation, culprit findings, remediation, user-intimation report) |
| C4 | Affected-user identification | Ability to query which Data Principals were affected by a given system compromise |

## Section D — Retention and erasure (Sections 8(7)-(8), 12(3), Rule 8) — penalty tier: Rs 50 crore

| # | Check | What to look for |
| --- | --- | --- |
| D1 | Erasure on consent withdrawal or purpose exhaustion (Section 8(7)(a)) | Deletion pipeline triggered by withdrawal/account deletion; covers DB, caches, search indexes, object storage, analytics, backup expiry policy |
| D2 | Processor erasure (Section 8(7)(b)) | Deletion propagates to vendors (API calls, deletion requests) |
| D3 | Retention exceptions documented | Legal holds (tax, KYC/PMLA, employment law) explicitly mapped per data category — not a blanket "keep everything". See implementation-guide decision table |
| D4 | Third Schedule 3-year clock (Rule 8(1)) — large e-commerce/gaming/social only | Inactivity tracking (last-approach timestamp); scheduled erasure job after 3 years; thresholds use Schedule “user” definition |
| D5 | 48-hour pre-erasure notice (Rule 8(2)) | Notification job that warns users at least 48h before scheduled erasure |
| D6 | 1-year minimum retention of data + logs for Seventh Schedule purposes (Rule 8(3)) | Segregated compliance store; deletion jobs must NOT purge these logs before 1 year from processing even when profile data is erased |
| D7 | Erasure vs legal-hold vs Rule 8(3) resolved in code | Pipeline branches: erase profile/purpose data; retain legal-hold subset with documented basis; retain 1-year Seventh Schedule logs with restricted access |

## Section E — Data Principal rights (Sections 11-14, Rules 9, 14) — penalty tier: Rs 50 crore

Sections 11-12 rights apply where the Data Principal previously gave consent (including consent as referred to in Section 7(a)). Do not assume a universal DSAR API is required for every Section 7 legitimate-use basis (e.g. pure employment processing under Section 7(i)) without checking that gate.

| # | Check | What to look for |
| --- | --- | --- |
| E1 | Access: summary of data + processing activities (Section 11(1)(a)) | Data-export/DSAR endpoint or process producing a human-readable summary |
| E2 | Access: identities of all fiduciaries/processors data was shared with (Section 11(1)(b)) | A maintained vendor/sharing registry that the DSAR response can enumerate — most codebases miss this |
| E3 | Correction, completion, updating (Section 12(2)) | Profile edit capabilities or a request channel covering all stored PII, not just profile fields |
| E4 | Erasure on request (Section 12(3)) | User-initiated account/data deletion (see D1) |
| E5 | Rights-request means published (Rule 14(1)) | Website/app prominently documents how to exercise rights and what identifiers are needed |
| E6 | Grievance redressal <= 90 days, published (Section 13, Rule 14(3)) | Grievance channel, published response period, ticket tracking to meet it |
| E7 | Nomination (Section 14, Rule 14(4)) | Mechanism (even manual) for nominating another individual for death/incapacity |
| E8 | Contact person/DPO published (Section 8(9), Rule 9) | Website/app shows business contact info of DPO or responsible person; included in every rights-request response |

## Section F — Children's data (Section 9, Rules 10-12) — penalty tier: Rs 200 crore

| # | Check | What to look for |
| --- | --- | --- |
| F1 | Is the service likely to process children's (<18) data? | Age gates, age declarations at signup; audience of the product |
| F2 | Verifiable parental consent before processing (Section 9(1), Rule 10) | Parent identification flow using reliable held details, or voluntarily provided identity/age details, or a virtual token/Digital Locker — a bare "I am over 18" checkbox is NOT sufficient for known children |
| F3 | No detriment to child well-being (Section 9(2)) | Content/feature review (needs-human-review) |
| F4 | No tracking, behavioural monitoring or targeted ads at children (Section 9(3)) | Analytics/ads SDKs disabled or gated for child accounts; no profiling of minors |
| F5 | Exemption fit (Rule 12, Fourth Schedule) | Healthcare, education, crèche, transport-safety, email-account, real-time-location-safety carve-outs — verify conditions strictly |
| F6 | Guardian consent for persons with disability (Rule 11) | Due diligence that guardian is court/authority/committee-appointed |

## Section G — Significant Data Fiduciary (Section 10, Rule 13) — penalty tier: Rs 150 crore

Only if notified as SDF (or planning for it):

| # | Check | What to look for |
| --- | --- | --- |
| G1 | DPO appointed, based in India, reports to board, is grievance contact (Section 10(2)(a)) | Published DPO details |
| G2 | Independent data auditor (Section 10(2)(b)) | Engagement in place |
| G3 | Annual DPIA + audit, report to Board (Rule 13(1)-(2)) | DPIA artifacts (see `assets/dpia-checklist.md`) |
| G4 | Algorithmic due diligence (Rule 13(3)) | Documented review that recommender systems/ML pipelines processing personal data don't risk Data Principal rights |
| G5 | Localisation of notified data categories (Rule 13(4)) | Ability to pin specified data + traffic data to India regions |

## Section H — Cross-border transfer (Section 16, Rule 15) — penalty tier: Rs 50 crore

| # | Check | What to look for |
| --- | --- | --- |
| H1 | Inventory of transfers outside India | Cloud regions, SaaS vendor locations, CDN, analytics endpoints |
| H2 | No transfers to Government-restricted countries (Section 16(1)) | Watch for notified restrictions (none notified as of the Rules' publication; verify current status) |
| H3 | Rule 15 orders on data availability to foreign States | Contract terms with foreign processors on government access requests (needs-human-review) |
| H4 | Sectoral localisation still applies (Section 16(2)) | RBI payments data localisation etc. for fintech |

## Section I — Data quality and accountability (Section 8(1), (3), (4))

| # | Check | What to look for |
| --- | --- | --- |
| I1 | Accuracy/completeness/consistency where data drives decisions or is disclosed onward (Section 8(3)) | Validation on ingestion; update propagation to downstream consumers |
| I2 | Processor contracts exist for all vendors (Section 8(2)) | Vendor list vs contract register (needs-human-review) |
| I3 | Fiduciary accountability regardless of processor fault (Section 8(1)) | Vendor due-diligence/monitoring process |
| I4 | Role classification documented per integration | Marketplace sellers, analytics SDKs, ad partners, AI APIs tagged processor vs independent fiduciary; contracts match the tag |

## Section J — Building a Consent Manager (Section 6(9), Rule 4, First Schedule) — from 13 Nov 2026

Only if the product **is** (or aims to be) a registered Consent Manager. Fiduciaries that merely *consume* CM consent use A10, not this section.

| # | Check | What to look for |
| --- | --- | --- |
| J1 | India company + net worth >= Rs 2 crore (First Schedule Part A) | Incorporation docs / financial eligibility (needs-human-review) |
| J2 | Interoperable platform certified to Board standards (Part A item 9) | Platform architecture for give/manage/review/withdraw; certification path |
| J3 | Personal data contents **not readable** by the CM (Part B item 2) | Encryption/blind routing so CM cannot read shared personal data payloads |
| J4 | Consent/notice/sharing records retained >= 7 years; machine-readable export (Part B items 3-4) | Append-only record store + DSAR-style export for principals |
| J5 | No subcontract/assignment of CM obligations (Part B item 6) | Architecture and vendor contracts forbid outsourcing core CM duties |
| J6 | Fiduciary capacity + conflict-of-interest controls (Part B items 8-10) | Policies; MoA/AoA clauses; KMP/director interest checks |
| J7 | Transparency disclosures published (Part B item 11) | Website/app lists promoters, directors, KMP, >2% shareholders, related body-corporate holdings |
| J8 | Periodic audit/reporting to the Board (Part B item 12) | Audit mechanisms and reporting pipeline |
| J9 | Control transfers need prior Board approval (Part B item 13) | Corporate/M&A process flag (needs-human-review) |
| J10 | Security safeguards to prevent personal data breach (Part B item 7; Rule 6 analogue) | Same bar as Section B for any personal data the CM does process (account data, metadata) |

## Section K — Employment and HR processing (Section 7(i))

For employers / HR products processing employees, contractors, or applicants for purposes of employment or those relating to safeguarding the employer from loss or liability (e.g. prevention of corporate espionage, maintenance of confidentiality of trade secrets, intellectual property):

| # | Check | What to look for |
| --- | --- | --- |
| K1 | Section 7(i) scope not stretched to marketing/product analytics on employees | Purpose inventory separates employment ops from consent-based uses |
| K2 | Employee/applicant notice (even where consent is not the ground) | Onboarding/privacy notice covering categories, purposes, rights that still apply, grievance contact |
| K3 | Monitoring / productivity / access-badge / email tools minimised | Only what employment/safeguarding purpose needs; vendors under contract (Section 8(2)) |
| K4 | HR vendor map + deletion/retention | ATS, payroll, benefits, background-check vendors in sharing registry; retention mapped to labour/tax law holds |
| K5 | Rights gating correct | Do not auto-apply Sections 11-12 DSAR patterns built for consent/§7(a) customers to pure §7(i) employment files without legal review — grievance (Section 13) and security/erasure duties still apply where relevant |

## Section L — Cookies, SDKs and device identifiers (Sections 4-6, Rule 3)

| # | Check | What to look for |
| --- | --- | --- |
| L1 | Non-essential cookies/SDKs gated on consent | Analytics, ads, personalisation, session replay blocked until purpose-scoped consent |
| L2 | Essential vs non-essential split documented | Auth/session/security cookies justified; not used as a blanket exemption for marketing pixels |
| L3 | Device IDs / advertising IDs treated as personal data when they identify | Collection disclosed in notice; withdrawal disables collection and suppresses vendors |
| L4 | Tag manager / multiple pixels inventoried | GTM (or equivalent) contents match sharing registry; no undeclared third parties |

## Section M — Government information calls (Rule 23, Seventh Schedule) — readiness

| # | Check | What to look for |
| --- | --- | --- |
| M1 | Legal/security contact path for MeitY/authorised-person orders | Runbook for furnishing information within the ordered period |
| M2 | Non-disclosure capability (Rule 23(2)) | Ability to suppress notice to the Data Principal when an order cites sovereignty/security and forbids disclosure |
| M3 | SDF-assessment data availability (Seventh Schedule item 3) | Can produce volume/sensitivity/risk metrics if asked for SDF evaluation |

## Report format

Produce the audit report as:

```markdown
# DPDP Compliance Audit — <product> — <date>

Status of law: obligations enforceable from 13 May 2027 (consent-manager provisions 13 Nov 2026).

## Summary
<counts by severity; top 3 risks>

## Findings

### [CRITICAL] <title> — Section/Rule <ref> (penalty exposure: up to Rs <amount> crore)
- Evidence: `path/file.ext:line` — <what was found or what is missing>
- Requirement: <what the law requires, quoted/paraphrased>
- Fix: <concrete engineering fix>

... (repeat, ordered by severity)

## Not applicable / out of scope
<items and why>

## Needs human review
<contracts, legal holds, SDF notification status, etc.>

---
This is technical compliance guidance, not legal advice. Consult qualified counsel for legal decisions.
```

Severity guide: Critical = Rs 200-250 crore exposure or active harm (security gaps, breach unreadiness, children's data violations); High = Rs 150 crore or systemic gaps (no consent records, no erasure pipeline); Medium = Rs 50 crore items (missing DSAR fields, notice defects); Low = documentation/publication gaps easy to fix.
