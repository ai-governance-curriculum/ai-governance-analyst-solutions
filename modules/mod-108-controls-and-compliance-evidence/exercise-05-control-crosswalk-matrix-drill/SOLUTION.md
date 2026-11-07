# Exercise 05 — Control Crosswalk Matrix Drill — Reference Solution

## 1. Solution overview

Most AI governance programmes end up referencing several frameworks
simultaneously: **NIST AI RMF 1.0** for structure, **ISO/IEC
42001:2023** for the AIMS clauses an auditor will look for, the
**NIST Generative AI Profile (NIST AI 600-1)** for GAI-specific
actions, **OWASP LLM Top 10** and **OWASP ML Security Top 10** plus
**MITRE ATLAS** for the security vocabulary, and a growing set of
regulatory obligations (EU AI Act, Colorado, NYC LL 144, etc.).

Each of those speaks its own vocabulary. Without a crosswalk, each
framework becomes a separate audit, each audit asks for a new
version of the same evidence, and the control catalogue becomes a
list of near-duplicates ("GOVERN-1.1 attestation" vs. "Clause 5.1
attestation").

A crosswalk matrix solves this by making the **internal control
catalogue the join key**. Every internal control row carries its
mappings to the external frameworks; every framework-side reference
points back to one or more internal controls. The analyst does
diligence once and emits framework-specific views on demand.

This solution provides:

- A matrix structure (internal controls × external frameworks).
- A worked matrix with twelve representative internal controls
  spanning governance, data, model, security, deployment,
  monitoring, third-party, and incident.
- Direction-of-fit rules (when does a framework entry map to one
  control vs. many).
- A gap-analysis procedure using the matrix.
- Decision rationale and a grading rubric.

Scope: design + implementation exercise. The worked matrix is a
reference; a production matrix would carry more rows and live in
the organisation's GRC tool.

## 2. Worked answer or implementation

### 2.1 Matrix row schema

```yaml
control_crosswalk_row:
  control_id: string              # FK to the control catalogue (exercise-01)
  control_intent: string          # one sentence — what outcome this control exists to produce
  control_type: enum              # "governance" / "data" / "model" / "security" /
                                  # "deployment" / "monitoring" / "third-party" / "incident"
  owner_role: string              # role, not team, not person
  mappings:
    nist_ai_rmf_1_0: [string]     # e.g. ["GOVERN 1.4", "MAP 4.1"]
    iso_42001_2023: [string]      # clauses + Annex A controls
    nist_ai_600_1: [string]       # GAI profile action refs (e.g. "GV-1.2-001")
    owasp_llm_top_10: [string]    # LLM01..LLM10 with edition
    owasp_ml_top_10: [string]     # ML01..ML10 with edition
    mitre_atlas: [string]         # tactic/technique ids with matrix version
    regulatory_obligations: [string]  # FK to tracker obligations (mod-106)
  evidence_type: [enum]           # "attestation" / "artefact-link" / "test-output" / ...
  frequency: enum                 # see exercise-01
  last_result: enum|null
  next_due_date: date
  notes: string|null              # scoping notes, dependencies
```

### 2.2 Direction-of-fit rules

A framework entry (e.g. NIST AI RMF `MEASURE 2.11`) may map to one
internal control, several, or none. Apply the following:

1. **One control, one intent.** If two frameworks both ask for
   "fairness across protected groups", that is one control (with
   two framework mappings), not two controls.
2. **One framework entry, many controls, is normal.** An ISO/IEC
   42001 clause (e.g. 8.1 Operational Planning and Control) is
   intentionally broad; expect it to map to many internal controls.
3. **One control, many framework entries, is also normal.** A single
   model-card-published control maps to NIST AI RMF `MAP 4.1` and
   `GOVERN 1.4`, ISO/IEC 42001 clauses 7.5 and 8.1, NIST AI 600-1
   `MAP-4.1-001`, EU AI Act Art. 11 technical-documentation duty,
   and more.
4. **A missing framework mapping is a signal.** If a framework entry
   has no mapped internal control, that is a gap — open a control-
   gap finding and route per exercise-01.
5. **A control with only one framework mapping is a smell.** If a
   control maps only to one framework, either it is genuinely
   framework-specific (rare) or it is redundant with another
   control and should merge.

### 2.3 Worked matrix — twelve internal controls

Mappings are illustrative. Verify framework entries against the
current editions before publishing. ATLAS version and OWASP editions
should be pinned per row; shown here at the matrix header for
brevity.

Matrix header (pin per publication):

- NIST AI RMF 1.0 (January 2023)
- ISO/IEC 42001:2023
- NIST AI 600-1 (July 2024 GAI Profile)
- OWASP LLM Top 10 — 2025 edition
- OWASP ML Security Top 10 — current edition
- MITRE ATLAS — current matrix version

#### Row 1 — CTRL-GOV-01-ai-policy-published

- **Intent**: a board-approved AI policy exists and is current
  (reviewed in last 12 months).
- **Type**: governance. **Owner**: Head of AI Governance.
  **Frequency**: annual.
- **NIST AI RMF 1.0**: `GOVERN 1.1`, `GOVERN 1.2`, `GOVERN 1.4`.
- **ISO/IEC 42001:2023**: clauses 5.2 (policy), 5.1 (leadership),
  7.4 (communication).
- **NIST AI 600-1**: `GV-1.1-001`, `GV-1.2-001` (confirm precise
  action ids against the profile).
- **Regulatory obligations**: EU AI Act Art. 17 (QMS for providers
  of high-risk) as a dependency.
- **Evidence**: policy document, signed approval record, review log.

#### Row 2 — CTRL-GOV-02-ai-council-charter

- **Intent**: a named AI council (role, cadence, decision scope)
  exists; minutes are kept.
- **Type**: governance. **Owner**: Head of AI Governance. **Freq**:
  continuous (quarterly minutes).
- **NIST AI RMF 1.0**: `GOVERN 2.1`, `GOVERN 2.2`, `GOVERN 5.1`.
- **ISO/IEC 42001:2023**: clauses 5.3 (roles and
  responsibilities), 9.3 (management review).
- **NIST AI 600-1**: `GV-2.*` actions on accountability.
- **Regulatory obligations**: EU AI Act Art. 26 oversight duties
  (where deployer).
- **Evidence**: charter, membership record, minutes.

#### Row 3 — CTRL-DAT-01-intake-and-inventory

- **Intent**: every AI system has a current intake + inventory
  record (per mod-102).
- **Type**: data / governance. **Owner**: Head of AI Governance.
  **Freq**: on-change + quarterly check.
- **NIST AI RMF 1.0**: `MAP 1.1`, `MAP 1.2`, `MAP 1.3`.
- **ISO/IEC 42001:2023**: clauses 6.1.3 (determining risks /
  opportunities), 8.1.
- **NIST AI 600-1**: `MAP-1.*` actions.
- **Regulatory obligations**: EU AI Act Art. 16 (provider
  obligations) / Art. 26 (deployer); Colorado SB 24-205
  inventory-related duties.
- **Evidence**: inventory row; intake record.

#### Row 4 — CTRL-DAT-02-dataset-provenance

- **Intent**: every training / fine-tuning dataset has provenance,
  licence, and sensitive-data classification recorded.
- **Type**: data. **Owner**: Head of ML Engineering. **Freq**:
  on-change + annual.
- **NIST AI RMF 1.0**: `MAP 2.3`, `MEASURE 2.*` (where data quality
  is measured), `MANAGE 2.*`.
- **ISO/IEC 42001:2023**: Annex A controls on data management (A.4
  / A.7 / A.8 in the AIMS Annex A — verify against the final
  standard's control numbering).
- **NIST AI 600-1**: data-governance actions under `MAP` and
  `MEASURE`.
- **OWASP LLM Top 10**: `LLM04` (data poisoning) dependency.
- **OWASP ML Security Top 10**: data-poisoning category.
- **Regulatory obligations**: EU AI Act Art. 10 (data and data
  governance for high-risk); GDPR Art. 5(1)(b).
- **Evidence**: dataset manifest; licence record.

#### Row 5 — CTRL-MOD-01-model-card-published

- **Intent**: every production AI system has a published model /
  system card at current major version.
- **Type**: model. **Owner**: AI Governance Analyst (with ML eng
  co-author). **Freq**: on-change + quarterly check.
- **NIST AI RMF 1.0**: `MAP 4.1`, `MEASURE 2.9`, `MANAGE 1.3`.
- **ISO/IEC 42001:2023**: clauses 7.5 (documented information), 8.1.
- **NIST AI 600-1**: `MAP-4.1-*` and `MS-2.*` documentation actions.
- **Regulatory obligations**: EU AI Act Art. 11 (technical
  documentation), Art. 13 (transparency to deployers), Art. 53
  (GPAI model documentation).
- **Evidence**: model card URL, git SHA, approval record.

#### Row 6 — CTRL-MOD-02-release-gate-eval

- **Intent**: every release of an AI system passes the release-gate
  evaluation suite covering performance, fairness, and safety
  thresholds; failing suites block release.
- **Type**: model. **Owner**: Head of ML Engineering. **Freq**:
  on-change.
- **NIST AI RMF 1.0**: `MEASURE 1.1`, `MEASURE 2.3`, `MEASURE 2.9`,
  `MEASURE 2.11`, `MANAGE 1.*`.
- **ISO/IEC 42001:2023**: clauses 8.1, 9.1 (monitoring and
  measurement).
- **NIST AI 600-1**: `MS-*` evaluation actions.
- **OWASP LLM Top 10**: `LLM09` (misinformation) and `LLM04` where
  the suite covers poisoning signals.
- **Regulatory obligations**: EU AI Act Art. 15 (accuracy,
  robustness, cybersecurity); NYC LL 144 bias audit (as a
  dependency, not a substitute).
- **Evidence**: release-gate run id; verdict; linked eval report.

#### Row 7 — CTRL-SEC-01-prompt-injection-eval

- **Intent**: generative / LLM systems run an adversarial prompt-
  injection evaluation as part of the release gate; findings route
  to the fairness-and-safety review.
- **Type**: security (AML). **Owner**: Head of Security / AI
  Security Lead. **Freq**: on-change + quarterly.
- **NIST AI RMF 1.0**: `MEASURE 2.6`, `MEASURE 2.7`, `MANAGE 1.*`.
- **ISO/IEC 42001:2023**: clauses 8.1, 9.1; Annex A AI-security
  controls.
- **NIST AI 600-1**: GAI security actions.
- **OWASP LLM Top 10**: `LLM01` (primary), `LLM07` (secondary).
- **MITRE ATLAS**: tactic Execution / Impact; technique LLM Prompt
  Injection.
- **Regulatory obligations**: EU AI Act Art. 15 cybersecurity;
  incident-reporting Art. 73 dependency.
- **Evidence**: red-team report; eval run id.

#### Row 8 — CTRL-SEC-02-model-provenance

- **Intent**: no third-party model weights are loaded into
  production without signed provenance (hash-pinning + supply
  chain attestation).
- **Type**: security / third-party. **Owner**: Head of Security.
  **Freq**: on-change + annual.
- **NIST AI RMF 1.0**: `GOVERN 6.1`, `GOVERN 6.2`, `MANAGE 3.*`.
- **ISO/IEC 42001:2023**: clause 8.1; Annex A supplier controls.
- **NIST AI 600-1**: supply-chain actions.
- **OWASP LLM Top 10**: `LLM03` (supply chain), `LLM04` (data /
  model poisoning).
- **OWASP ML Security Top 10**: AI supply-chain attacks;
  model-poisoning category.
- **MITRE ATLAS**: Resource Development / Initial Access.
- **Regulatory obligations**: EU AI Act Art. 25 (value chain).
- **Evidence**: artefact hash record; vendor attestation (link to
  exercise-04 questionnaire).

#### Row 9 — CTRL-DEP-01-human-oversight

- **Intent**: every high-risk AI system has a documented human-
  oversight mechanism, with a reviewer role, intervention
  authority, and trained personnel.
- **Type**: deployment. **Owner**: Head of Product (or equivalent
  business owner). **Freq**: on-change + annual.
- **NIST AI RMF 1.0**: `MAP 3.*`, `MANAGE 1.*`, `GOVERN 3.*`.
- **ISO/IEC 42001:2023**: clauses 7.2 (competence), 8.1.
- **NIST AI 600-1**: `MG-3.*` and `GV-3.*` actions.
- **Regulatory obligations**: EU AI Act Art. 14 (human oversight);
  Colorado SB 24-205 deployer oversight; NYC LL 144 alternative
  selection process.
- **Evidence**: oversight design document; reviewer training record.

#### Row 10 — CTRL-MON-01-production-monitoring

- **Intent**: every production AI system has production monitoring
  (performance, calibration, fairness, drift, safety-filter hits)
  with alerting to a named role.
- **Type**: monitoring. **Owner**: Head of ML Engineering. **Freq**:
  continuous.
- **NIST AI RMF 1.0**: `MEASURE 2.*`, `MEASURE 4.*`, `MANAGE 4.*`.
- **ISO/IEC 42001:2023**: clause 9.1 (monitoring and measurement).
- **NIST AI 600-1**: `MS-*` and `MG-*` monitoring actions.
- **OWASP LLM Top 10**: `LLM10` (unbounded consumption — rate /
  cost monitoring layer).
- **Regulatory obligations**: EU AI Act Art. 72 post-market
  monitoring (where provider), Art. 15.
- **Evidence**: monitor configuration; alerting routes; sample
  alerts closed.

#### Row 11 — CTRL-TP-02-vendor-attestation

- **Intent**: every tier-1 and tier-2 AI vendor supplies an annual
  attestation covering the questions in exercise-04.
- **Type**: third-party. **Owner**: Head of Procurement / Vendor
  Risk Lead. **Freq**: annual.
- **NIST AI RMF 1.0**: `GOVERN 6.1`, `GOVERN 6.2`.
- **ISO/IEC 42001:2023**: clause 8.1 (supplier aspects); Annex A
  supplier controls.
- **NIST AI 600-1**: supplier-focused actions under `GV`.
- **OWASP LLM Top 10**: `LLM03` dependency.
- **Regulatory obligations**: EU AI Act Art. 25 (value chain),
  Art. 53 (GPAI information duties).
- **Evidence**: signed attestation; AI Addendum; follow-up ticket
  closure.

#### Row 12 — CTRL-INC-01-incident-response

- **Intent**: an AI-specific incident-response runbook is active;
  every AI incident runs through classification, containment,
  investigation, communication, and postmortem.
- **Type**: incident. **Owner**: Head of Security (incident
  commander) with AI Governance Analyst (communications).
  **Freq**: on-incident + quarterly tabletop.
- **NIST AI RMF 1.0**: `MANAGE 4.1`, `MANAGE 4.2`, `MANAGE 4.3`.
- **ISO/IEC 42001:2023**: clauses 10.2 (nonconformity and
  corrective action), 8.1.
- **NIST AI 600-1**: `MG-4.*` incident actions.
- **OWASP LLM Top 10**: all categories as potential incident
  classes.
- **MITRE ATLAS**: tactic Impact; Collection; Exfiltration.
- **Regulatory obligations**: EU AI Act Art. 73 (serious-incident
  reporting), Colorado SB 24-205 Attorney-General notification
  (confirm current discovery-to-notice window against the enrolled
  amendments).
- **Evidence**: runbook; incident log; postmortem pdfs; tabletop
  minutes.

### 2.4 Framework-side inverse views

Using the twelve rows above, the inverse-direction views answer
auditor questions in one query.

**View A — NIST AI RMF `GOVERN 6.*` auditor view.** Which internal
controls demonstrate third-party risk management? Rows 8, 11 (and,
by dependency, rows 4, 12).

**View B — ISO/IEC 42001:2023 clause 8.1 auditor view.** Which
internal controls operate the AIMS? Rows 3, 4, 5, 6, 7, 9, 10, 11,
12.

**View C — OWASP LLM Top 10 coverage view.** Which categories are
addressed by at least one control?

| Category | Controls |
|---|---|
| LLM01 Prompt Injection | 7 |
| LLM02 Sensitive Information Disclosure | *gap — covered partially by 10; open a dedicated DLP-class control if generative output paths handle sensitive data* |
| LLM03 Supply Chain | 8, 11 |
| LLM04 Data and Model Poisoning | 4, 6, 8 |
| LLM05 Improper Output Handling | *gap — propose CTRL-SEC-03-output-handling* |
| LLM06 Excessive Agency | *gap — propose CTRL-SEC-04-agent-authority* |
| LLM07 System Prompt Leakage | 7 |
| LLM08 Vector and Embedding Weaknesses | *gap — propose CTRL-SEC-05-retrieval-isolation* |
| LLM09 Misinformation | 6, 10 |
| LLM10 Unbounded Consumption | 10 |

Every "gap" above is a control-gap finding routed per exercise-01.

**View D — EU AI Act Annex III auditor view.** For a high-risk
hiring system under Annex III(4), which controls satisfy the
provider / deployer duties?

| Article | Controls |
|---|---|
| Art. 9 (risk management) | 3, 6, 10, 12 |
| Art. 10 (data and data governance) | 4 |
| Art. 11 (technical documentation) | 5, 6 |
| Art. 13 (transparency to deployers) | 5 |
| Art. 14 (human oversight) | 9 |
| Art. 15 (accuracy, robustness, cybersecurity) | 6, 7, 10 |
| Art. 17 (QMS, providers) | 1, 2, 3 |
| Art. 25 (value chain) | 8, 11 |
| Art. 26 (deployer) | 2, 5, 9 |
| Art. 27 (FRIA) | 3, 9 (and the FRIA artefact itself from mod-105) |
| Art. 72 (post-market monitoring) | 10 |
| Art. 73 (serious-incident reporting) | 12 |

### 2.5 Gap analysis procedure

Run quarterly as part of exercise-01's plan phase:

1. Walk the NIST AI RMF 1.0 subcategory list; any subcategory with
   no mapped control is a gap (either add a control or add an
   explicit "intentionally out-of-scope" rationale, with Legal
   sign-off).
2. Walk the ISO/IEC 42001:2023 Annex A controls; any Annex-A
   control without an internal mapping is a gap or an explicit
   exclusion.
3. Walk the NIST AI 600-1 GAI Profile actions; for any system
   whose profile is generative, any action without a mapping is a
   gap.
4. Walk the OWASP LLM Top 10 categories against systems where the
   surface applies; same rule.
5. Walk the regulatory tracker (mod-106); for every in-scope
   obligation, confirm ≥1 mapped control with current evidence.
6. For every gap, raise a finding into the risk register with
   owner and target date; feed the plan phase of the next quarter.

### 2.6 Decision rationale

- **Internal control is the join key.** Mapping frameworks to each
  other directly produces an N × N matrix that no one maintains.
  Mapping each framework to the internal control row keeps the
  cost linear.
- **A row maps to many frameworks on purpose.** Good crosswalks
  show the overlap; separate rows for "NIST version" and "ISO
  version" of the same control cause drift.
- **Pin editions in the matrix header and per row.** OWASP editions
  shift; ATLAS evolves; NIST AI 600-1 may be revised. A frozen
  edition keeps the matrix comparable.
- **Gaps are routed, not hidden.** An empty mapping cell is
  signal, not failure. The gap procedure converts signal into
  work items.
- **Framework-side inverse views are cheap once the row-wise
  matrix exists.** Pre-computing a GOVERN-6 view or an Annex III
  view by hand each quarter is wasted analyst time; the row-wise
  matrix is the source of truth and the views are queries.
- **Owner role, not team.** Mirrors exercise-01 and the regulatory
  tracker; audit trails need named roles.
- **Explicit out-of-scope is better than implicit gap.** "Not
  applicable because systems X" with sign-off is auditor-defensible;
  a silent gap is not.
- **Vendor-attestation row ties into exercise-04.** The crosswalk
  can be "complete" internally but hollow at the vendor edge if
  the questionnaire programme does not run.

## 3. Validation steps

1. Confirm every internal control row carries at least the NIST AI
   RMF and ISO/IEC 42001 mappings.
2. For every row, confirm the regulatory-obligation list is pointed
   at FK ids in the mod-106 tracker (not free text).
3. For every regulatory obligation flagged in-scope for this
   quarter, confirm at least one control with current evidence is
   mapped.
4. For every OWASP LLM Top 10 category that applies to at least
   one production system, confirm at least one control is mapped
   or an explicit gap is open.
5. Run the inverse-view queries for ISO/IEC 42001 clause 8.1 and
   NIST AI RMF `GOVERN 6.*`. Confirm each returns a non-trivial
   list of controls.
6. Pin editions (framework version numbers) in the matrix header
   on publication.
7. Confirm every gap has a finding ticket with owner and target
   date.

## 4. Rubric or review checklist

Grade the crosswalk on six dimensions, 3 points each (total 18).
Pass = total ≥ 13 with no 0.

| Criterion | 3 — strong | 2 — acceptable | 0–1 — weak |
|---|---|---|---|
| **Join-key design** | Internal control is the join key; framework-side views are inverse queries. | Mixed — some rows per framework, some per control. | Framework-to-framework mapping tables. |
| **Coverage breadth** | Rows span governance, data, model, security, deployment, monitoring, third-party, incident. | 5–6 of the 8 types covered. | ≤3 types covered. |
| **Framework mapping completeness** | Rows carry mappings to NIST AI RMF, ISO/IEC 42001, NIST AI 600-1, OWASP (LLM and ML where applicable), MITRE ATLAS, regulatory obligations. | Three or four of the framework mappings per row. | Only one framework mapping. |
| **Gap analysis** | Procedure defined; gaps become findings; worked view-C-style gap list produced. | Procedure defined but not run against the sample rows. | Gaps not surfaced. |
| **Inverse views** | At least two inverse views (e.g. GOVERN-6, Annex III) demonstrated. | One inverse view. | None. |
| **Edition / version pinning** | Matrix header pins framework editions; per-row OWASP and ATLAS versions recorded. | Pinned at the header only. | No versions pinned. |

Document-level checks (binary, all must pass):

- [ ] Every row has `owner_role` as a role.
- [ ] Every row has `next_due_date`.
- [ ] Framework editions are pinned.
- [ ] Every gap has a ticket with owner and date.

## 5. Common mistakes

- **Framework-to-framework matrix.** N × N mapping is unmaintainable.
  Map each framework to internal controls and compute cross-
  framework overlaps from there.
- **One control row per framework.** "ISO 42001 attestation" and
  "NIST AI RMF attestation" as separate rows guarantee drift. If
  the intent is the same, merge.
- **Mapping completeness as the goal.** The goal is operating
  controls with current evidence; completeness of mapping is the
  intermediate artefact. A fully-mapped matrix with stale evidence
  fails an audit.
- **Silent gaps.** Empty mapping cells with no "intentionally out-
  of-scope" rationale are indistinguishable from forgotten
  controls.
- **Wrong direction of fit.** Framework entries are often broad
  (an Annex-A control; a NIST subcategory); many internal
  controls may map to one entry. That is fine. Trying to force a
  1-to-1 fit loses signal.
- **Not pinning framework editions.** Silent re-labelling when a
  framework revises its numbering corrupts the matrix history.
- **Treating regulatory obligations as a framework.** Regulatory
  obligations are discrete duties (mod-106 tracker rows), not a
  framework's subcategory list. Keep them in a separate column so
  the deadline and bearer come through.
- **Owner as team.** Audit trails do not survive team renames.
  Role-level ownership survives.
- **No cadence.** A crosswalk that is not re-walked quarterly in
  exercise-01's plan phase drifts.
- **Auditor-pleasing over truth-telling.** Fabricated mappings to
  look complete are worse than named gaps; an experienced
  auditor can pattern-match a fake mapping quickly.

## 6. References

- NIST, *AI Risk Management Framework (AI RMF 1.0)*, January 2023 —
  `GOVERN`, `MAP`, `MEASURE`, `MANAGE` functions and subcategories.
  <https://www.nist.gov/itl/ai-risk-management-framework>
- NIST, *AI RMF Generative AI Profile* (NIST AI 600-1), 2024 — GAI-
  specific actions referenced in multiple rows.
  <https://nvlpubs.nist.gov/nistpubs/ai/NIST.AI.600-1.pdf>
- ISO/IEC 42001:2023 — *Information technology — Artificial
  intelligence — Management system*; clauses and Annex A controls
  used throughout the matrix.
  <https://www.iso.org/standard/81230.html>
- Regulation (EU) 2024/1689 — EU AI Act. Articles cited:
  9, 10, 11, 13, 14, 15, 17, 25, 26, 27, 50, 53, 72, 73.
  <https://eur-lex.europa.eu/eli/reg/2024/1689/oj>
- OWASP Top 10 for LLM Applications & Gen AI Security Project —
  LLM01..LLM10 coverage.
  <https://genai.owasp.org/llm-top-10/>
- OWASP Machine Learning Security Top 10 — classical-ML coverage.
  <https://owasp.org/www-project-machine-learning-security-top-10/>
- MITRE ATLAS — adversarial-ML tactics/techniques referenced in
  security rows.
  <https://atlas.mitre.org/>
- VeriSwarm Trust Center — practitioner implementation example of
  a public-facing crosswalk between internal controls and NIST
  AI RMF / ISO/IEC 42001 / OWASP / MITRE ATLAS.
  <https://veriswarm.ai/trust>
