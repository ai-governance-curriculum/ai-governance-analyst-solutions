# Exercise 03 — Inventory Row Schema and Example — Reference Solution

## 1. Solution overview

An AI inventory is the authoritative, machine-readable list of AI systems an
organisation builds, buys, or deploys. It is the artefact an auditor asks for
first and the join key every other governance record (risk register, DPIA,
model card, incident log) points at. If the inventory row schema is weak —
free text where enums belong, URLs where IDs belong, no review cadence — the
rest of the governance programme inherits that weakness.

This solution provides:

- A canonical **row schema** (26 columns) with types, required/optional flags,
  enum values, source-of-truth, and the regulatory or framework clause each
  field serves.
- A valid **JSON Schema (draft 2020-12)** encoding of the same fields,
  short enough to paste into a validator unchanged.
- A fully filled **worked example** for a fictional customer-support
  assistant ("Cora") — presented both as a field/value table and as a JSON
  object that validates against the schema.
- Decision rationale explaining why `status` and `lifecycle_stage` are
  separate, why `eu_ai_act_role` is a first-class column, why review cadence
  is codified in-row, and what the inventory deliberately omits.

The learner's output is correct when (a) every regulated attribute the
AI RMF Map, EU AI Act Articles 25/49/72, and ISO/IEC 42001 Clauses 7–8
require to be *findable* is a column, (b) controlled vocabularies are
enums not free text, and (c) the example row validates against the
schema without manual repair.

## 2. Worked answer or implementation

### 2.1 Row schema

Twenty-six columns. Every row represents **one AI system** (not one model —
see §2.6). `system_id` is the primary key; every other governance system
references rows by `system_id`, not by name.

