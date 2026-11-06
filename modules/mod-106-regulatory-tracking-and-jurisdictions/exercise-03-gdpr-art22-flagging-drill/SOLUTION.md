# Exercise 03 — GDPR Article 22 Flagging Drill — Reference Solution

## 1. Solution overview

GDPR Article 22 governs **solely automated decision-making, including
profiling, which produces legal effects concerning the data subject or
similarly significantly affects them**. The flagging drill is not an
Article 22 compliance assessment — that belongs to the DPO and Legal
with the controller. The drill's job is narrower and more operational:
given a short description of an AI system, the analyst decides whether
the system is a **candidate** for Article 22 scope and routes it for a
full assessment.

Getting this flagging step right matters because:

- **False negatives** (a system is in scope and the flag is "no")
  mean the controller ships a system that owes Art. 22(3) data-subject
  rights it is not providing — the single most expensive GDPR AI
  finding in recent enforcement.
- **False positives** (a system is routed when it should not be)
  swamp the DPO queue and erode the signal of the flag itself.

This solution provides:

- A four-question flagging worksheet that mirrors the Article 22(1)
  test as clarified by the CJEU in **SCHUFA (C-634/21, 7 December
  2023)** and by the Article 29 Working Party / European Data
  Protection Board guidance on automated decision-making and profiling
  (WP251rev.01, adopted 6 February 2018, endorsed by the EDPB).
- Six worked examples across likely drill cases.
- A rubric and a worked routing memo.

Article 22 sits alongside, not on top of, the EU AI Act (`mod-106`
exercise-02). A system can be flagged under Art. 22 and not be
"high-risk" under the AI Act, or vice versa.

## 2. Worked answer or implementation

### 2.1 The Article 22(1) test, operationalised

Article 22(1) prohibits a decision **based solely** on automated
processing that produces **legal effects** or **similarly
significantly affects** the data subject, unless one of the Art.
22(2) exceptions applies (contract necessity, EU/MS law
authorisation, explicit consent). Where processing uses **special-
category data** (Art. 9), Art. 22(4) requires Art. 9(2)(a) explicit
consent or Art. 9(2)(g) substantial public interest, with suitable
measures.

Four questions. **A "yes" to all four** flags the system; a "likely"
on any routes the system to the DPO for the full determination.

**Q1. Is there personal data in the decision input?**

- Yes if the input includes identifiers, pseudonymous IDs,
  behaviour patterns tied to a person, or any feature derived from
  personal data.
- Answering "no" closes the drill; Art. 22 does not bite on
  non-personal processing.

**Q2. Does the system produce a decision (not just an output)?**

- "Decision" means an outcome that is acted on — approved / denied,
  scored into a band used to approve or deny, prioritised in a queue
  that determines access, flagged into a review pathway.
- Mere outputs (a ranking displayed for a human to consider among
  other inputs) are not decisions in themselves. **But** — see Q3 —
  the reality of human involvement matters more than the label.

**Q3. Is the decision "based solely" on automated processing?**

- The CJEU's SCHUFA judgment reads "based solely" broadly: where the
  automated output is used by a third party in a manner that is
  determinative, the automated producer can itself be treated as
  making an Art. 22 decision, even if a human downstream rubber-
  stamps it.
- EDPB WP251rev.01 is explicit that nominal human involvement that
  does not involve a person with authority and competence reviewing
  the specific case does not take a decision out of Art. 22.
- Factors indicating "solely":
  - Reviewers do not have the time to do more than approve.
  - Reviewers lack the data or authority to overturn.
  - Policy caps reviewer deviation from the model output.
  - Reviewer-overturn rate is near zero.
- Factors suggesting meaningful human involvement:
  - Reviewers have standing authority to overturn, apply judgement,
    and request more information.
  - Specific-case review with documented reasoning.
  - A positive, measured overturn rate consistent with substantive
    review (the figure is a smell test, not a bright line — WP251
    does not set a threshold).

**Q4. Does the decision produce legal effects or similarly
significantly affect the data subject?**

