# Exercise 04 — SR 11-7 Mapping for a Financial-Services Use Case — Reference Solution

## 1. Solution overview

**SR 11-7** — "Guidance on Model Risk Management", issued by the Federal
Reserve Board on 4 April 2011 as supervisory letter SR 11-7, with
parallel OCC Bulletin **OCC 2011-12** — is the model risk management
regime US banks have run under for over a decade. It applies to any
"quantitative method, system, or approach" that applies statistical,
economic, financial, or mathematical theories, techniques, or
assumptions to process input data into quantitative estimates — which
covers nearly all machine-learning models used in bank decision-making.
The FDIC adopted substantively the same guidance in **FIL-22-2017**.

SR 11-7 structures model risk management around three elements:

1. **Model development, implementation, and use** — sound design,
   rigorous data, proper implementation, appropriate use.
2. **Model validation** — independent, effective challenge through
   (a) evaluation of conceptual soundness, (b) ongoing monitoring
   including process verification and benchmarking, and (c) outcomes
   analysis including back-testing.
3. **Governance, policies, and controls** — board and senior-management
   oversight, roles and responsibilities, a model inventory, and
   model documentation.

A governance analyst who already has a NIST AI RMF 1.0 first-draft
assessment needs to produce an **SR 11-7 mapping** so the MRM team
(Model Risk Management, usually a second-line function) can take the
assessment through the bank's MRM process without re-work.

This solution provides:

- A reference financial-services use case (local exercise context).
- A mapping of SR 11-7 elements to NIST AI RMF 1.0 and ISO/IEC 23894
  artefacts.
- A worked SR 11-7 one-pager that an MRM team would accept.
- Decision rationale and rubric.

## 2. Worked answer or implementation

### 2.1 Use case under assessment (local exercise context)

> **System name**: SmallDollarUW (internal)
> **Owner**: Consumer Lending, with second-line MRM and third-line IA.
> **Purpose**: Score new-to-bank consumer small-dollar loan
> applications to produce a numeric risk estimate the underwriter uses
> to recommend approve / decline / refer-to-human.
> **Model**: Gradient-boosted tree trained on internal application and
> bureau data; deployed via an internal real-time inference service.
> **Decision role**: Advisory to a human underwriter for amounts below
> a threshold; automated decline for scores below a cutoff, with
> adverse-action notice and reason codes per FCRA; referral path for
> borderline scores.
> **Scope of this assessment**: initial validation prior to
> limited-population launch.
> **Out-of-scope**: pricing, servicing, collections.

This is a reference scenario for the exercise, not a real system.

### 2.2 SR 11-7 element → artefact mapping

| SR 11-7 element | What SR 11-7 expects | NIST AI RMF 1.0 artefact | ISO/IEC 23894 artefact | Who owns |
| --- | --- | --- | --- | --- |
| **1. Development, implementation, use — Development** | Sound design; documented assumptions; data appropriate and limitations known. | Map 1 (context), Map 3 (vs status quo), Map 4 (components); Measure 1 (metric selection). | Risk-source review (ML, data, lifecycle). | 1LoD model development. |
| **1. Development, implementation, use — Implementation** | Proper implementation in production; input/output integrity; change control. | Map 4 (components); Manage 4 (change-control log). | Lifecycle objectives. | 1LoD engineering. |
| **1. Development, implementation, use — Use** | Fit-for-purpose use; limitations communicated to users; conservative use near boundaries. | Map 1 (intended purpose); Govern 1 (policy); model card limitations section. | Objectives for use & human factors. | 1LoD business users. |
| **2. Validation — Conceptual soundness** | Independent critical review of design, theory, and assumptions. | Measure 1, Measure 4 (feedback on measurement efficacy). | Objectives on robustness, bias, explainability. | 2LoD MRM (independent). |
| **2. Validation — Ongoing monitoring** | Process verification (code review, pipeline checks) and benchmarking against alternatives. | Measure 3 (tracking risks over time); Manage 4 (monitoring plan). | Lifecycle objectives; drift monitoring. | 2LoD MRM + 1LoD. |
| **2. Validation — Outcomes analysis** | Back-testing, sensitivity, challenger-model comparisons; fair-lending analysis for credit models. | Measure 2 (seven trustworthy characteristics), Measure 3. | Objectives on robustness and performance; bias-related objectives. | 2LoD MRM. |
| **3. Governance, policies, controls — Oversight** | Board / senior management accountability; risk-appetite statements. | Govern 1, Govern 2. | Objectives on accountability and governance. | Board, senior mgmt. |
| **3. Governance, policies, controls — Model inventory** | Complete, current inventory with risk tier per model. | Govern 1 (inventory); outputs from the intake process (see `mod-102`). | Lifecycle-management objectives. | 2LoD MRM. |
| **3. Governance, policies, controls — Documentation** | Development doc, validation report, model use doc, change log. | Model card, risk assessment, Measure/Manage artefacts. | Lifecycle-management objectives. | 1LoD + 2LoD. |
| **3. Governance, policies, controls — Policy** | Written model-risk-management policy; roles; frequency of validation. | Govern 1 (AI policy). | Objectives on governance. | 2LoD MRM. |

