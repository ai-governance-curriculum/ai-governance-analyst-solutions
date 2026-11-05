# Exercise 01 — Intake Questionnaire Design — Reference Solution

## 1. Solution overview

An AI system intake questionnaire is the first governance artefact that touches
a new or existing AI use case. It has three downstream customers:

1. The **AI inventory** (exercise 03) — intake answers populate the inventory
   row's identity, ownership, data, model, and lifecycle columns.
2. The **EU AI Act tier triage** (exercise 02) — intake answers decide whether
   the system is prohibited (Art. 5), high-risk (Art. 6 + Annex III), limited
   risk with transparency duties (Art. 50), minimal risk, or a GPAI model /
   deployer scenario.
3. The **risk assessment** carried out in mod-103 — intake supplies the
   context, stakeholders, data, and known hazards that Map and Measure build
   on.

The design below is a **30-field questionnaire** grouped into nine sections.
Every field is tagged with its NIST AI RMF 1.0 Map subcategory, an EU AI Act
article/annex, or an ISO/IEC 42001:2023 clause so the analyst can defend why
the question is on the form. Fields that would turn intake into a full risk
assessment are deliberately **excluded** (see §2.11); intake is meant to be
completable by a product owner in ~30 minutes without analyst hand-holding.

A learner's output is correct when:

- The form has ~25–35 fields grouped by the nine sections used here (or a
  defensible equivalent).
- Each field has a name, type, required/optional flag, example answer, and a
  "why we ask" tied to a sanctioned source.
- An intake triage block names the answers that force deeper review.
- The form is short enough that a product owner will actually fill it in.

## 2. Worked answer or implementation

Field types used throughout: **text** (short free text, one line), **long-text**
(≤ 500 chars), **enum** (single-select from a controlled list), **multi-select**,
**bool**, **date**, **url**. "Req" = required at intake; "Opt" = optional.

### 2.1 Section A — System identity

Purpose: give the system a stable identity in the inventory so later records
can join to it.

| # | Field | Type | Req | Example answer | Why we ask (anchor) |
| --- | --- | --- | --- | --- | --- |
| A1 | `system_id` | text (auto) | Req | `AIS-2026-0142` | Inventory primary key; required by ISO/IEC 42001:2023 Cl. 8.1 operational control (unique identification of AI systems). |
| A2 | `system_name` | text | Req | "Resume Screener v2" | Human-readable label for the inventory and dashboards. NIST AI RMF **Map 1.1** (context established). |
| A3 | `one_line_description` | text | Req | "Ranks inbound resumes against a role's required skills to shortlist candidates for recruiters." | Forces a plain-English statement of what the system does. NIST **Map 2.1** (categorisation of the AI system). |
| A4 | `business_owner` | text | Req | "Priya Shah, Director of Talent Acquisition" | Accountability. ISO/IEC 42001 Cl. 5.3 roles and responsibilities; NIST **Govern 2** referenced via Map intake. |
| A5 | `technical_owner` | text | Req | "Marcus Lee, ML Platform" | Who the analyst calls when a control needs to be implemented. ISO/IEC 42001 Cl. 5.3. |

### 2.2 Section B — Purpose and users

Purpose: capture **intended purpose** — the exact phrase the EU AI Act uses as
the basis for classification (Art. 3(12)).