| # | Field | Type | Required | Enum / format | Source of truth | Framework anchor |
| --- | --- | --- | --- | --- | --- | --- |
| 1 | `system_id` | string (slug) | yes | `^[a-z0-9][a-z0-9-]{2,63}$` | inventory system (assigned at intake) | ISO/IEC 42001 Cl. 7.5 (documented information identification) |
| 2 | `system_name` | string | yes | — | intake form | NIST AI RMF Map 1 (context) |
| 3 | `owner_team` | string | yes | — | intake form | NIST AI RMF Govern 2 (accountability); ISO/IEC 42001 Cl. 5.3 |
| 4 | `owner_contact` | string (email) | yes | RFC 5322 email | intake form | NIST AI RMF Govern 2 |
| 5 | `business_purpose` | string | yes | — (1–2 sentences) | intake form | NIST AI RMF Map 1; EU AI Act Art. 13 (intended purpose) |
| 6 | `status` | enum | yes | `planned` \| `in_dev` \| `in_prod` \| `deprecated` \| `retired` | ticketing system | ISO/IEC 42001 Cl. 8.1 (operational planning and control) |
| 7 | `lifecycle_stage` | enum | yes | `design` \| `data` \| `build` \| `test` \| `deploy` \| `operate` \| `decommission` | tier triage / change-control | NIST AI RMF Map 2; ISO/IEC 42001 Cl. 6.1.4 (AI system lifecycle) |
| 8 | `model_type` | enum | yes | `classical_ml` \| `deep_learning` \| `llm` \| `multimodal` \| `rule_based` \| `hybrid` | intake form | NIST AI RMF Map 2 (categorisation) |
| 9 | `model_provenance` | enum | yes | `first_party` \| `fine_tuned` \| `vendor_api` \| `open_source_hosted` | intake form / vendor documentation | NIST AI RMF Map 4 (components, third-party) |
| 10 | `data_sources` | list[string] | yes | short labels, not raw records | intake form | NIST AI RMF Map 4; EU AI Act Art. 10 (data governance) |
| 11 | `contains_personal_data` | boolean | yes | — | intake form / DPIA | EU AI Act Art. 10(5); ISO/IEC 42001 Cl. 7.5 |
| 12 | `special_category_data_flag` | boolean | yes | — (GDPR Art. 9 categories) | DPIA | EU AI Act Art. 10(5) |
| 13 | `jurisdictions_in_use` | list[enum] | yes | ISO 3166-1 alpha-2 codes, plus `EU` | intake form / deployment config | EU AI Act Art. 2 (scope); NIST AI RMF Map 1 |
| 14 | `eu_ai_act_tier` | enum | yes | `prohibited` \| `high_risk` \| `limited_risk` \| `minimal_risk` \| `gpai` \| `out_of_scope` | tier triage (exercise 02) | EU AI Act Arts. 5, 6, 50, 51 |
| 15 | `eu_ai_act_role` | enum | yes | `provider` \| `deployer` \| `importer` \| `distributor` \| `not_applicable` | tier triage | EU AI Act Art. 25 (actor roles along the value chain) |
| 16 | `high_risk_annex_iii_category` | enum, nullable | conditional | Annex III points 1–8, or `null` | tier triage | EU AI Act Annex III; Art. 6(2) |
| 17 | `gpai_flag` | boolean | yes | — | tier triage | EU AI Act Art. 51 (GPAI classification) |
| 18 | `transparency_obligations_flag` | boolean | yes | — | tier triage | EU AI Act Art. 50 (transparency for certain systems) |
| 19 | `human_oversight_mode` | enum | yes | `human_in_the_loop` \| `human_on_the_loop` \| `human_in_command` \| `none_documented` | intake form | EU AI Act Art. 14; NIST AI RMF Manage 1 |
| 20 | `last_review_date` | date (ISO 8601) | yes | `YYYY-MM-DD` | governance review log | ISO/IEC 42001 Cl. 9.3 (management review); EU AI Act Art. 72 |
| 21 | `next_review_due` | date (ISO 8601) | yes | `YYYY-MM-DD` | governance review log | ISO/IEC 42001 Cl. 9.3; EU AI Act Art. 72 (post-market monitoring cadence) |
| 22 | `link_to_risk_assessment` | string (URI reference) | yes | resolvable identifier | risk register | NIST AI RMF Map 5, Measure 1 |
| 23 | `link_to_model_card` | string (URI reference) | conditional (required if `model_type` is ML/LLM/multimodal) | resolvable identifier | model documentation repo | NIST AI RMF Map 2; EU AI Act Art. 13 |
| 24 | `link_to_dpia` | string (URI reference), nullable | conditional (required if `contains_personal_data` = true) | resolvable identifier | privacy office | EU AI Act Art. 26(9); GDPR Art. 35 (cross-reference) |
| 25 | `incidents_log_link` | string (URI reference) | yes | resolvable identifier | incident management system | EU AI Act Art. 73 (serious incident reporting); NIST AI RMF Manage 4 |
| 26 | `retention_period` | string (ISO 8601 duration) | yes | e.g. `P10Y` | records-management policy | EU AI Act Art. 19 (logs retention); ISO/IEC 42001 Cl. 7.5 |
| 27 | `change_control_owner` | string | yes | team or role name | change-management system | ISO/IEC 42001 Cl. 8.1; NIST AI RMF Govern 1 |

(Count: 27 columns — 26 governance fields plus the `system_id` key. The
exercise asks for ~20–30; this sits near the middle.)

### 2.2 JSON Schema (draft 2020-12)

