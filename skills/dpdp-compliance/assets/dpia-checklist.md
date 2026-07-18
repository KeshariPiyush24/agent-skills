# Data Protection Impact Assessment (DPIA) Checklist (Section 10(2)(c)(i) of the DPDP Act, Rule 13 of the DPDP Rules)

Mandatory for **Significant Data Fiduciaries**: a DPIA and audit once every 12 months, with a report of significant observations furnished to the Board by the person carrying it out (Rule 13(1)-(2)). Also good practice for any Data Fiduciary before launching high-risk processing.

Per Section 10(2)(c)(i), a DPIA comprises: a description of Data Principal rights, the purposes of processing, assessment and management of risks to Data Principal rights.

## 1. Processing description

- [ ] Product/system in scope; owner; assessment date and assessor
- [ ] Personal data inventory: categories, sources, volumes, whether children's data is processed
- [ ] Purposes of processing, mapped to legal basis (consent per purpose / Section 7 legitimate use)
- [ ] Data flows: collection points, storage locations (incl. regions), internal consumers, Data Processors, other Data Fiduciaries (attach the sharing registry)
- [ ] Retention schedule per data category; erasure mechanisms
- [ ] Cross-border transfers and destinations (Section 16, Rule 15); localisation obligations if notified (Rule 13(4))

## 2. Data Principal rights description (Section 10(2)(c)(i))

For each right, describe how it is operationalised and its usage metrics:

- [ ] Notice and consent (Sections 5-6): notice versions, languages offered, consent capture and withdrawal flows
- [ ] Access (Section 11): DSAR mechanism, average response time
- [ ] Correction/erasure (Section 12): mechanisms, completion metrics
- [ ] Grievance redressal (Section 13): channel, published response period (<= 90 days), volumes and resolution times
- [ ] Nomination (Section 14): mechanism

## 3. Risk assessment

Score each risk (likelihood x impact on Data Principal rights: low/medium/high) and record treatment:

| Risk area | Prompts |
| --- | --- |
| Unauthorised access / breach | Attack surface, access controls, encryption, monitoring (Rule 6); breach readiness (Rule 7) |
| Excessive collection | Data beyond what each purpose needs (Section 6(1)) |
| Unlawful secondary use | Processing beyond specified purposes; analytics/ads without consent |
| Retention beyond need | Missing erasure jobs; orphaned copies in warehouses/backups (Section 8(7), Rule 8) |
| Inaccuracy harming decisions | Where data drives decisions about the principal (Section 8(3)) |
| Children's data harms | Age assurance gaps; tracking/behavioural monitoring/targeted ads at children (Section 9(3)) |
| Processor risk | Vendors without contracts/safeguards (Section 8(2), Rule 6(1)(f)) |
| **Algorithmic risk (Rule 13(3))** | Recommenders, ranking, ML models, automated decisions: do technical measures for hosting/display/sharing of personal data pose risks to Data Principal rights? Document the due-diligence review, test methodology and findings |
| Cross-border risk | Transfers to restricted destinations; foreign-State access (Rule 15) |

## 4. Risk management plan

- [ ] For each medium/high risk: mitigation, owner, deadline
- [ ] Residual-risk acceptance recorded and signed off by the DPO and governing body
- [ ] Follow-up verification date

## 5. Governance sign-off

- [ ] DPO review (based in India, responsible to the board — Section 10(2)(a))
- [ ] Independent data auditor's evaluation (Section 10(2)(b))
- [ ] **Report of significant observations furnished to the Data Protection Board** by the person carrying out the DPIA/audit (Rule 13(2))
- [ ] Next DPIA scheduled within 12 months (Rule 13(1))