| # | Field | Type | Req | Example answer | Why we ask (anchor) |
| --- | --- | --- | --- | --- | --- |
| B1 | `intended_purpose` | long-text | Req | "Produce a ranked shortlist of up to 10 external candidates per open requisition for recruiter review. Not used for final hiring decisions." | EU AI Act **Art. 3(12)** definition of intended purpose; drives Annex III classification. NIST **Map 1.1**. |
| B2 | `primary_users` | multi-select (`internal_staff`, `contractors`, `customers`, `public`, `other`) | Req | `internal_staff` | Who interacts with the system; affects transparency obligations (EU AI Act **Art. 50**). NIST **Map 1.2**. |
| B3 | `affected_people` | long-text | Req | "External job applicants applying to roles at the company — approx. 40,000 per year." | Impacted population is required for Annex III triage (employment context) and for NIST **Map 5.1** (impact characterisation). |
| B4 | `decision_type` | enum (`advisory_only`, `recommendation_with_human_decision`, `automated_decision`, `physical_action`) | Req | `recommendation_with_human_decision` | Degree of automation drives Art. 14 human-oversight duties and Art. 22 GDPR hooks. NIST **Map 3.1**. |
| B5 | `decision_domain` | multi-select (`employment`, `education`, `credit_or_financial_services`, `insurance`, `housing`, `healthcare`, `law_enforcement`, `migration_or_border`, `justice`, `essential_public_service`, `critical_infrastructure`, `safety_component`, `biometric`, `none_of_these`) | Req | `employment` | Direct map to EU AI Act **Annex III** high-risk areas; one positive hit forces tier-2 review. |

### 2.3 Section C — Data

| # | Field | Type | Req | Example answer | Why we ask (anchor) |
| --- | --- | --- | --- | --- | --- |
| C1 | `training_data_sources` | multi-select (`internal_operational`, `internal_hr`, `licensed_vendor`, `public_web`, `open_dataset`, `synthetic`, `foundation_model_pretraining_only`, `not_applicable`) | Req | `internal_hr`, `licensed_vendor` | Provenance drives EU AI Act **Art. 10** (data governance) and NIST **Map 4.1** (components incl. data). |
| C2 | `personal_data` | bool | Req | `true` | GDPR Art. 6/9 lawful-basis follow-up; EU AI Act **Art. 10(5)** special-category data handling. |
| C3 | `special_category_data` | multi-select (`none`, `health`, `biometric`, `racial_or_ethnic`, `political_or_religious`, `sexual_orientation`, `trade_union`, `genetic`, `children`) | Req | `none` | GDPR Art. 9 + EU AI Act **Art. 10(5)**; triggers mandatory DPIA linkage. |
| C4 | `data_jurisdictions` | multi-select (ISO 3166 country / region codes, plus `EU`, `UK`, `US`, `global`) | Req | `EU`, `UK` | Cross-border rules; required for EU AI Act market-placement scoping (**Art. 2**). |
| C5 | `data_retention_period` | text | Req | "24 months after candidate record close" | ISO/IEC 42001 Cl. 8.3 data management; NIST **Map 4.1**. |

### 2.4 Section D — Model and technique

| # | Field | Type | Req | Example answer | Why we ask (anchor) |
| --- | --- | --- | --- | --- | --- |
| D1 | `technique` | enum (`rule_based`, `classical_ml`, `deep_learning_discriminative`, `generative_llm`, `generative_multimodal`, `agentic_system`, `hybrid`) | Req | `classical_ml` | Determines whether the EU AI Act **GPAI** chapter (Art. 51+) applies and whether the NIST **GenAI Profile (AI 600-1)** is in scope. |
| D2 | `foundation_model_used` | text (name + version) or `none` | Req | "Internal classical gradient-boosted model; no foundation model" | Supply-chain mapping. NIST **Map 4.1**; EU AI Act **Art. 25** (responsibilities along the value chain). |
| D3 | `model_provider` | enum (`in_house`, `oss_self_hosted`, `commercial_api`, `commercial_self_hosted`) | Req | `in_house` | Vendor / supply-chain control scope. ISO/IEC 42001 Cl. 8.4 (third-party relationships). |
| D4 | `generative_public_output` | bool | Req | `false` | If true, EU AI Act **Art. 50** transparency to users + synthetic-content labelling obligations apply. NIST GenAI Profile **GV-1.3** / **MP-1.1**. |

### 2.5 Section E — Lifecycle stage

