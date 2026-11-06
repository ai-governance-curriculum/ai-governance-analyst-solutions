# Exercise 03 — FRIA First-Draft Drill — Reference Solution

## 1. Solution overview

**FRIA** stands for *Fundamental Rights Impact Assessment*, the
obligation created by **Article 27** of the EU AI Act (Regulation
(EU) 2024/1689). It is a **deployer**-side obligation — not the
provider's — and it is **mandatory** for specific actors deploying
high-risk AI systems. It is not a reusable risk assessment: it asks a
narrow, Article-27-shaped set of questions, and the deployer must
notify the market surveillance authority of the results on the EU
template (Art. 27(3)).

Article 27 applies to deployers that are:

- bodies governed by public law, or private entities providing public
  services; or
- deployers of the high-risk AI systems referred to in Annex III
  points 5(b) (credit scoring / creditworthiness evaluation, excluding
  fraud detection) and 5(c) (risk assessment and pricing for life and
  health insurance).

Other deployers of high-risk AI systems are not required to run a FRIA
under Article 27, though they may perform an equivalent assessment to
support their Art. 26 obligations.

This solution delivers:

- A worked FRIA first draft for a reference scenario (`EuroCreditScore`,
  a bank's consumer-credit scoring model deployed in the EU — Annex III
  5(b)).
- A one-to-one mapping of the FRIA sections to the six elements
  Article 27(1)(a)–(f) requires.
- Decision rationale and rubric.

Scope note: this is a first-draft FRIA, not the final submission to a
market-surveillance authority. The final version uses the template the
AI Office will publish under Art. 27(5); the structure here anticipates
that template at the content level.

## 2. Worked answer or implementation

### 2.1 Reference system (local exercise context)

> **System name**: EuroCreditScore (fictional).
> **Deployer**: A medium-sized retail bank with branches across three
> EU member states (reference scenario — not a real bank).
> **Provider**: Vendor-provided high-risk AI system, CE-marked;
> provider has delivered the Article 13 instructions for use.
> **Purpose**: Score consumer applications for revolving credit and
> personal loans; classify the application into "approve / refer /
> decline" with a continuous score.
> **Annex III category**: point 5(b), creditworthiness / credit scoring.
> **Decision role**: Advisory for "refer"; mostly automated for
> extreme-high and extreme-low scores; final action always touched by
> an underwriter in the referred band.
> **Scope of this FRIA**: pre-deployment in the three EU member states;
> assessment valid pending material change per Art. 27(2).

The scenario is constructed for the exercise.

### 2.2 Article 27(1) element-by-element draft

Headings map directly to the six elements of Article 27(1).

#### (a) Description of the deployer's processes in which the high-risk AI system will be used, in line with its intended purpose

- EuroCreditScore is embedded in the bank's consumer-credit origination
  workflow. The customer completes an application in-branch or online;
  bureau and internal data populate the input vector; the system
  produces a score and a band (approve / refer / decline).
- "Approve" and "decline" bands currently flow through to the
  decisioning engine without an underwriter in the loop for amounts
  below a threshold. "Refer" always routes to a human underwriter.
- Adverse-action communication is drafted from the provider's
  reason-code output and the bank's letter templates; the customer
  receives a plain-language summary of the main factors and the right
  to request human review (Art. 22 GDPR, and Art. 26(11) of the AI
  Act which requires deployers to inform affected persons that they
  are subject to the use of a high-risk AI system).
- Internal re-reviews and appeals go to a separate underwriting-review
  queue.

#### (b) Period of time and frequency of use

- Deployment is open-ended; the system runs on every eligible
  application, approximately high-volume daily throughput across the
  three member states.
- FRIA is reviewed on material change (model refresh, new product
  segment, new country, new data source) and at a minimum annually.

#### (c) Categories of natural persons and groups likely to be affected

Primary affected group: consumer applicants for revolving credit and
personal loans in the three EU member states.

Sub-groups that merit named attention because they are identifiable
under EU fundamental-rights instruments (Charter Articles 20–21 —
equality and non-discrimination — and GDPR special-category
protections):

- Applicants protected under Charter Art. 21 non-discrimination
  (race/ethnic origin, sex, religion, disability, age, sexual
  orientation).
- Applicants in receipt of social benefits (proxy signal in bureau
  data).
- Applicants new to the country (thin-file credit history).
- Applicants in rural branches (digital-channel disparity).
- Applicants near statutory thresholds (e.g., consumer-credit cap).

#### (d) Specific risks of harm (using Art. 13 information from the provider)

Each risk below cites the Art. 13 information the provider delivered
with the system where it is relevant.

