# Exercise 01 — Regulatory Tracker Schema — Reference Solution

## 1. Solution overview

A regulatory tracker is the analyst's source of truth for every AI-relevant
law, rule, guidance, enforcement action, or standard the organisation
has decided to monitor. Its purpose is **not** to contain legal
interpretation — the legal and compliance teams own that — but to let
anyone on the governance team answer four questions in under a minute:

1. What rule are we tracking?
2. Does it apply to us (which jurisdictions, which systems)?
3. What do we owe by when?
4. Who is on the hook for each obligation, and what's the status?

A good schema is boring on purpose. It names the fields, the controlled
vocabularies, and the required cross-links (to the system inventory, the
risk register, the control catalogue). Done badly, each row is a
free-text blob that no two analysts write the same way; done well, the
tracker produces diffable records a council can sign off on and an
auditor can reproduce.

This solution provides:

- A field-by-field schema (YAML + JSON Schema) for a `regulation`
  record and a child `obligation` record.
- A worked instance for three items — the **EU AI Act** (Regulation
  (EU) 2024/1689), **NYC Local Law 144 of 2021** (AEDT), and the
  **NIST AI RMF 1.0** (non-binding standard, tracked for alignment).
- Controlled-vocabulary lists for the enumerated fields.
- Decision rationale and a grading rubric.

Scope: this is a design + implementation exercise. The schema is a
reference; a production tracker would sit on top of the organisation's
existing GRC tool and use these fields as a contract, not as a
schema-of-record.

## 2. Worked answer or implementation

### 2.1 Entity model

Two linked entities:

- **`regulation`** — the top-level record for an instrument (a law,
  rule, guidance document, standard, case, or enforcement action).
- **`obligation`** — a specific duty derived from the instrument that
  the organisation might owe. One regulation produces many obligations.

Both carry a stable `id`, provenance, status, and a cross-link to the
AI system inventory (see `mod-102`). Obligations additionally carry
the internal owner, control mapping (see `mod-108`), and due date.

```
regulation (1) ──── has ────▶ (N) obligation
      │
      └── applies_to_systems ──▶ (N) ai_system  (FK to inventory)
```

### 2.2 `regulation` record — YAML schema

```yaml
regulation:
  # --- Identification ---
  id: string                          # internal stable slug, e.g. "eu-ai-act-2024"
  short_name: string                  # "EU AI Act"
  full_citation: string               # "Regulation (EU) 2024/1689 of 13 June 2024"
  instrument_type: enum               # see §2.6 controlled vocabularies
  issuing_body: string                # "European Parliament and Council of the EU"
  jurisdictions: [enum]               # ISO 3166 country codes, plus "EU", "US-STATE-*",
                                      # "US-FEDERAL", "UK", "CA-PROV-*", etc.
  sector_scope: [enum]                # "cross-sector", "financial-services",
                                      # "healthcare", "employment", "insurance", ...
  extraterritorial: boolean           # does it bind entities outside the home jurisdiction?
  extraterritorial_basis: string|null # cite the article/section that extends jurisdiction

  # --- Status in the instrument's own lifecycle ---
  lifecycle_stage: enum               # see §2.6
  adopted_date: date|null             # formal adoption / signing
  effective_date: date|null           # when duties begin to bind
  key_milestone_dates:                # per-obligation phase-ins live here
    - name: string
      date: date
      obligation_refs: [string]       # which obligation records this milestone governs

  # --- Source & provenance ---
  primary_source_url: string          # link to authoritative text (official journal, etc.)
  implementing_rules_urls: [string]   # delegated / implementing acts, agency rules
  official_guidance_urls: [string]    # regulator FAQs, technical standards
  last_checked_date: date             # most recent review by the analyst
  last_checked_by: string             # role of the reviewer

  # --- Internal posture ---
  tracking_status: enum               # see §2.6 (monitor-only / in-scope / active-work / out-of-scope)
  applicability_decision: string      # one-paragraph rationale for the status above
  applies_to_systems: [string]        # FK to ai_system.id from the inventory
  internal_owner: string              # role name (not team); e.g. "Head of AI Governance"
  legal_point_of_contact: string|null # role in Legal that owns interpretation

  # --- Risk / change signal ---
  change_log:                         # append-only
    - date: date
      event: string                   # "amendment adopted", "Art. 5 guidance published", ...
      source_url: string
      recorded_by: string             # role
  open_questions: [string]            # unresolved interpretation questions
  next_review_date: date              # analyst's next scheduled check

  # --- References ---
  references:
    nist_ai_rmf_mapping: [string]     # e.g. ["GOVERN 1.1", "MAP 4.1"] — cross-link to RMF
    iso_42001_mapping: [string]       # AIMS clauses, where applicable
    related_regulation_ids: [string]  # other regulation records (e.g. GDPR ↔ EU AI Act)
```