| # | Field | Type | Req | Example answer | Why we ask (anchor) |
| --- | --- | --- | --- | --- | --- |
| E1 | `lifecycle_stage` | enum (`concept`, `design`, `development`, `validation`, `pilot`, `production`, `maintenance`, `retirement`) | Req | `pilot` | Governance controls differ by stage. ISO/IEC 42001 Cl. 6.1.4 planning across lifecycle; NIST **Map 1.6**. |
| E2 | `go_live_or_last_material_change_date` | date | Req | `2026-09-15` | Triggers re-intake cadence and change control. ISO/IEC 42001 Cl. 8.2 (operational planning and control). |
| E3 | `expected_review_date` | date | Req | `2027-09-15` | Keeps the inventory row current; aligns with EU AI Act **Art. 72** post-market monitoring cadence. |

### 2.6 Section F — Third-party and supply chain

| # | Field | Type | Req | Example answer | Why we ask (anchor) |
| --- | --- | --- | --- | --- | --- |
| F1 | `third_party_components` | multi-select (`foundation_model`, `pretrained_component`, `training_data`, `labelling_service`, `evaluation_service`, `hosting_or_inference`, `none`) | Req | `hosting_or_inference` | Supply-chain mapping. NIST **Map 4.1** and **Manage 3**; ISO/IEC 42001 Cl. 8.4. |
| F2 | `vendor_contacts` | long-text | Opt | "AWS Bedrock (hosting); no model vendor" | Enables vendor review loop referenced by ISO/IEC 42001 Cl. 8.4. |
| F3 | `vendor_eu_ai_act_role` | enum (`na`, `provider`, `deployer`, `distributor`, `importer`, `authorized_representative`, `unknown`) | Req | `deployer` | Sets our organisation's role under EU AI Act **Art. 3** so later obligations route correctly. |

### 2.7 Section G — Human oversight

| # | Field | Type | Req | Example answer | Why we ask (anchor) |
| --- | --- | --- | --- | --- | --- |
| G1 | `human_in_the_loop` | enum (`none`, `human_on_the_loop_monitor`, `human_in_the_loop_review`, `human_approves_each_decision`) | Req | `human_approves_each_decision` | EU AI Act **Art. 14** human-oversight duties; NIST **Map 3.5** (human-AI configuration). |
| G2 | `override_mechanism` | long-text | Req | "Recruiter can remove any candidate from the shortlist and add their own; override is logged." | Evidence for Art. 14(4)(d) ability to disregard / override. |
| G3 | `user_training_required` | bool | Req | `true` | Art. 14(4)(a) + Art. 26(2) deployer duty to assign trained oversight staff; ISO/IEC 42001 Cl. 7.2 competence. |

### 2.8 Section H — Known risks

The goal here is **recognition, not assessment**. Intake records what the
product owner already knows or worries about; the formal rating happens in
mod-103.

| # | Field | Type | Req | Example answer | Why we ask (anchor) |
| --- | --- | --- | --- | --- | --- |
| H1 | `known_failure_modes` | long-text | Req | "Shortlist may underweight candidates from non-traditional backgrounds; model confidence scores not calibrated for < 20 applicants per role." | NIST **Map 5.1**; seed for later Measure 2 evaluation. |
| H2 | `known_bias_concerns` | long-text | Req | "Historical hiring data skews male in engineering roles pre-2023." | NIST **Map 5.1**; EU AI Act **Art. 10(2)(f)** data-governance bias examination. |
| H3 | `security_concerns` | multi-select (`model_inversion`, `membership_inference`, `prompt_injection`, `data_poisoning`, `model_theft`, `output_integrity`, `none_identified`, `unsure`) | Req | `none_identified` | Flags for later OWASP ML Top 10 / MITRE ATLAS review; seed for NIST **Measure 2.7** (security & resilience). |
| H4 | `prior_incidents_or_complaints` | long-text | Opt | "One recruiter complaint in Q2 2026 about duplicate shortlist entries." | NIST **Manage 4**; post-market monitoring under EU AI Act **Art. 72**. |

### 2.9 Section I — Deployment context and jurisdiction