- **Legal effects** examples from WP251rev.01: contract formation
  or termination; benefits entitlement; citizenship or residency
  status; access to a service that is a legal entitlement.
- **Similarly significant effects** examples from WP251rev.01:
  credit decisions; employment recruitment, promotion, or
  termination; access to education; access to housing; access to
  insurance; online targeting of especially vulnerable groups;
  decisions with lasting or substantial consequences for behaviour
  or choices.
- **Not "similarly significant"** by WP251rev.01 default: generic
  advertising that does not narrowly target an individual; routine
  content ranking with no gating consequence; minor price
  variations that do not foreclose participation.

A **Q2/Q3 "likely"** triggers the DPO route. A **Q4 "yes"** with
"likely" on Q2 or Q3 is the single most expensive case not to route.

### 2.2 The flagging worksheet (template)

```
System under drill: ______________________________  Analyst: ______
Inventory id: __________  Date: __________  DPO ref: __________

Q1. Personal data in decision input?           [ ] Yes   [ ] No
    Evidence:

Q2. Produces a decision (not just an output)?  [ ] Yes   [ ] Likely   [ ] No
    Evidence (acted-on outcome; reviewer workflow):

Q3. "Solely" automated?                        [ ] Yes   [ ] Likely   [ ] No
    Evidence (reviewer authority, time, overturn rate; policy caps;
    SCHUFA posture for third-party downstream reliance):

Q4. Legal or similarly significant effect?     [ ] Yes   [ ] Likely   [ ] No
    Evidence (contract, benefit, employment, credit, insurance,
    housing, education, essential-service gating):

Flag decision:
  [ ] Route to DPO — Art. 22 candidate (full determination required)
  [ ] Not routed — rationale:
Special-category (Art. 9) data involved?       [ ] Yes   [ ] No
Also flagged separately under EU AI Act?       [ ] Yes   [ ] No    (see mod-106 ex-02)
Routing notes:
```

### 2.3 Six worked cases

Each case is a short scenario the drill is likely to present.

**Case A — Consumer credit application.** An online lender uses a
credit-scoring model to produce an approve / decline decision. A
human reviewer sees only the recommendation and typically approves
in under 30 seconds; policy caps manual overrides at 2% of denials.

- Q1 personal data: **yes** (identifiers, bureau data).
- Q2 decision: **yes** (approve / decline).
- Q3 solely: **yes** (30-second rubber-stamp; 2% cap on overrides).
- Q4 effect: **yes** (credit — WP251rev.01 explicit example).
- **Flag: route — Art. 22 candidate.** Controller must rely on an
  Art. 22(2) exception and implement Art. 22(3) measures (right to
  obtain human intervention, express a view, contest the decision).

**Case B — CV screening with human recruiter.** A resume-ranking
assistant produces a 0–100 score; recruiters read the top 50 scores
per req, interview a shortlist, and make hiring decisions. Recruiters
regularly review candidates with lower scores when a hiring manager
asks. Reviewer-overturn rate on shortlist membership is substantial.

- Q1: **yes** (CV data).
- Q2: the system outputs a ranking; the hiring decision is the
  recruiter's. **No** on a strict reading; **likely** on a
  SCHUFA-aware reading if recruiters never surface candidates below
  a threshold.
- Q3: **likely** (hinges on whether recruiter review is substantive).
- Q4: **yes** (employment — WP251rev.01 explicit example).
- **Flag: route — Art. 22 candidate.** Even if Art. 22 does not
  ultimately apply, the Art. 13/14 transparency and Art. 15 access
  rights still run, and the AI Act Annex III(4) high-risk duties
  layer on.

**Case C — Fraud-triage queue.** A transaction-fraud model scores
transactions; a human analyst reviews scores above a cut-off and
approves or declines. Below the cut-off, transactions auto-approve.

- Q1: **yes**.
- Q2: **yes** on auto-approve (acted-on outcome below the cut-off)
  and **yes** on auto-decline at the extreme-risk cut-off; mixed on
  the mid-range queue where a human decides.