### 2.3 `obligation` record — YAML schema

```yaml
obligation:
  id: string                          # "eu-ai-act-art-6-high-risk-classification"
  regulation_id: string               # FK to regulation.id
  citation: string                    # "Art. 6" or "Art. 6(1) and Annex III(4)"
  summary: string                     # one sentence, analyst-written, not a quote
  obligation_type: enum               # see §2.6
  bearer: enum                        # who owes the duty: "provider" / "deployer" / "importer" /
                                      # "distributor" / "authorised-representative" /
                                      # "controller" / "processor" / "employer" / "other"
  triggered_when: string              # trigger condition in one sentence
  evidence_required: [string]         # artefact types ("technical documentation",
                                      # "conformity assessment", "bias audit summary")

  # --- Timing ---
  start_date: date|null
  recurrence: enum|null               # "one-off", "annual", "on-change", "on-incident"
  deadline_rule: string|null          # e.g. "10 business days before AEDT use"

  # --- Internal ownership and control mapping ---
  internal_owner: string              # role, not team
  secondary_owner: string|null
  mapped_controls: [string]           # IDs from the control catalogue (mod-108)
  mapped_risk_ids: [string]           # IDs from the risk register (mod-103)

  # --- Status ---
  status: enum                        # "not-started" / "in-progress" / "met" /
                                      # "not-applicable" / "blocked" / "retired"
  status_evidence: string|null        # link to evidence artefact
  last_reviewed_date: date
  next_review_date: date

  # --- Penalty / escalation signal ---
  penalty_summary: string|null        # "up to 7% global annual turnover or €35M"
                                      # for Art. 5 prohibited practices under Reg. (EU) 2024/1689
  escalation_trigger: string|null     # condition that forces executive-committee routing
```

### 2.4 Worked instance — EU AI Act (regulation)