| Risk | Fundamental right engaged (Charter) | Severity | Likelihood | Evidence / Art. 13 reference |
| --- | --- | --- | --- | --- |
| Disparate decline rate between protected and reference groups. | Art. 21 non-discrimination; Art. 20 equality. | High | Medium | Provider's performance report by subgroup (Art. 13 instructions for use); bank's own fair-lending backtest. |
| Thin-file applicants systematically referred, then timed-out. | Art. 20 equality. | Medium | Medium | Provider's known-limitations section. |
| Reason codes insufficient to allow the applicant to contest. | Art. 41 right to good administration (analogue in private sector), GDPR Art. 22(3) right to human review and to contest. | Medium | Medium | Provider's reason-code glossary; bank's letter template review. |
| Automatic decline on edge-case scores driven by a feature that is a proxy for a protected attribute. | Art. 21 non-discrimination. | High | Low (with mitigation) | Provider's feature list and SHAP global importances. |
| Poor model behaviour on very low-income applicants (sparse training coverage). | Art. 20 equality; Art. 34 social security and social assistance (as a contextual value). | Medium | Medium | Provider's data-distribution statistics. |
| Data-poisoning or model-skewing attack on the vendor's upstream pipeline. | Indirect — same set, via disparate impact. | High | Low | Vendor's security attestation; mapped to OWASP ML Top 10 and MITRE ATLAS. |

Risks of harm are identified at the intersection of (i) the categories
of persons in (c) and (ii) the information the provider gave the
deployer under Art. 13 — as Art. 27(1)(d) explicitly requires.

#### (e) Human oversight measures implemented according to the instructions for use

- Human underwriter is required on all "refer" decisions.
- Mandatory underwriter review on any automated decline whose principal
  reason code is a proxy-prone feature (vendor-flagged list).
- Four-eyes review on any decline for applicants in flagged sub-groups
  (e.g., thin-file).
- Underwriters have access to the model's reason codes and to a
  contrastive explanation per decision.
- Stop-use switch: an operational control to disable the model and
  fall back to full human underwriting, owned by the Chief Credit
  Officer.
- Training: underwriters trained against automation bias; retraining
  cadence and content documented.
- The deployer confirms the human-oversight measures match the
  provider's Art. 13 instructions for use.

#### (f) Measures in case of risk materialisation, internal governance, and complaint mechanisms

- Complaint intake: the bank's existing consumer complaints function
  (a regulated process under the member states' consumer-credit
  supervisory regime) receives automated-decision complaints on a
  dedicated route.
- Right to human review: every applicant notified at the point of
  decision of the right to request human review (Art. 22 GDPR plus
  Art. 26(11) AI Act notification).
- Internal governance: the FRIA is owned by the Head of Compliance;
  reviewed by the Fair Lending Committee quarterly; material changes
  require sign-off by the Chief Credit Officer and the DPO.
- Notification duties: results of this FRIA notified to the market
  surveillance authority on the AI Office template (Art. 27(3));
  serious incidents reported under Art. 73.
- Rollback: any monitoring signal exceeding a defined threshold (e.g.,
  subgroup FPR drift) triggers the stop-use switch while a full review
  runs.
- Redress: in-force statutory consumer-credit redress mechanisms and
  the data-protection complaint path to the supervisory authority.

### 2.3 Submission package (for market-surveillance notification)

Article 27(3) requires the deployer to notify the market-surveillance
authority of the results via the AI Office template once it is
published. In the interim, the submission package consists of:

- Signed FRIA (sections (a)–(f) above).
- Linked DPIA under GDPR Art. 35 (see `mod-105/exercise-04`).
- Copy of the provider's Art. 13 instructions for use and the
  conformity-assessment summary (declaration of conformity reference).
- Register entry in the EU database of high-risk AI systems (Art. 71).

### 2.4 Decision rationale

- **FRIA is not the DPIA.** They overlap but are not substitutes; the
  DPIA is about processing of personal data, the FRIA is about
  fundamental-rights impact of using the system. `mod-105/exercise-04`
  maps the overlap.
- **FRIA is not the deployer's risk assessment.** Art. 27 asks six
  specific questions; the FRIA answers those questions and cites the
  broader risk assessment rather than reproducing it.
- **The provider's Art. 13 information is a required input.** A FRIA
  that does not cite the provider's instructions for use, limitations,
  and intended purpose is incomplete per Art. 27(1)(d).
- **"Categories of persons" has to go beyond "our customers".**
  Non-discrimination grounds under Charter Art. 21 are enumerated;
  other identifiable groups (thin-file, rural, low-income) matter
  because they are often disparately affected even when they are not
  protected by name.
- **Human oversight measures must be deployer-side and operational.**
  Writing "the vendor says a human is in the loop" is not an answer to
  27(1)(e); the deployer must describe the oversight they themselves
  implement.
- **The EU AI Office template drives the final form.** Article 27(5)
  tasks the Office with publishing the template. The structure above
  is the content model; the final submission will reshape it to the
  Office's template.