Paste into any draft 2020-12 validator unchanged. Enums mirror §2.1
exactly.

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "https://example.org/schemas/ai-inventory-row.json",
  "title": "AI Inventory Row",
  "type": "object",
  "required": [
    "system_id", "system_name", "owner_team", "owner_contact",
    "business_purpose", "status", "lifecycle_stage", "model_type",
    "model_provenance", "data_sources", "contains_personal_data",
    "special_category_data_flag", "jurisdictions_in_use",
    "eu_ai_act_tier", "eu_ai_act_role", "gpai_flag",
    "transparency_obligations_flag", "human_oversight_mode",
    "last_review_date", "next_review_due", "link_to_risk_assessment",
    "incidents_log_link", "retention_period", "change_control_owner"
  ],
  "properties": {
    "system_id":    { "type": "string", "pattern": "^[a-z0-9][a-z0-9-]{2,63}$" },
    "system_name":  { "type": "string", "minLength": 1 },
    "owner_team":   { "type": "string", "minLength": 1 },
    "owner_contact":{ "type": "string", "format": "email" },
    "business_purpose": { "type": "string", "minLength": 10 },
    "status": {
      "type": "string",
      "enum": ["planned", "in_dev", "in_prod", "deprecated", "retired"]
    },
    "lifecycle_stage": {
      "type": "string",
      "enum": ["design", "data", "build", "test", "deploy", "operate", "decommission"]
    },
    "model_type": {
      "type": "string",
      "enum": ["classical_ml", "deep_learning", "llm", "multimodal", "rule_based", "hybrid"]
    },
    "model_provenance": {
      "type": "string",
      "enum": ["first_party", "fine_tuned", "vendor_api", "open_source_hosted"]
    },
    "data_sources": {
      "type": "array",
      "items": { "type": "string", "minLength": 1 },
      "minItems": 1
    },
    "contains_personal_data":     { "type": "boolean" },
    "special_category_data_flag": { "type": "boolean" },
    "jurisdictions_in_use": {
      "type": "array",
      "items": { "type": "string", "pattern": "^(EU|[A-Z]{2})$" },
      "minItems": 1
    },
    "eu_ai_act_tier": {
      "type": "string",
      "enum": ["prohibited", "high_risk", "limited_risk", "minimal_risk", "gpai", "out_of_scope"]
    },
    "eu_ai_act_role": {
      "type": "string",
      "enum": ["provider", "deployer", "importer", "distributor", "not_applicable"]
    },
    "high_risk_annex_iii_category": {
      "type": ["string", "null"],
      "enum": [
        "annex_iii_1_biometrics",
        "annex_iii_2_critical_infrastructure",
        "annex_iii_3_education",
        "annex_iii_4_employment",
        "annex_iii_5_essential_services",
        "annex_iii_6_law_enforcement",
        "annex_iii_7_migration_asylum_border",
        "annex_iii_8_justice_democracy",
        null
      ]
    },
    "gpai_flag":                    { "type": "boolean" },
    "transparency_obligations_flag":{ "type": "boolean" },
    "human_oversight_mode": {
      "type": "string",
      "enum": ["human_in_the_loop", "human_on_the_loop", "human_in_command", "none_documented"]
    },
    "last_review_date":        { "type": "string", "format": "date" },
    "next_review_due":         { "type": "string", "format": "date" },
    "link_to_risk_assessment": { "type": "string", "format": "uri-reference" },
    "link_to_model_card":      { "type": ["string", "null"], "format": "uri-reference" },
    "link_to_dpia":            { "type": ["string", "null"], "format": "uri-reference" },
    "incidents_log_link":      { "type": "string", "format": "uri-reference" },
    "retention_period":        { "type": "string", "pattern": "^P(?!$)(\\d+Y)?(\\d+M)?(\\d+D)?(T(\\d+H)?(\\d+M)?(\\d+S)?)?$" },
    "change_control_owner":    { "type": "string", "minLength": 1 }
  },
  "allOf": [
    {
      "if":  { "properties": { "eu_ai_act_tier": { "const": "high_risk" } }, "required": ["eu_ai_act_tier"] },
      "then":{ "required": ["high_risk_annex_iii_category"] }
    },
    {
      "if":  { "properties": { "contains_personal_data": { "const": true } }, "required": ["contains_personal_data"] },
      "then":{ "required": ["link_to_dpia"] }
    },
    {
      "if":  {
        "properties": {
          "model_type": { "enum": ["classical_ml", "deep_learning", "llm", "multimodal"] }
        },
        "required": ["model_type"]
      },
      "then":{ "required": ["link_to_model_card"] }
    }
  ],
  "additionalProperties": false
}
```

### 2.3 Worked example — "Cora" customer-support assistant

**Scenario (carried forward from exercises 01 and 02).** Cora is an
LLM-backed customer-support assistant. The organisation integrates a
third-party foundation model via API (vendor is the GPAI provider; the
organisation is the **deployer**). Cora is embedded in a web chat on the
customer portal in the EU and the UK. It discloses to the user that
they are speaking to an AI — a limited-risk transparency obligation —
and a human agent takes over on escalation. No Annex III use case
applies. Personal data (name, email, order history) is processed; no
special-category data.

#### 2.3.1 Field/value table

| Field | Value |
| --- | --- |
| `system_id` | `cora-support-assistant` |
| `system_name` | Cora Customer Support Assistant |
| `owner_team` | Customer Experience Platform |
| `owner_contact` | cx-platform-lead@example.com |
| `business_purpose` | Answer tier-1 customer questions about orders, shipping, and returns; escalate to a human agent when confidence is low or the customer asks. |
| `status` | `in_prod` |
| `lifecycle_stage` | `operate` |
| `model_type` | `llm` |
| `model_provenance` | `vendor_api` |
| `data_sources` | `["order_history_db", "shipping_status_api", "returns_policy_kb"]` |
| `contains_personal_data` | `true` |
| `special_category_data_flag` | `false` |
| `jurisdictions_in_use` | `["EU", "GB"]` |
| `eu_ai_act_tier` | `limited_risk` |
| `eu_ai_act_role` | `deployer` |
| `high_risk_annex_iii_category` | `null` |
| `gpai_flag` | `false` (Cora is not itself a GPAI model; it is a downstream deployment of one) |
| `transparency_obligations_flag` | `true` |
| `human_oversight_mode` | `human_on_the_loop` |
| `last_review_date` | `2026-09-15` |
| `next_review_due` | `2027-03-15` |
| `link_to_risk_assessment` | `rra://risk-register/cora-support-assistant/v3` |
| `link_to_model_card` | `mc://model-registry/vendor-x-foundation-v2/deployment/cora` |
| `link_to_dpia` | `dpia://privacy-office/cora-support-assistant/v2` |
| `incidents_log_link` | `inc://incidents/system/cora-support-assistant` |
| `retention_period` | `P6Y` |
| `change_control_owner` | CX Platform Change Advisory Board |