```yaml
id: eu-ai-act-2024
short_name: EU AI Act
full_citation: >
  Regulation (EU) 2024/1689 of the European Parliament and of the Council
  of 13 June 2024 laying down harmonised rules on artificial intelligence
  (Artificial Intelligence Act).
instrument_type: regulation
issuing_body: European Parliament and Council of the European Union
jurisdictions: [EU]
sector_scope: [cross-sector]
extraterritorial: true
extraterritorial_basis: >
  Art. 2(1)(c) — applies to providers and deployers of AI systems
  located in a third country where the output is used in the Union.
lifecycle_stage: in-force-phased
adopted_date: 2024-06-13
effective_date: 2024-08-01
key_milestone_dates:
  - name: Prohibited-practice ban (Art. 5) applies
    date: 2025-02-02
    obligation_refs: [eu-ai-act-art-5-prohibited-practices]
  - name: GPAI model obligations (Chapter V) apply
    date: 2025-08-02
    obligation_refs: [eu-ai-act-art-53-gpai-providers]
  - name: High-risk Annex III systems obligations apply
    date: 2026-08-02
    obligation_refs:
      - eu-ai-act-art-9-risk-management-system
      - eu-ai-act-art-10-data-governance
      - eu-ai-act-art-13-transparency-to-deployers
      - eu-ai-act-art-14-human-oversight
  - name: High-risk Annex I embedded-safety systems apply
    date: 2027-08-02
    obligation_refs: [eu-ai-act-annex-i-embedded-high-risk]
primary_source_url: https://eur-lex.europa.eu/eli/reg/2024/1689/oj
implementing_rules_urls: []
official_guidance_urls:
  - https://digital-strategy.ec.europa.eu/en/policies/regulatory-framework-ai
last_checked_date: 2026-11-05
last_checked_by: AI Governance Analyst
tracking_status: active-work
applicability_decision: >
  We operate within the Union and place AI-enabled outputs on the EU
  market. Multiple systems are candidates for Annex III classification
  (hiring assistant — Annex III(4); credit-scoring model — Annex III(5)).
  Posture: active-work; obligations are being mapped and owners named.
applies_to_systems: [sys-0012-resume-ranker, sys-0034-credit-score]
internal_owner: Head of AI Governance
legal_point_of_contact: Deputy General Counsel — Technology
change_log:
  - date: 2024-07-12
    event: Published in Official Journal of the European Union (L series).
    source_url: https://eur-lex.europa.eu/eli/reg/2024/1689/oj
    recorded_by: AI Governance Analyst
open_questions:
  - Whether sys-0034 falls under Annex III(5)(b) (creditworthiness)
    given its decisioning role — awaiting Legal's written opinion.
next_review_date: 2026-12-15
references:
  nist_ai_rmf_mapping: [GOVERN 1.1, GOVERN 1.4, MAP 4.1, MEASURE 2.11]
  iso_42001_mapping: ["Clause 4", "Clause 6", "Annex A — A.6"]
  related_regulation_ids: [gdpr-2016-679, eu-dsa-2022-2065]
```

### 2.5 Worked instance — NYC LL 144 (regulation + one obligation)

```yaml
id: nyc-local-law-144-2021
short_name: NYC LL 144 (AEDT)
full_citation: >
  New York City Local Law 144 of 2021 — Automated Employment Decision
  Tools. Codified at NYC Admin. Code §§20-870 to 20-874.
instrument_type: local-law
issuing_body: Council of the City of New York
jurisdictions: [US-NY-NYC]
sector_scope: [employment]
extraterritorial: false
extraterritorial_basis: null
lifecycle_stage: in-force
adopted_date: 2021-12-11
effective_date: 2023-01-01
key_milestone_dates:
  - name: Enforcement begins (DCWP)
    date: 2023-07-05
    obligation_refs:
      - nyc-ll144-annual-bias-audit
      - nyc-ll144-candidate-notice
      - nyc-ll144-public-summary
primary_source_url: https://legistar.council.nyc.gov/LegislationDetail.aspx?ID=4344524&GUID=B051915D-A9AC-451E-81F8-6596032FA3F9
implementing_rules_urls:
  - https://rules.cityofnewyork.us/rule/automated-employment-decision-tools-updated/
official_guidance_urls:
  - https://www.nyc.gov/site/dca/about/automated-employment-decision-tools.page
last_checked_date: 2026-11-05
last_checked_by: AI Governance Analyst
tracking_status: active-work
applicability_decision: >
  sys-0012-resume-ranker is used to screen candidates for NYC-located
  positions — the AEDT definition in §20-870 is met.
applies_to_systems: [sys-0012-resume-ranker]
internal_owner: Head of Talent Operations
legal_point_of_contact: Employment Counsel
change_log: []
open_questions: []
next_review_date: 2027-05-01

---
# One child obligation for the regulation above.
obligation:
  id: nyc-ll144-annual-bias-audit
  regulation_id: nyc-local-law-144-2021
  citation: NYC Admin. Code §20-871(a)(1); 6 RCNY §5-301.
  summary: >
    Have an independent bias audit of the AEDT performed within the
    preceding year before any use to screen NYC candidates.
  obligation_type: assessment
  bearer: employer
  triggered_when: >
    Any use of an AEDT to substantially assist or replace discretionary
    hiring decision-making for an NYC-located position.
  evidence_required:
    - signed-independent-auditor-attestation
    - audit-data-file
    - audit-public-summary
  start_date: 2023-07-05
  recurrence: annual
  deadline_rule: "Audit must be dated within the preceding 12 months of any use."
  internal_owner: Head of Talent Operations
  secondary_owner: AI Governance Analyst
  mapped_controls: [CTRL-HR-04-bias-audit, CTRL-HR-05-candidate-notice]
  mapped_risk_ids: [RISK-sys-0012-adverse-impact]
  status: met
  status_evidence: /evidence/2026/ll144-audit-report-2026-10-15.pdf
  last_reviewed_date: 2026-10-18
  next_review_date: 2027-07-01
  penalty_summary: >
    Civil penalty per violation; each day of non-compliance and each
    unlawful AEDT use is a separate violation (§20-872).
  escalation_trigger: >
    Any below-0.80 impact ratio on an intersectional category triggers
    routing to Employment Counsel and the AI Council (see mod-104).
```