## 3. Validation steps

1. Confirm the system is in-scope for Art. 27 by checking: (a) deployer
   is a public body / private entity providing public services, OR
   (b) system is in Annex III points 5(b) or 5(c). If neither applies,
   label the artefact as a "voluntary FRIA-style assessment"; do not
   claim it is required under Art. 27.
2. Confirm an Art. 13 instructions-for-use document from the provider
   exists; if it does not, a compliant FRIA cannot be produced.
3. Check that every one of the six Art. 27(1) elements (a)–(f) is
   answered. None may be left blank.
4. For every risk in (d), confirm it is tied to a specific category of
   persons from (c). Risks with no owner-category are miscoded as
   generic risks rather than fundamental-rights risks.
5. For every human-oversight measure in (e), confirm it matches or
   exceeds the provider's Art. 13 instructions; flag divergences.
6. Confirm (f) names a complaint channel that actually receives
   automated-decision complaints — not a generic "customer care"
   mailbox.
7. Confirm the FRIA is linked to the DPIA and to the register entry
   (Art. 71).

## 4. Rubric or review checklist

### Per-element scoring (0–3) for each of (a)–(f)

| Score | Criterion |
| --- | --- |
| **3 — strong** | Element answered; answer is specific to this deployment; evidence is cited. |
| **2 — acceptable** | Element answered but with generic language reusable across any FRIA. |
| **1 — weak** | Element present but fails to connect to (c) or to provider Art. 13 content. |
| **0 — fail** | Element missing, or (d) risks are the deployer's generic risk register pasted in without the Charter / Art. 13 anchoring. |

### FRIA-level checks (binary, all must pass)

- [ ] Scope statement names Article 27 applicability (in-scope or
      voluntary) with the specific clause / Annex III point cited.
- [ ] Provider's Art. 13 instructions for use are referenced.
- [ ] Charter articles are named for each risk in (d) where a right
      is engaged.
- [ ] Human oversight (e) is deployer-side and operational, not just a
      quote from the vendor.
- [ ] Complaint mechanism (f) has a named intake channel and a named
      owner.
- [ ] Notification to the market-surveillance authority is listed as a
      deliverable with a due date; EU-database registration (Art. 71)
      is noted.
- [ ] Cross-reference to the DPIA is present or an explicit "no
      personal data processing in scope" is recorded (rare for Annex
      III 5(b)/5(c)).

## 5. Common mistakes

- **Treating the FRIA as a copy of the risk assessment.** Art. 27 asks
  six questions. Answer them.
- **"Categories of persons" = "our users".** Art. 27(1)(c) wants
  identifiable sub-groups, not the whole population label.
- **Writing (d) without citing Art. 13.** The statutory link between
  (d) and the provider's information is explicit; auditors look for
  it.
- **Human oversight lifted from the vendor datasheet.** The deployer
  must describe the oversight the deployer implements; a quote is not
  an implementation.
- **Confusing the DPIA with the FRIA.** They overlap on affected-
  persons content, but a DPIA is processing-centric and a FRIA is
  system-centric. See `mod-105/exercise-04`.
- **Skipping the notification duty.** The FRIA has to be notified to
  the market-surveillance authority (Art. 27(3)).
- **Claiming Art. 27 does not apply when it does.** Annex III 5(b) and
  5(c) deployers are in-scope regardless of public/private status.

## 6. References

- Regulation (EU) 2024/1689 of the European Parliament and of the
  Council (the EU AI Act), Articles 26 (deployer obligations), 27
  (fundamental-rights impact assessment), 13 (transparency and provision
  of information to deployers), 71 (EU database of high-risk AI
  systems), 73 (serious-incident reporting), and Annex III.
  <https://eur-lex.europa.eu/eli/reg/2024/1689/oj>
- Charter of Fundamental Rights of the European Union, especially
  Articles 20 (equality before the law) and 21 (non-discrimination).
  <https://eur-lex.europa.eu/eli/treaty/char_2012/oj>
- Regulation (EU) 2016/679 (GDPR), Articles 22 (automated individual
  decision-making) and 35 (data protection impact assessment).
  <https://eur-lex.europa.eu/eli/reg/2016/679/oj>
- NIST, *AI Risk Management Framework (AI RMF 1.0)*, January 2023 —
  Map 5 "Impacts to individuals, groups, communities, organizations,
  and society" as a methodological complement.
  <https://www.nist.gov/itl/ai-risk-management-framework>
- OWASP Machine Learning Security Top 10 — security framing for the
  attack-path items feeding (d).
  <https://owasp.org/www-project-machine-learning-security-top-10/>
- MITRE ATLAS — adversarial-ML tactics used to characterise (d) risks
  of deliberate harm. <https://atlas.mitre.org/>