#### 2.3.2 JSON object (validates against §2.2)

```json
{
  "system_id": "cora-support-assistant",
  "system_name": "Cora Customer Support Assistant",
  "owner_team": "Customer Experience Platform",
  "owner_contact": "cx-platform-lead@example.com",
  "business_purpose": "Answer tier-1 customer questions about orders, shipping, and returns; escalate to a human agent when confidence is low or the customer asks.",
  "status": "in_prod",
  "lifecycle_stage": "operate",
  "model_type": "llm",
  "model_provenance": "vendor_api",
  "data_sources": ["order_history_db", "shipping_status_api", "returns_policy_kb"],
  "contains_personal_data": true,
  "special_category_data_flag": false,
  "jurisdictions_in_use": ["EU", "GB"],
  "eu_ai_act_tier": "limited_risk",
  "eu_ai_act_role": "deployer",
  "high_risk_annex_iii_category": null,
  "gpai_flag": false,
  "transparency_obligations_flag": true,
  "human_oversight_mode": "human_on_the_loop",
  "last_review_date": "2026-09-15",
  "next_review_due": "2027-03-15",
  "link_to_risk_assessment": "rra://risk-register/cora-support-assistant/v3",
  "link_to_model_card": "mc://model-registry/vendor-x-foundation-v2/deployment/cora",
  "link_to_dpia": "dpia://privacy-office/cora-support-assistant/v2",
  "incidents_log_link": "inc://incidents/system/cora-support-assistant",
  "retention_period": "P6Y",
  "change_control_owner": "CX Platform Change Advisory Board"
}
```

### 2.4 Decision rationale

- **`status` and `lifecycle_stage` are separate columns.** `status` tracks
  the *business* state of the system (is it live? has it been retired?)
  and is owned by product/ticketing. `lifecycle_stage` tracks where in
  the AI lifecycle the system currently sits (are we re-training? re-testing?)
  and is owned by the change-control process. A system can be `in_prod`
  (status) and `test` (lifecycle_stage) simultaneously — e.g., live traffic
  shadow-tested against a candidate model. Collapsing them hides that case.
- **`eu_ai_act_role` is first-class, not derived.** Article 25 obligations
  differ sharply between provider, deployer, importer, and distributor.
  The same model can be deployed by one legal entity and provided by
  another; the inventory row must say which role *this* organisation plays
  for *this* system. Without it, the AI Act column cannot drive workflow.
- **Review cadence lives in the row.** `last_review_date` and
  `next_review_due` let a daily cron job produce an "overdue reviews"
  report without joining to a separate calendar. EU AI Act Article 72
  (post-market monitoring) and ISO/IEC 42001 Clause 9.3 both presume a
  recurring review; codifying the due date in-row is the cheapest way to
  evidence it.
- **Links are URI references, not plain URLs.** The schema accepts
  `uri-reference`, which permits scheme-qualified internal identifiers
  (`rra://…`, `dpia://…`). This keeps the inventory stable when the web
  front-end of the risk register is replaced; the identifier survives.