### 2.6 Controlled vocabularies (illustrative — align to local policy)

**`instrument_type`**
`regulation` · `directive` · `federal-statute` · `state-statute` ·
`local-law` · `agency-rule` · `agency-guidance` · `technical-standard` ·
`court-decision` · `enforcement-action` · `advisory-opinion`.

**`lifecycle_stage`**
`proposed` · `consulting` · `adopted-not-in-force` · `in-force-phased` ·
`in-force` · `amended` · `superseded` · `repealed`.

**`sector_scope`**
`cross-sector` · `financial-services` · `insurance` · `healthcare` ·
`employment` · `education` · `housing` · `public-sector` ·
`children-and-minors` · `biometric` · `critical-infrastructure`.

**`tracking_status`**
`monitor-only` — on our radar, no action.
`in-scope` — likely to apply; applicability review open.
`active-work` — applies; obligations are being resourced and closed.
`met-steady-state` — applies; obligations recurring on cadence.
`out-of-scope` — reviewed and determined not to apply; rationale on file.

**`obligation_type`**
`registration` · `notification` · `assessment` · `audit` · `recordkeeping` ·
`disclosure` · `human-oversight` · `transparency` · `data-governance` ·
`conformity-assessment` · `incident-reporting` · `prohibition` ·
`contestability` · `training-and-awareness`.

### 2.7 Minimal JSON Schema fragment (for CI validation)