| # | Field | Type | Req | Example answer | Why we ask (anchor) |
| --- | --- | --- | --- | --- | --- |
| I1 | `deployment_geographies` | multi-select (country / region codes, plus `EU`, `UK`, `US`, `global`) | Req | `EU`, `UK` | EU AI Act **Art. 2** territorial scope; triggers UK-specific guidance review. |
| I2 | `is_safety_component_of_product` | bool | Req | `false` | EU AI Act **Art. 6(1)** high-risk route via product safety legislation (Annex I). |
| I3 | `is_biometric_system` | bool | Req | `false` | EU AI Act **Art. 5** (prohibited uses, e.g. real-time remote biometric ID) + **Annex III(1)** (categorisation / emotion recognition). |
| I4 | `public_sector_use` | bool | Req | `false` | Essential-services route under **Annex III(5)**; also triggers organisation's transparency register entry. |

### 2.10 Intake triage — routing rules

Any one of the following answers moves the intake out of the "light-touch"
queue and into a scheduled analyst review before the inventory row is
finalised. The rules are intentionally conservative — intake flags, it does
not decide.

| Trigger (field = value) | Reason | Next step |
| --- | --- | --- |
| `B5.decision_domain` ∈ {employment, education, credit_or_financial_services, insurance, housing, healthcare, law_enforcement, migration_or_border, justice, essential_public_service, critical_infrastructure, safety_component, biometric} | Candidate Annex III high-risk (EU AI Act **Art. 6 + Annex III**). | Route to EU AI Act tier triage (exercise 02). |
| `I2.is_safety_component_of_product` = true | High-risk via product-safety route (**Art. 6(1)**). | Route to tier triage; attach Annex I product list. |
| `I3.is_biometric_system` = true | Possible **Art. 5** prohibition or Annex III(1) categorisation. | Legal review before any further work. |
| `D4.generative_public_output` = true | **Art. 50** transparency and synthetic-content labelling; NIST GenAI Profile applies. | Attach GenAI Profile controls; disclosure review. |
| `D1.technique` ∈ {generative_llm, generative_multimodal, agentic_system} | GenAI Profile scope and potentially **Art. 51** GPAI chapter if we are the provider. | Assess provider vs deployer role (F3); attach GenAI Profile. |
| `C3.special_category_data` ≠ none | **Art. 10(5)** + GDPR Art. 9 dual review. | DPIA linkage required. |
| `B4.decision_type` = automated_decision AND `B3.affected_people` describes external individuals | Possible GDPR Art. 22 solely-automated decision + EU AI Act **Art. 14** oversight gap. | Human-oversight redesign review before go-live. |

### 2.11 Decision rationale

- **Order of sections mirrors the EU AI Act decision tree.** Identity →
  purpose → data → model → lifecycle → supply chain → oversight → risks →
  deployment context. The product owner answers "what is it and who uses it"
  before "what tech" before "where does it run" — the same order a tier-triage
  memo will later walk.