- **`gpai_flag` is a system attribute, not an inherited vendor property.**
  For Cora, `gpai_flag` is **false**: Cora is a downstream deployment of
  a GPAI model, not itself a GPAI model. The vendor's GPAI classification
  belongs on the vendor's row (or the organisation's vendor-model
  sub-inventory), not on Cora's.
- **Deliberate omissions.** The inventory row does **not** carry: full
  risk-register content (treatment decisions, residual risk — those live
  in the risk register, linked by `link_to_risk_assessment`); model
  weights hashes or training-data snapshots (belong on the model card);
  the names or records of individuals whose personal data is processed
  (belong in the processing system, flagged here and linked via DPIA);
  free-text change history (belongs in the change-control system). The
  inventory is a *pointer table*, not a document store.

## 3. Validation steps

1. **Schema validates.** Load the JSON Schema from §2.2 into a draft
   2020-12 validator (e.g. `ajv`, `jsonschema`). The validator must
   return no errors on the schema itself.
2. **Example validates against schema.** Run the JSON object from §2.3.2
   through the validator with the §2.2 schema. Expected: zero errors.
3. **Conditional `allOf` fires correctly.** Change
   `contains_personal_data` to `true` and remove `link_to_dpia`:
   validation must now fail with a "required property missing" error on
   `link_to_dpia`. Restore the field and re-validate; it must pass.
   Repeat for `eu_ai_act_tier: high_risk` without
   `high_risk_annex_iii_category`, and for `model_type: llm` without
   `link_to_model_card`.
4. **Enums reject typos.** Replace `"status": "in_prod"` with
   `"status": "production"`. Validation must fail with an enum
   violation. Restore and re-validate.
5. **Primary key format.** Replace `system_id` with `"Cora Support"`
   (contains uppercase and space). Validation must fail on the pattern.
6. **Date fields.** Replace `last_review_date` with `"15/09/2026"`.
   Validation must fail on `format: date`.
7. **Retention duration regex.** Replace `retention_period` with
   `"6 years"`. Validation must fail. `"P6Y"` must pass.
8. **Derivability from intake.** For each required field, trace it back
   to a specific question on the exercise-01 intake form or a decision
   output of the exercise-02 tier triage. If any required field has no
   source upstream, either add a question to intake or demote the field
   to optional.
9. **Framework coverage sweep.** Confirm at least one column maps to
   each of: NIST AI RMF Map 1, Map 4, Measure 1, Manage 4; EU AI Act
   Articles 25, 50, 51, 72; ISO/IEC 42001 Clauses 7.5, 8.1, 9.3.

## 4. Rubric or review checklist

Score each dimension 0–3. A passing schema averages ≥ 2.0 across
dimensions with no 0s.

| Dimension | 3 — strong | 2 — acceptable | 1 — weak | 0 — fail |
| --- | --- | --- | --- | --- |
| **Field coverage — regulatory** | All EU AI Act actor-role, tier, Annex III, transparency, GPAI, oversight, and post-market fields present as columns. | One of the above is captured only indirectly (e.g., inferred from free text). | Two or more regulatory attributes missing or only in prose. | AI Act role or tier not represented. |
| **Field coverage — ownership** | Owner team, owner contact, change-control owner all present and distinguishable. | Two of three present. | Only one present; or all collapsed into "owner". | No human is nameable for the system from the row. |
| **Field coverage — risk linkage** | Separate resolvable links to risk assessment, model card, DPIA, incident log. | Three of four present. | Two of four present. | Inventory is a leaf (no outbound links). |
| **Enums vs free text** | Status, lifecycle_stage, model_type, model_provenance, eu_ai_act_tier, eu_ai_act_role, Annex III category, human_oversight_mode are all enums. | One of those is free text. | Two are free text. | Three or more are free text, or tier is free text. |
| **Schema/example consistency** | Example JSON validates against the schema; every required field is populated with a schema-legal value. | Example validates after one trivial fix (e.g., a typo). | Example fails validation on two or more fields. | Example contradicts the schema's enums or types. |
| **Machine-readability** | JSON Schema is valid draft 2020-12, uses `$schema`, `$id`, formats, and conditional `allOf`/`if`/`then`. | Valid draft 2020-12 without conditional logic. | Draft-07 or earlier, or missing `$schema`. | Not valid JSON Schema. |
| **Review cadence** | `last_review_date` and `next_review_due` are columns, with a documented default interval tied to tier. | Both columns present, no documented interval. | Only one of the two present. | No review cadence captured in-row. |