Use this to lint tracker files in a `pre-commit` hook. The schema is
deliberately permissive on strings so Legal can shape the free-text
fields without re-releasing the schema.

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "https://example.org/schemas/regulatory-tracker/regulation.json",
  "title": "regulation",
  "type": "object",
  "required": [
    "id", "short_name", "full_citation", "instrument_type",
    "issuing_body", "jurisdictions", "lifecycle_stage",
    "primary_source_url", "last_checked_date", "last_checked_by",
    "tracking_status", "applicability_decision", "internal_owner",
    "next_review_date"
  ],
  "properties": {
    "id": {"type": "string", "pattern": "^[a-z0-9][a-z0-9-]{2,}$"},
    "short_name": {"type": "string"},
    "full_citation": {"type": "string"},
    "instrument_type": {
      "type": "string",
      "enum": [
        "regulation", "directive", "federal-statute", "state-statute",
        "local-law", "agency-rule", "agency-guidance",
        "technical-standard", "court-decision", "enforcement-action",
        "advisory-opinion"
      ]
    },
    "jurisdictions": {
      "type": "array", "minItems": 1, "items": {"type": "string"}
    },
    "lifecycle_stage": {
      "type": "string",
      "enum": [
        "proposed", "consulting", "adopted-not-in-force",
        "in-force-phased", "in-force", "amended", "superseded", "repealed"
      ]
    },
    "extraterritorial": {"type": "boolean"},
    "primary_source_url": {"type": "string", "format": "uri"},
    "last_checked_date": {"type": "string", "format": "date"},
    "last_checked_by": {"type": "string"},
    "tracking_status": {
      "type": "string",
      "enum": [
        "monitor-only", "in-scope", "active-work",
        "met-steady-state", "out-of-scope"
      ]
    },
    "applicability_decision": {"type": "string", "minLength": 1},
    "internal_owner": {"type": "string"},
    "next_review_date": {"type": "string", "format": "date"},
    "applies_to_systems": {
      "type": "array", "items": {"type": "string"}
    },
    "change_log": {
      "type": "array",
      "items": {
        "type": "object",
        "required": ["date", "event", "recorded_by"],
        "properties": {
          "date": {"type": "string", "format": "date"},
          "event": {"type": "string"},
          "source_url": {"type": "string", "format": "uri"},
          "recorded_by": {"type": "string"}
        }
      }
    }
  }
}
```

### 2.8 Decision rationale

- **Two entities, not one.** A regulation holds many obligations; the
  obligation is the row an owner closes. Collapsing them into one
  "regulation + checklist" field hides the owner-level accountability.
- **Role, not team, in `internal_owner`.** Teams rotate. Named roles
  persist for audit. This mirrors the routing decision in `mod-103`
  exercise-05.
- **Append-only `change_log`.** Regulators notice re-written history. An
  append-only log lets the analyst show how interpretation evolved.
- **`tracking_status` ≠ `lifecycle_stage`.** The regulator's status
  (in-force) is independent of ours (active-work vs. monitor-only).
  Confusing the two is a common tracker failure.
- **`extraterritorial` is a separate flag.** Rules like the EU AI Act
  Art. 2(1)(c) bind non-EU entities whose outputs reach the Union; a
  jurisdictions-only field misses this.
- **Cross-link to inventory, controls, and risk, not re-state them.**
  The tracker is the source of regulatory obligations; the inventory
  is the source of systems; the control catalogue is the source of
  evidence. Each FK is a single pointer, not a copy.
- **Controlled vocabularies with explicit allowed values.** A
  free-text `sector_scope` cannot be filtered across the tracker.
- **Penalty summary, not penalty analysis.** The field is a reminder,
  not the legal opinion; legal analysis lives with Legal.
- **`next_review_date` on every row.** A tracker with no review cadence
  is a graveyard within a quarter.
- **Mapping to NIST AI RMF and ISO/IEC 42001** on the regulation
  record is deliberately coarse (clause-level) — it's the hand-off
  point to the control catalogue (see `mod-108`).

## 3. Validation steps

1. Run the JSON Schema check on every tracker file in CI. Fail on any
   missing required field.
2. For each `regulation` with `tracking_status` ∈ {`active-work`,
   `met-steady-state`}, confirm at least one child `obligation` exists
   and at least one `applies_to_systems` entry resolves against the
   inventory.
3. For each `obligation` with `status = met`, confirm `status_evidence`
   resolves to an artefact in the evidence store.
4. Any row with `last_checked_date` older than `next_review_date`
   listed on the previous review is overdue — list to the analyst.
5. Any `open_questions` entry older than 90 days without a change-log
   update is stale — surface to Legal.
6. Cross-check the `change_log` dates against the regulator's own
   publication feeds for the high-signal instruments
   (`eu-ai-act-2024`, `nyc-local-law-144-2021`, `gdpr-2016-679`, and
   any sector-specific regulators listed in `mod-106` exercise-04).
7. Confirm the `references.nist_ai_rmf_mapping` entries resolve to
   subcategories that actually exist in AI RMF 1.0 (the full
   taxonomy, e.g. GOVERN 1.1 through GOVERN 6.2, MAP 1.1 through
   MAP 5.2, MEASURE 1.1 through MEASURE 4.3, MANAGE 1.1 through
   MANAGE 4.3).

## 4. Rubric or review checklist

Grade the learner's submission on five dimensions, 3 points each
(total 15). Pass = total ≥ 11 with no 0.

| Criterion | 3 — strong | 2 — acceptable | 0–1 — weak |
|---|---|---|---|
| **Entity model** | Separates `regulation` and `obligation`; justifies why. | Single flat table with repeated fields per obligation. | Collapses everything into one free-text field. |
| **Required fields** | Covers identification, jurisdiction (with extraterritoriality flag), lifecycle, provenance, internal posture, owner (role), and review cadence. | Covers ~80% of the above. | Misses jurisdiction detail, owner, or review cadence. |
| **Controlled vocabularies** | Enumerated types for `instrument_type`, `lifecycle_stage`, `sector_scope`, `tracking_status`, `obligation_type`; vocabularies are justified. | Enumerated types exist but incomplete. | Free text everywhere. |
| **Cross-links** | FK to inventory, controls, risk register, and related regulations is explicit and bidirectional in intent. | FK to inventory only. | No cross-links; tracker is isolated. |
| **Worked instance quality** | Three worked instances across at least two instrument types and two jurisdictions; dates are real; penalty summary cites the article. | Two worked instances. | One worked instance with invented or missing dates and no source URL. |

Document-level checks (binary, all must pass):

- [ ] `id` field is a stable slug, not a title.
- [ ] `primary_source_url` resolves to an authoritative source (official
      journal, regulator website, or codified text).
- [ ] `internal_owner` is a role, not a person or a team.
- [ ] `next_review_date` is set on every record.
- [ ] Schema is linted (JSON Schema or equivalent) in CI.

## 5. Common mistakes

- **One row per regulation, no child obligations.** The owner-level
  accountability dissolves because there is nothing to close.
- **Jurisdiction as a free-text country name.** "NY" and "New York" and
  "NYS" and "New York State" all get written; the filter breaks.
- **Missing `extraterritorial` flag.** The EU AI Act and GDPR both reach
  non-EU entities; a jurisdictions-only field hides the scope.
- **`internal_owner` is a team.** "Compliance signed it" is not an
  audit trail. Name the role.
- **No `next_review_date`.** The tracker becomes a snapshot and goes
  stale within a quarter.
- **Rewriting history in place.** Change log is append-only; editing
  the primary record without a change-log entry loses the "when did we
  know" answer.
- **Treating the NIST AI RMF or ISO/IEC 42001 row as a regulation.**
  They are tracked for alignment, not as sources of legal duty. Use
  `instrument_type: technical-standard` and `tracking_status:
  monitor-only` unless local policy has adopted them as internal
  requirements.
- **Penalty analysis in the tracker.** A short penalty summary is
  useful; a legal analysis belongs in a Legal memo linked from the
  record.
- **Mixing applicability with interpretation.** The tracker says
  whether the rule applies and who owns it; the Legal memo says what
  the rule means. Collapsing both into `applicability_decision` makes
  the tracker un-reviewable.

## 6. References

- Regulation (EU) 2024/1689 of the European Parliament and of the
  Council of 13 June 2024 laying down harmonised rules on artificial
  intelligence (Artificial Intelligence Act), OJ L series, 12 July
  2024. <https://eur-lex.europa.eu/eli/reg/2024/1689/oj>
- Regulation (EU) 2016/679 — General Data Protection Regulation. <https://eur-lex.europa.eu/eli/reg/2016/679/oj>
- New York City Local Law 144 of 2021 — Automated Employment Decision
  Tools; NYC Admin. Code §§20-870 to 20-874. <https://legistar.council.nyc.gov/LegislationDetail.aspx?ID=4344524&GUID=B051915D-A9AC-451E-81F8-6596032FA3F9>
- NYC Department of Consumer and Worker Protection, *Final Rules on
  Automated Employment Decision Tools*, 6 RCNY ch. 5, subch. T. <https://rules.cityofnewyork.us/rule/automated-employment-decision-tools-updated/>
- NIST, *AI Risk Management Framework (AI RMF 1.0)*, January 2023 —
  `GOVERN`, `MAP`, `MEASURE`, `MANAGE` functions used for cross-ref. <https://www.nist.gov/itl/ai-risk-management-framework>
- ISO/IEC 42001:2023 — *Information technology — Artificial
  intelligence — Management system*. <https://www.iso.org/standard/81230.html>
- OWASP Machine Learning Security Top 10 — referenced where a
  tracked instrument interacts with ML-security duties (e.g.
  incident-reporting obligations). <https://owasp.org/www-project-machine-learning-security-top-10/>
- MITRE ATLAS — referenced where security-incident reporting under a
  tracked instrument is likely to use adversary-technique vocabulary. <https://atlas.mitre.org/>