- Q3: **yes** on the auto branches; **no** on the manually-reviewed
  branch where the analyst has substantive authority.
- Q4: **yes** on decline (service gating; funds frozen); likely
  **yes** on a repeat-false-positive pattern that forecloses
  participation; weaker on a single auto-approve.
- **Flag: route — Art. 22 candidate** for the decline paths. The
  manually reviewed branch flows out of Art. 22 but still owes Art.
  5(1)(a) transparency and Art. 15 rights.

**Case D — Generative support assistant.** A chatbot drafts responses
that human support agents send, with full editorial authority. The
assistant does not gate access to anything.

- Q1: **yes** (personal data in the conversation).
- Q2: **no** — the agent decides.
- Q3: N/A.
- Q4: N/A.
- **Flag: not routed.** Controller still owes Art. 5, Art. 13/14,
  Art. 15, and, if applicable, EU AI Act Art. 50(1) transparency.
  Art. 22 is not triggered on these facts.

**Case E — Dynamic pricing of an essential service.** A travel site
offers rail tickets with prices that vary by individual profile and
recent search behaviour; the user can only purchase at the offered
price.

- Q1: **yes**.
- Q2: **yes** (the offered price is the decision — purchase at that
  price or not at all).
- Q3: **yes** (no human in the loop).
- Q4: **likely yes** if the price variation is large enough to
  foreclose participation for some data subjects; **no** if the
  variation is trivial. WP251rev.01 notes significant price
  differentiation can be similarly significant.
- **Flag: route — Art. 22 candidate.** The route is the DPO's call
  on the materiality of the effect.

**Case F — Internal risk tiering for an AI governance programme.**
The AI governance team scores each in-scope system on a residual-
risk band (see `mod-103` exercise-03). No personal data is used.

- Q1: **no**.
- **Flag: not routed.** Art. 22 does not apply to processing that
  is not of personal data. The row is still tracked in the
  regulatory tracker and the risk register.

### 2.4 Worked routing memo (one page)

**To:** Data Protection Officer
**From:** AI Governance Analyst
**Re:** Art. 22 flagging — sys-0034-credit-score (Case A above)
**Date:** 2026-11-05

1. **Scope.** This memo routes `sys-0034-credit-score` for the full
   Art. 22 determination. It does not interpret Art. 22(2) or
   22(3) — that is Legal's and your call.
2. **Facts.**
   - Model produces an approve / decline recommendation for each
     online credit application.
   - Reviewer workflow: human reviewer presented with the
     recommendation; median review time 24 seconds; manual-override
     cap at 2% of denials per policy.
   - Data subjects: EU residents applying for consumer credit.
   - Special-category data: none in the input, but ethnicity proxies
     are plausible via ZIP / address-derived features (Art. 22(4)
     signal — route in parallel).
3. **Four-question read.**
   - Q1 yes; Q2 yes; Q3 yes (reviewer-time and cap evidence);
     Q4 yes (WP251rev.01 explicit example — credit).
4. **Recommendation.** Treat as Art. 22 in scope pending Legal's
   confirmation. In parallel:
   - Confirm an Art. 22(2) exception is available (consent, contract
     necessity, or EU/MS authorisation).
   - Design Art. 22(3) measures: a documented right to obtain human
     intervention, express the applicant's view, and contest the
     decision.
   - Integrate Art. 13(2)(f) and 14(2)(g) transparency language into
     the application flow.
   - Cross-flag under EU AI Act Annex III(5)(b) (creditworthiness)
     and reconfirm the deployer-side human-oversight (Art. 14) and
     deployer-notice (Art. 26) duties (see `mod-106` exercise-02).
5. **Not covered by this memo.** DPIA (Art. 35) is in scope for this
   system and is already ticketed. The fairness audit is the subject
   of a separate workstream (see `mod-104`).

### 2.5 Decision rationale