Binary deck-level checks (all must pass):

- [ ] `system_id` is unique, machine-safe, and used as the join key by every linked system.
- [ ] No personal data of data subjects is stored in the inventory itself.
- [ ] Every link column is a resolvable identifier, not a raw UI URL.
- [ ] `eu_ai_act_role` is a required enum, not inferred.
- [ ] High-risk rows require `high_risk_annex_iii_category` (enforced in schema).

## 5. Common mistakes

- **Free-text columns where enums belong.** `status`, `eu_ai_act_tier`,
  and `eu_ai_act_role` written as prose make the inventory unsortable
  and unfilterable, and guarantee that two analysts will spell the same
  value three different ways. Make them enums and let the schema enforce
  it.
- **Storing a URL instead of a resolvable identifier.** A link like
  `https://wiki.example.org/pages/view?id=19283` breaks the first time
  the wiki is migrated. A scheme-qualified identifier
  (`rra://risk-register/<system_id>/<version>`) is resolved by a thin
  adapter and survives tool changes.
- **Conflating "model" with "AI system".** One inventory row per *AI
  system* (user-visible capability). Vendor model versions live on the
  model card linked from `link_to_model_card`; a model swap is a change
  event on the existing row, not a new row.
- **Missing jurisdiction.** Without `jurisdictions_in_use`, you cannot
  answer "is this system in scope of the EU AI Act?" from the
  inventory. Capture it as a list of ISO 3166-1 codes (plus `EU` as a
  shorthand), not as free text like "Europe".
- **No review cadence in-row.** Review calendars in a separate tool
  drift out of sync with the inventory. `last_review_date` and
  `next_review_due` columns make "what is overdue?" a one-line query.
- **Storing personal data in the inventory.** The inventory is read by
  every reviewer and auditor; it is not the right system for personal
  data about customers or employees. Replace the data itself with
  `contains_personal_data` + `special_category_data_flag` + a link to
  the DPIA.
- **Using `gpai_flag` to mean "uses a GPAI model".** The flag is about
  whether *this system* is itself a GPAI model under Article 51, which
  is almost always only true for the model provider. Deployer rows that
  wrap a GPAI model should have `gpai_flag: false` and rely on
  `model_provenance: vendor_api` plus the vendor's own inventory to
  carry the GPAI designation.

## 6. References

- NIST, *AI Risk Management Framework (AI RMF 1.0)* — **Map 1**
  (context / intended purpose / jurisdictions) and **Map 4**
  (components, including third-party), used to anchor the ownership,
  jurisdiction, data-source, and provenance columns.
  <https://www.nist.gov/itl/ai-risk-management-framework>
- NIST, *AI RMF Playbook* — expanded suggested actions for Map 1 and
  Map 4, used to justify capturing provenance and jurisdiction as
  structured fields rather than free text.
  <https://www.nist.gov/itl/ai-risk-management-framework>
- Regulation (EU) 2024/1689 (the **EU AI Act**):
  - **Article 25** — obligations along the AI value chain (provider,
    deployer, importer, distributor); anchors the `eu_ai_act_role`
    column.
  - **Article 49** — registration of high-risk AI systems in the EU
    database; the inventory row is the organisation's internal record
    aligned to that registration.
  - **Article 72** — post-market monitoring by providers; anchors
    `last_review_date` and `next_review_due`.
  - Also referenced: Articles 10 (data governance), 13 (transparency
    and information to deployers), 14 (human oversight), 19 (logs
    retention), 50 (transparency obligations for certain systems), 51
    (GPAI classification), 73 (serious-incident reporting), and Annex
    III (high-risk categories).
  <https://eur-lex.europa.eu/eli/reg/2024/1689/oj>
- ISO/IEC 42001:2023, *Information technology — Artificial
  intelligence — Management system* — **Clause 7.5** (documented
  information), **Clause 8.1** (operational planning and control),
  **Clause 9.3** (management review). Anchors the identification,
  change-control, and review-cadence columns.
  <https://www.iso.org/standard/81230.html>
