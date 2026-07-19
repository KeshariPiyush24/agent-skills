# Consent Record Schema (Section 6 of the DPDP Act; notice versioning links to Rule 3)

A reference data model for provable, purpose-scoped consent. Section 6(10) places the burden of proof on the Data Fiduciary: you must be able to show that notice was given and consent obtained. Rule 3 governs notice content — store `notice_version` so each grant maps to the exact Rule 3 notice shown. Adapt names/types to the target stack.

## Design rules

1. **Append-only event log** — never mutate or delete consent events; derive current state.
2. **Purpose-scoped** — one grant/withdrawal per purpose, not a global boolean.
3. **Notice-versioned** — every grant references the exact notice version and language shown.
4. Retain consent records at least as long as you process the data plus your limitation period; Consent Managers must keep records 7 years (First Schedule Part B) — a good default benchmark.

## Tables

### consent_events (append-only)

| Column | Type | Notes |
| --- | --- | --- |
| id | uuid pk | |
| data_principal_id | fk -> users | The individual (or child) the data is about |
| purpose | enum/text | e.g. `service_delivery`, `marketing_email`, `marketing_sms`, `analytics`, `personalization`, `third_party_sharing` |
| personal_data_items | text[] / jsonb | Data items covered by this grant (supports Section 6(1) necessity / minimisation proof), e.g. `["email","phone"]` |
| action | enum | `granted`, `withdrawn`, `denied` |
| notice_version | text | Version of the privacy notice shown (Rule 3) |
| notice_language | text | Language the notice/request was presented in (Section 6(3)) |
| mechanism | text | `signup_form`, `settings_toggle`, `consent_manager`, `parental_flow` |
| consent_manager_id | text nullable | If given via a registered Consent Manager (Section 6(7)) |
| given_by | enum | `self`, `parent`, `lawful_guardian`, `nominee` |
| guardian_verification_id | fk nullable | Link to parental/guardian verification evidence (Rule 10/11) |
| ip_address | inet | Capture context for proof |
| user_agent | text | |
| occurred_at | timestamptz | |

### consent_state (derived/materialised)

| Column | Type | Notes |
| --- | --- | --- |
| data_principal_id + purpose | pk | |
| status | enum | `active`, `withdrawn`, `never_granted` |
| granted_at / withdrawn_at | timestamptz | |
| latest_event_id | fk | |

### guardian_verifications (Rule 10/11 evidence)

| Column | Type | Notes |
| --- | --- | --- |
| id | uuid pk | |
| child_principal_id | fk | |
| guardian_identity_method | enum | `held_details` (existing verified user), `voluntary_details`, `virtual_token` (e.g. DigiLocker), `court_appointment_doc` (Rule 11) |
| evidence_ref | text | Pointer to verification artifact/token id — do not store raw ID documents unless necessary |
| verified_adult | boolean | Rule 10 due-diligence result |
| verified_at | timestamptz | |

### sharing_registry (supports Section 11(1)(b) access requests)

| Column | Type | Notes |
| --- | --- | --- |
| id | pk | |
| recipient_name | text | Vendor/partner (Data Processor or other Data Fiduciary) |
| recipient_role | enum | `data_processor`, `data_fiduciary` |
| data_categories | text[] | What is shared |
| purpose | text | Why |
| contract_ref | text | DPA/contract identifier (Section 8(2), Rule 6(1)(f)) |
| active | boolean | |

## Withdrawal side-effects (Section 6(6), Section 8(7))

On `withdrawn` event, an async worker must:

1. Disable the purpose's processing (feature flags, suppression lists).
2. Notify each `sharing_registry` recipient / call processor deletion or suppression APIs; record acknowledgements.
3. Evaluate erasure: delete data collected solely for that purpose unless a documented legal retention exception applies, honouring the Rule 8(3) one-year log retention floor in a segregated store.

## Proof query (Section 6(10))

You must be able to answer: "Show that notice version N was presented to principal P in language L, and P (or her parent/guardian) affirmatively granted purpose X at time T via mechanism M, and it was not later withdrawn." — a single indexed query over `consent_events`.