- **The drill flags, it does not decide.** Flagging is a routing
  action; the determination belongs to the DPO and Legal. A rubric
  that pushes the analyst toward issuing legal conclusions on Art.
  22 is wrong.
- **"Solely" is a substance question, not a workflow-label
  question.** SCHUFA and WP251rev.01 both deflate claims of human
  involvement that rely on a rubber-stamp step. The drill's
  evidence prompts (reviewer authority, reviewer time, cap on
  overrides, overturn rate) sit on top of this.
- **"Similarly significant" is a non-exhaustive list.** WP251rev.01
  gives examples. The analyst cites the examples that match and
  argues materiality for the ones that do not.
- **Special-category data forces a separate route.** Art. 22(4) adds
  the Art. 9 layer; the drill flags both branches so the DPO can
  see them together.
- **Art. 22 is independent of the EU AI Act classification.** A
  system can be "low-risk" under the AI Act (e.g. a limited-risk
  chatbot) and still be Art. 22 in scope (if it makes qualifying
  decisions). The analyst does not use the AI Act classification as
  a proxy for the Art. 22 answer.
- **Pseudonymous IDs count as personal data.** The temptation to
  answer Q1 "no" because the input is pseudonymised is the most
  common failure mode. GDPR Art. 4(1) and Recital 26 are explicit.

## 3. Validation steps

1. For every drilled system, the worksheet has evidence pointers
   under each of Q1–Q4, not just ticks.
2. "Likely" on Q2 or Q3 routes to the DPO; a worksheet that marks
   "likely" and does not route is a failure.
3. Reviewer-time and override-rate evidence is drawn from the
   reviewer-workflow log, not from policy documents alone (policy
   can describe a process that does not happen in practice).
4. Art. 9 special-category check is answered even when the system
   does not intentionally collect such data — the proxy question
   must be answered.
5. The memo cross-references the inventory id, the DPIA ticket,
   the EU AI Act classification (if in scope), and the risk-register
   row.
6. The flag is recorded in the regulatory tracker (see exercise-01)
   with `instrument_type: regulation`, `id: gdpr-2016-679`,
   `obligation_type: contestability`, `tracking_status: active-work`.

## 4. Rubric or review checklist

Grade the learner's drill answer on six dimensions, 3 points each
(total 18). Pass = total ≥ 13 with no 0.

| Criterion | 3 — strong | 2 — acceptable | 0–1 — weak |
|---|---|---|---|
| **Q1 personal data** | Correctly identifies personal data including pseudonymous IDs and proxy features; cites Art. 4(1) / Recital 26. | Identifies personal data but misses pseudonymous IDs. | Marks "no" because inputs are pseudonymised. |
| **Q2 decision** | Distinguishes decision from output; cites acted-on-outcome evidence. | Calls it a decision but with workflow-label evidence. | Treats every model output as a decision. |
| **Q3 "solely" reading** | Cites SCHUFA (C-634/21) and WP251rev.01 for substance over label; names reviewer-authority / time / override-rate factors. | Mentions substance but misses the SCHUFA angle. | Equates "there is a human in the loop" with "not solely automated". |
| **Q4 significance** | Maps to the WP251rev.01 examples; argues materiality for near-threshold cases. | Maps to WP251rev.01 but without materiality. | Asserts "not significant" without reasoning. |
| **Art. 9 check** | Flags proxy-based special-category risk even when direct collection is absent. | Flags only direct collection. | Does not consider Art. 9 at all. |
| **Routing** | Routes to the DPO with a memo that names the inventory id, DPIA ticket, EU AI Act parallel, and risk-register row. | Routes but with a weaker memo. | Flags and does not route, or routes without a memo. |

Document-level checks (binary, all must pass):

- [ ] Every flagged system has a DPO routing memo on file.
- [ ] No worksheet is "no" on Q1 because inputs are pseudonymised.
- [ ] No worksheet treats "there is a reviewer" as dispositive of Q3.
- [ ] The regulatory tracker row for `gdpr-2016-679` has a current
      `obligation` entry for the drilled system.

## 5. Common mistakes