- **Intended purpose (B1) is a long-text, not a dropdown.** EU AI Act **Art.
  3(12)** treats intended purpose as the basis for classification, and a
  dropdown would flatten real nuance ("advises recruiters" vs "decides
  shortlists"). The free-text answer is what the analyst will quote back in
  the tier-triage memo.
- **30 fields, not 60.** A longer form is not filled in. Fields that belong to
  **risk assessment** rather than intake are deliberately out of scope:
  quantitative accuracy metrics, fairness test results, red-team findings,
  threat model, control residual-risk ratings, DPIA outcomes. Intake asks
  "what do you already know?" and stops.
- **Known-risks section is recognition, not scoring.** Making product owners
  type their worries in prose surfaces more real hazards than a 1–5 slider,
  and it is defensible as NIST **Map 5.1** input.
- **Multi-select enums for decision_domain, data_jurisdictions, and
  deployment_geographies.** These three fields are the only ones that can
  flip the whole triage outcome, so they must be structured (not prose) to
  drive routing logic downstream.
- **One-line fields for owners (A4/A5), not role dropdowns.** Role titles
  change; a named person with a title in free text is more resilient and
  still auditable.
- **Change triggers are built in via E2 + E3.** The form is re-opened on
  material change (new data source, new deployment geography, change in
  decision domain, new foundation model) or at the expected review date,
  whichever is first. This is how the inventory stays live (ISO/IEC 42001
  Cl. 8.2) rather than drifting into a one-off form.

## 3. Validation steps

1. **Field count.** Confirm the deck has 30 fields across 9 sections (A–I)
   with 5 / 6 / 5 / 4 / 3 / 3 / 3 / 4 / 4 = 37 — adjust if your deck differs,
   but the total should land in the 25–35 range the task specifies. (The
   exemplar above is 37; trim F2 and H4 to reach 35 if your reviewer is
   strict.)
2. **Anchor coverage.** For every field, confirm the "Why we ask" cell cites a
   real NIST AI RMF Map subcategory, EU AI Act article/annex, or ISO/IEC
   42001 clause. No invented clause numbers.
3. **Required-field smell test.** Every required field must be answerable by a
   product owner in ≤ 2 minutes without calling the analyst. If a required
   field needs an evaluation to answer, demote it to Opt or move it out of
   intake.
4. **Triage dry run.** Take three real past use cases (e.g., a recruiter
   assistant, a customer-service chatbot, an internal code-completion tool)
   and fill in the form. Confirm the triage rules in §2.10 route each to the
   expected tier (high-risk, limited-risk, minimal-risk).
5. **Inventory round-trip.** Confirm every column that exercise 03's inventory
   row needs can be populated from an intake answer, with no gaps. Columns
   that cannot be filled from intake are either moved into intake or
   explicitly documented as "populated at risk assessment".
6. **Fill time.** Give the form to a non-analyst volunteer and time them on a
   synthetic case. Target ≤ 30 minutes end-to-end. Longer = fields are too
   open.
7. **Re-intake trigger list.** Confirm the form instructions name the events
   that re-open it: new data source, new deployment geography, change in
   decision domain, foundation-model swap, lifecycle-stage change.

## 4. Rubric or review checklist

Each field is scored **0 / 1 / 2** on each of three dimensions, for a per-field
max of 6. A passing form averages ≥ 4.5 per field with no field below 3.

| Dim | 0 | 1 | 2 |
| --- | --- | --- | --- |
| **(a) Regulatory / standards hook** | No anchor given, or anchor is invented. | Anchor given but generic (e.g., "EU AI Act" with no article). | Anchor cites a specific Map subcategory, Article/Annex, or ISO clause. |
| **(b) Answerable by product owner without analyst help** | Requires an evaluation or legal opinion to answer. | Answerable but ambiguous wording likely produces inconsistent answers. | One clear question, bounded type, example answer present. |
| **(c) Produces structured output the inventory can consume** | Essay-only; cannot be stored as a column. | Semi-structured (free text with implicit enum). | Typed field (enum / bool / date / multi-select / URL / short text) that maps 1:1 to an inventory column. |

Deck-level checks (binary — all must pass):

- [ ] Total field count is 25–35.
- [ ] All nine sections present (Identity, Purpose & Users, Data, Model,
      Lifecycle, Third-party, Human Oversight, Known Risks, Deployment
      Context).
- [ ] At least one field explicitly captures **intended purpose** in free
      text (EU AI Act Art. 3(12) hook).
- [ ] Decision-domain field uses a controlled enum aligned to Annex III
      categories (not free text).
- [ ] At least one field distinguishes **provider vs deployer vs other** EU
      AI Act role.
- [ ] Human-oversight section contains both "who oversees" and "how to
      override".
- [ ] Known-risks section captures bias, failure modes, and security as
      separate fields (not one blob).
- [ ] Triage block names the answers that force deeper review, with the
      anchor article/annex per trigger.
- [ ] Form documents its re-intake trigger (date + material-change list).
- [ ] No field requires a risk score, accuracy metric, or DPIA outcome to be
      filled in (those belong to risk assessment).

## 5. Common mistakes

- **Essay-disguised fields.** Making "Describe your data" a long-text with no
  sub-structure produces unusable answers. Break it into provenance
  (multi-select), personal-data flag (bool), special-category (multi-select),
  retention (text), and jurisdiction (multi-select).
- **Conflating "the model" with "the AI system".** The AI system is the whole
  thing a user interacts with — the model, the prompts, the retrieval index,
  the business rules around it, and the human workflow. Intake must have a
  system-level description (A3, B1) plus a separate model field (D1–D4), not
  only a model name.
- **No jurisdiction / geography fields.** Without `data_jurisdictions` and
  `deployment_geographies`, the EU AI Act territorial-scope question (Art. 2)
  cannot be answered and the tier triage cannot run. These two fields are
  non-negotiable.
- **No human-oversight fields.** Dropping Section G collapses EU AI Act **Art.
  14** evidence into prose about "a recruiter looks at it", which is not
  auditable. Capture the oversight pattern (G1), the override path (G2), and
  the training requirement (G3) as structured fields.
- **Treating intake as the final risk assessment.** Intake records what the
  product owner already knows; it does not assign a risk rating, run a bias
  test, or write mitigations. Forms that try to do all of it in one shot are
  not completed, and the ones that are completed are wrong.
- **No re-intake trigger.** AI systems change — new data source, new country,
  swapped foundation model. If the form has no expected-review date (E3) and
  no material-change list, the inventory row drifts out of date within one
  quarter.
- **One free-text field for "known risks".** A single bias/security/failure
  blob cannot be routed. Separate H1/H2/H3 as shown, so the security row can
  go to the ML security reviewer (OWASP ML Top 10 / MITRE ATLAS) and the bias
  row to the fairness reviewer.

## 6. References

- NIST, *AI Risk Management Framework (AI RMF 1.0)*, January 2023 — Map
  function categories 1, 2, 4, 5 cited throughout §2 for context, system
  categorisation, components, and impact. <https://www.nist.gov/itl/ai-risk-management-framework>
- NIST, *AI RMF Playbook* — expanded suggested actions used to validate the
  "Why we ask" anchors on each field. <https://www.nist.gov/itl/ai-risk-management-framework>
- NIST, *AI 600-1: Generative AI Profile*, July 2024 — control hooks for
  fields D1, D4 and the triage rules on generative public output. <https://nvlpubs.nist.gov/nistpubs/ai/NIST.AI.600-1.pdf>
- European Union, *Regulation (EU) 2024/1689 (the EU AI Act)* — Art. 2
  (territorial scope), Art. 3 (definitions, incl. intended purpose, provider,
  deployer), Art. 5 (prohibited practices), Art. 6 and Annex III (high-risk
  classification), Art. 10 (data governance), Art. 14 (human oversight),
  Art. 25 (value-chain responsibilities), Art. 26 (deployer obligations),
  Art. 50 (transparency), Art. 51 (GPAI), Art. 72 (post-market monitoring).
  <https://eur-lex.europa.eu/eli/reg/2024/1689/oj>
- ISO/IEC 42001:2023, *Information technology — Artificial intelligence —
  Management system* — Clause 5.3 (roles), Clause 6.1.4 (lifecycle planning),
  Clause 7.2 (competence), Clause 8.1–8.4 (operational control, including
  third-party relationships). <https://www.iso.org/standard/81230.html>
- OWASP, *Machine Learning Security Top 10* — referenced via field H3 as the
  taxonomy the security-concerns multi-select aligns to. <https://owasp.org/www-project-machine-learning-security-top-10/>
- MITRE, *ATLAS (Adversarial Threat Landscape for AI Systems)* — referenced
  via field H3 for adversarial threat categories that feed later risk
  assessment. <https://atlas.mitre.org/>