### 2.3 Worked SR 11-7 one-pager for SmallDollarUW

A one-pager the MRM team would recognise. Column references point at
the NIST artefacts the analyst has already produced.

**Model summary**

- **Name / ID**: SmallDollarUW v0.9 (pre-production).
- **Purpose**: Risk estimate for new-to-bank small-dollar personal loan
  underwriting.
- **Model type**: Gradient-boosted tree (XGBoost-family); supervised
  binary target (90+ dpd within 12 months).
- **Data**: Internal application data + consumer bureau pulls. Training
  window and population documented in the development report
  (section N — reference only).
- **Use**: Score drives (a) automated decline below cutoff with
  adverse-action notice per FCRA, (b) advisory to underwriter in
  border zone, (c) auto-refer above threshold.
- **Risk tier (internal MRM scale)**: High, due to consumer impact,
  automated adverse action, and fair-lending exposure.
- **AI RMF Map category outputs**: see assessment §Map 1–5.

**1. Development, implementation, use**

- **Development**: Development report covers data sourcing, feature
  derivation, target definition, exclusions, sampling and weighting,
  hyperparameter search, calibration. Assumptions listed with
  limitations (thin-file applicants under-represented, data recency
  caveat). (RMF Map 3, Map 4; Measure 1.)
- **Implementation**: Inference service with input schema validation,
  immutable model artifact, deterministic scoring, reason-code
  generation for adverse-action, SR-compliant logging. Change control
  requires 2LoD sign-off. (RMF Map 4, Manage 4.)
- **Use**: Underwriter policy specifies when human judgement
  overrides the score; override reasons captured. Score boundaries and
  limitations communicated in the user-facing SOP. (RMF Govern 1,
  Map 1.)

**2. Validation**

- **Conceptual soundness**: Independent MRM review of target choice,
  feature permissibility (ECOA / Reg B prohibited bases), monotonicity
  constraints, and fit of GBT to the problem. (RMF Measure 1, 4.)
- **Ongoing monitoring plan**: Monthly PSI on top features; weekly
  score-distribution monitoring; quarterly fair-lending AIR / SMD
  review on protected-class proxies (per internal fair-lending
  methodology — see FDIC / OCC fair-lending guidance for
  authoritative definitions). (RMF Measure 3; Manage 4.)
- **Outcomes analysis**: Back-test on a held-out out-of-time window.
  Challenger-model comparison against a logistic baseline. Fair-lending
  outcomes analysis on approve/decline and pricing effects for
  reportable segments per bank methodology. (RMF Measure 2, Measure 3.)

**3. Governance, policies, controls**

- **Oversight**: Model Risk Committee reviews initial validation and
  annual revalidation. Risk-appetite statement includes loss-rate and
  fair-lending tolerances. (RMF Govern 1, 2.)
- **Inventory**: Registered in the model inventory under tier High,
  with links to this risk assessment, development report, validation
  report, and model card. (RMF Govern 1.)
- **Documentation**: Development report, model card, this SR 11-7
  mapping, validation report, change log — all under version control.
- **Policy**: Model-risk policy sets revalidation cadence (annual for
  High-tier, with event-driven triggers) and explicit roles for 1LoD
  / 2LoD / 3LoD.

**Regulatory anchors (not exhaustive):** SR 11-7 (FRB); OCC 2011-12;
FDIC FIL-22-2017; ECOA / Regulation B; FCRA (adverse-action and
accuracy); UDAAP considerations. Jurisdiction-specific anchors
(state-level and non-US) to be added before go-live.

**Open items (block go-live):** complete 2LoD independent validation
sign-off; finalise fair-lending methodology sign-off with Compliance;
confirm reason-code wording with Legal for adverse-action notices.

### 2.4 Decision rationale

- **Mapping is additive, not a rewrite.** The NIST AI RMF 1.0
  assessment stays the authoritative source of truth; the SR 11-7
  mapping is a view onto it that the MRM team can consume. Running two
  parallel documents produces drift.
- **"Validation" means independent effective challenge.** SR 11-7
  expects validation to be done by parties with the skills, authority,
  and independence to challenge. First-line self-attestation is
  development, not validation; keeping them separated in the mapping
  prevents a frequent audit finding.
- **Model inventory is a governance element, but it is populated by
  intake.** The mapping points at the inventory already produced in
  `mod-102` rather than re-creating it.
- **Fair-lending analysis is called out in Outcomes analysis.** Credit
  models used in adverse-action decisions have ECOA / Reg B and FCRA
  obligations that go beyond NIST AI RMF's bias-management language.
  The mapping surfaces them; the bank's fair-lending methodology is the
  authoritative source for thresholds.
- **Open items block go-live; they are not scored down.** SR 11-7
  mapping is used at validation and then at change / revalidation.
  Half-done items are flagged as go-live blockers rather than treated
  as closed.

## 3. Validation steps

1. Confirm every SR 11-7 element in the table has a NIST AI RMF
   artefact and an owner. Missing owners usually mean the mapping is
   aspirational.