- **"We have a human reviewer, so Art. 22 does not apply."** This is
  the most common failure. SCHUFA and WP251rev.01 both reject the
  nominal-human-review defence; substance controls.
- **"Our inputs are pseudonymised, so Art. 22 does not apply."**
  Pseudonymous data is personal data under Art. 4(5) and Recital 26.
- **"The model only ranks; it does not decide."** The decision is
  whether the downstream workflow treats the ranking as
  determinative. If the top-k slice is the only slice that proceeds,
  the ranking is the decision.
- **"The AI Act does not list this as high-risk, so Art. 22 does
  not apply."** The AI Act and GDPR run in parallel; neither
  pre-empts the other.
- **Flagging only for direct special-category collection.** Proxy
  features for race, religion, sexual orientation, health, and
  trade-union membership trigger the Art. 22(4) analysis even when
  the model does not ingest the special category directly. WP251
  rev.01 §V.B names proxy-driven profiling as a flag.
- **Treating the DPIA (Art. 35) as the Art. 22 analysis.** A DPIA
  is broader (any high-risk processing); Art. 22 is a specific duty
  with specific data-subject rights. They overlap but are not
  interchangeable.
- **No "Art. 22 candidate" status in the tracker.** The flag is
  evidence; it belongs in the tracker (`mod-106` exercise-01) with
  an owner and an expiry.
- **Writing the memo as a legal conclusion.** The analyst flags;
  Legal and the DPO decide.
- **Confusing Art. 22(2) grounds.** Contract necessity,
  EU/MS-law authorisation, and explicit consent are not
  interchangeable. The analyst's memo should route all three for
  Legal's call; the drill does not choose among them.

## 6. References

- Regulation (EU) 2016/679 — General Data Protection Regulation,
  Art. 22 (automated individual decision-making including
  profiling), Art. 4(1), 4(5), Recital 26 (personal data,
  pseudonymisation), Art. 9 (special categories), Arts. 13(2)(f)
  and 14(2)(g) (transparency), Art. 15(1)(h) (access), Art. 35
  (DPIA). <https://eur-lex.europa.eu/eli/reg/2016/679/oj>
- Court of Justice of the European Union, *OQ v. Land Hessen*
  (SCHUFA), C-634/21, judgment of 7 December 2023. <https://curia.europa.eu/juris/liste.jsf?num=C-634/21>
- Article 29 Data Protection Working Party, *Guidelines on
  Automated individual decision-making and Profiling for the
  purposes of Regulation 2016/679*, WP251rev.01, adopted 3 October
  2017 as revised 6 February 2018, endorsed by the EDPB. <https://ec.europa.eu/newsroom/article29/items/612053>
- EDPB, *Guidelines 3/2019 on processing of personal data through
  video devices* (for proxy-identification context). <https://edpb.europa.eu/our-work-tools/our-documents/guidelines/guidelines-32019-processing-personal-data-through-video_en>
- Regulation (EU) 2024/1689 — EU AI Act (for parallel-regime
  cross-check). <https://eur-lex.europa.eu/eli/reg/2024/1689/oj>
- NIST, *AI Risk Management Framework (AI RMF 1.0)*, January 2023 —
  `GOVERN 1.5` (legal and regulatory accountability) and
  `MAP 1.2`–`MAP 3` (contextual mapping used to source Q4 evidence). <https://www.nist.gov/itl/ai-risk-management-framework>
- OWASP Machine Learning Security Top 10 — referenced for
  reviewer-workflow abuse (ML05:2023 Model Theft, ML06:2023
  Corrupted Packages, ML09:2023 Output Integrity Attack) where
  attacks on the reviewer surface would push a system into the
  "solely automated" posture. <https://owasp.org/www-project-machine-learning-security-top-10/>
- MITRE ATLAS — referenced for adversary-technique vocabulary in
  describing failures of the reviewer workflow (e.g. T0043
  "Craft Adversarial Data" scenarios that bypass the reviewer). <https://atlas.mitre.org/>