2. Confirm "validation" rows name an independent party. If the owner
   column lists the developing team, the row violates SR 11-7's
   effective-challenge requirement.
3. Confirm the model inventory entry already exists (from `mod-102`)
   and links back to this mapping. If the inventory entry is "to be
   created", the governance element is not satisfied.
4. Confirm the use case triggers any applicable fair-lending (ECOA /
   Reg B, FCRA adverse action, UDAAP) analysis; a credit model that
   does not have fair-lending outcomes analysis will fail SR 11-7's
   outcomes-analysis expectation.
5. Confirm documentation inventory is complete: development report,
   validation report, model card, change log, and this mapping. SR
   11-7 is explicit that documentation is a governance element.
6. Confirm revalidation cadence and triggers (time-based and
   event-based) are stated in the governance row. Silence here is
   common and produces findings.
7. Peer review with the bank's actual MRM policy: the mapping must use
   the bank's model-risk tier definitions and thresholds, not the
   reference wording in this solution.

## 4. Rubric or review checklist

Score each SR 11-7 element out of 4; passing average ≥ 3.0 and no 0s.

| Score | Criterion |
| --- | --- |
| **4 — strong** | Element mapped to a specific NIST artefact and ISO/IEC 23894 objective; owner named; traceable to a produced document. |
| **3 — acceptable** | Mapped to an artefact; owner named; document exists in draft. |
| **2 — weak** | Mapped to a generic NIST reference ("Govern function") rather than a specific sub-category; or owner is a team, not a role. |
| **1 — poor** | Element present on paper but no artefact exists; revalidation cadence absent. |
| **0 — fail** | Development team listed as the validator; or governance element omitted; or fair-lending analysis absent on a credit model. |

Mapping-level checks (binary, all must pass):

- [ ] Three elements (Development/Implementation/Use; Validation;
      Governance) all present.
- [ ] Validation owner is independent of development.
- [ ] Model inventory entry exists.
- [ ] Documentation set is listed (dev report, validation report,
      model card, change log, this mapping).
- [ ] Revalidation cadence (time + event triggers) is stated.
- [ ] Fair-lending outcomes analysis is named for credit / pricing /
      collections models.
- [ ] Bank's own model-risk policy is cited — not only SR 11-7.

## 5. Common mistakes

- **Treating SR 11-7 as a bank-of-boxes checklist.** The guidance is
  principles-based. Analysts who tick boxes without evidence produce
  mappings that validation teams reject.
- **Calling 1LoD review "validation".** SR 11-7 is explicit that
  validation must be effective challenge by independent parties.
  Self-validation is development.
- **Forgetting fair-lending analysis on credit models.** ECOA / Reg B
  and FCRA do not appear on the NIST AI RMF 1.0 face directly; SR 11-7
  outcomes analysis is where they land for US consumer-credit use.
- **Missing the model inventory link.** SR 11-7 governance explicitly
  requires an inventory. A mapping that does not point to the inventory
  record will fail the governance element.
- **No revalidation cadence.** A one-time validation is not enough;
  cadence and event triggers are required by the governance element.
- **Assuming SR 11-7 only applies to traditional statistical models.**
  SR 11-7's definition of "model" is broad enough to cover ML and
  increasingly LLM-assisted decision models. If output drives a
  decision, SR 11-7 applies.
- **Confusing SR 11-7 with the OCC's parallel bulletin or the FDIC
  FIL.** They are substantively the same guidance across the three
  regulators; cite whichever the bank's primary federal regulator uses.

## 6. References

- Board of Governors of the Federal Reserve System, _SR 11-7:
  Guidance on Model Risk Management_, 4 April 2011. <http://web.archive.org/web/20260414150921/https://www.federalreserve.gov/supervisionreg/srletters/sr1107.htm>
- Office of the Comptroller of the Currency, _OCC Bulletin 2011-12:
  Supervisory Guidance on Model Risk Management_. <http://web.archive.org/web/20260416232808/https://www.occ.gov/news-issuances/bulletins/2011/bulletin-2011-12.html>
- FDIC, _FIL-22-2017: Adoption of Supervisory Guidance on Model Risk
  Management_. <https://www.fdic.gov/news/financial-institution-letters/2017/fil17022.html>
- NIST, _AI Risk Management Framework (AI RMF 1.0)_, January 2023 —
  the functions used in the mapping. <https://www.nist.gov/itl/ai-risk-management-framework>
- NIST, _AI 600-1: Generative AI Profile_, July 2024 — referenced where
  the use case uses generative components. <https://nvlpubs.nist.gov/nistpubs/ai/NIST.AI.600-1.pdf>
- ISO/IEC 23894:2023 — _Information technology — Artificial
  intelligence — Guidance on risk management_ — objectives used in the
  mapping. <https://www.iso.org/standard/77304.html>
- CFPB, _Consumer Financial Protection Circular 2022-03: Adverse
  action notification requirements in connection with credit
  decisions based on complex algorithms_ — supports the FCRA
  adverse-action references in §2.3. <https://www.consumerfinance.gov/compliance/circulars/circular-2022-03-adverse-action-notification-requirements-in-connection-with-credit-decisions-based-on-complex-algorithms/>
