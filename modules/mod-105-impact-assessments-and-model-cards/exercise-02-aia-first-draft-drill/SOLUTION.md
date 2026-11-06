# Exercise 02 — AIA First-Draft Drill — Reference Solution

## 1. Solution overview

**"AIA" in this exercise** means the *Algorithmic Impact Assessment*
published by the Government of Canada under the Treasury Board's
*Directive on Automated Decision-Making*. The AIA is a public
questionnaire — hosted at
<https://canada.ca/en/government/system/digital-government/digital-government-innovations/responsible-use-ai/algorithmic-impact-assessment.html>
with the source on GitHub under an MIT licence — that produces an
**Impact Level** from I (little impact) to IV (very high impact). The
Impact Level then dictates the mitigation and transparency measures the
federal institution must put in place (the Directive's appendices
specify which measures apply at which level).

Many non-federal and non-Canadian organisations use the AIA as a lightweight
first-draft impact questionnaire even when the Directive does not apply
to them, because it:

- forces a concrete yes/no answer on 60+ risk and mitigation questions;
- produces a single number everyone can rally around; and
- uses a vocabulary that matches NIST AI RMF and ISO/IEC 42005 reasonably
  well.

This solution delivers:

- A worked AIA first draft for a reference high-impact system
  (`UIClaimsTriage`, an unemployment-insurance claim triage model), with
  answers to the AIA's six question blocks and an Impact Level call.
- Decision rationale explaining how each answer lands.
- A rubric for grading a learner's own first draft.

The system is fictional; the point of the drill is to exercise the
instrument, not to assess a real agency's practice.

## 2. Worked answer or implementation

### 2.1 Reference system (local exercise context)

> **System name**: UIClaimsTriage (fictional).
> **Owner**: A provincial unemployment-insurance agency (reference
> scenario). Not a real system.
> **Purpose**: Triage incoming UI claims into three lanes — auto-approve
> (fast lane), human-review (standard lane), fraud-investigation
> (adverse lane).
> **Model**: Gradient-boosted classifier trained on 5 years of claim
> and adjudication outcomes.
> **Decision role**: The model's output is advisory in the standard and
> adverse lanes (a human adjudicator must sign off), but a fast-lane
> score crosses the automated-approval threshold without further human
> review.
> **Users directly affected**: UI claimants (members of the public).
> **Users operating**: Agency adjudicators.
> **Scope of this assessment**: pre-deployment AIA on initial launch.

### 2.2 Questionnaire responses by AIA section

The AIA groups its questions into **six blocks** (titles below follow
the published instrument; wording is paraphrased):

1. Project — basic information and motivation.
2. Business drivers.
3. Risk profile.
4. Project authority (procurement, data, lifecycle).
5. About the system (model type, training, explainability).
6. About the data (sources, sensitivity, labelling).
7. Risk mitigation (consultation, de-risking, procedural safeguards).

The AIA scores each positive-risk answer upward and each positive-
mitigation answer downward, then converts the net score into Impact
Level I–IV using the published thresholds. The mapping published with
the instrument defines the cut-points; we paraphrase the resulting
level in words rather than quoting the thresholds verbatim.

**1. Project.**
- Title: UIClaimsTriage.
- Purpose: adjudicate UI claims faster while preserving human review on
  non-trivial cases.
- Expected go-live: pilot in one region before full rollout.

**2. Business drivers.**
- Volume: many claims per month at steady state; higher during
  recessions.
- Status-quo baseline: all claims currently 100% adjudicated by humans.
- Measured business outcome: median time-to-first-payment; adjudicator
  caseload.

**3. Risk profile (positive-risk answers that push Impact Level up).**
- *Does the decision affect rights of individuals?* Yes — the right to
  statutorily owed benefits.
- *Does the decision affect economic interests?* Yes.
- *Can the decision result in adverse impact on health, well-being, or
  environment?* Health and well-being, via delayed benefits.
- *Is the system intended to replace a human decision-maker?* Partial:
  fast-lane approvals bypass human review.
- *Is the automated decision reversible?* Yes, but the delay harm is
  not.
- *Vulnerable populations likely affected?* Yes — the whole claimant
  population includes people in acute financial need.
- *Line of business is life-critical?* No, but livelihood-critical.

**4. Project authority.**
- Data-governance and procurement sign-offs in place for the training
  data.
- Line of business is an executive agency with statutory authority.
- Off-the-shelf model or in-house? In-house training on in-house data;
  model code reviewed internally.

**5. About the system.**
- Explainability: SHAP feature attributions per prediction surfaced in
  the adjudicator UI; per-class global importance published internally.
- Decision thresholding: fast-lane threshold tuned conservatively on a
  held-out set; refreshed quarterly.
- Human-in-the-loop: present in standard and adverse lanes; **absent**
  on fast-lane auto-approvals (positive-risk answer).
- Appeals mechanism: statutory appeal path exists; AIA does not
  replace it.

**6. About the data.**
- Source: historical claims and adjudication outcomes from the agency's
  own systems.
- Sensitivity: contains employment history, sometimes medical reason
  codes; falls under provincial privacy law.
- Labels: supervised on historical adjudicator outcomes — therefore
  inherits any human bias in the training labels (positive-risk answer).
- Completeness: no linked outcomes for unsuccessful appeals in some
  provinces (positive-risk answer).

**7. Risk mitigation (negative-risk answers that push Impact Level down).**
- Published AIA report: yes.
- Peer-reviewed fairness evaluation across age, gender, Indigenous
  status, and region: yes (reference baseline).
- Notification to affected individuals that an automated system is
  used: yes, in claim communications and on the agency website.
- Option to request human review: yes — any fast-lane approval can be
  contested and re-adjudicated.
- Appeals pathway: preserved statutorily.
- Training for adjudicators: yes.
- Monitoring plan: drift and fairness monitoring, with a rollback
  switch.
- Gender-Based Analysis Plus (GBA+): completed.
- Contingency plan if the system is unavailable: fall back to 100%
  human adjudication.

### 2.3 Impact Level call

On the AIA's four-level scale:

- **Impact Level: III (high).** The combination of effect on statutory
  benefits, partial human-in-the-loop (fast-lane auto-approvals),
  bias-prone labels, and reach across the full claimant population pulls
  the score into Level III. A cleaner fast-lane design — e.g.,
  requiring a human co-sign on every fast-lane approval — would be the
  single change most likely to drop this to Level II.

A Level III system triggers the Directive's prescribed Level III
mitigations: among them, a **peer-reviewed independent review**
(technical and ethical), an **explanation of the decision in plain
language** to affected individuals, and a **published description of
how the system works**. The AIA output tells the governance analyst
what mitigation measures the agency has already committed to and which
are still outstanding.

### 2.4 Decision rationale

- **The AIA is a questionnaire, not a narrative.** The analyst
  completes every item, even when the answer looks obvious. Blank
  items invalidate the score.
- **Impact Level is a function of risk answers AND mitigation answers.**
  Teams that over-state mitigations to drag the score down fail
  review. Write mitigations down as "committed, owner, due date"
  rather than "we would / should / plan to".
- **The fast-lane design is the pivot point.** The AIA is particularly
  sensitive to whether the decision replaces a human decision-maker.
  If the fast lane required a human co-sign, this system would land in
  Level II and need materially less reporting.
- **Vulnerable-population framing matters.** UI claimants are not a
  "general public" use case for impact-assessment purposes; they are a
  population under financial stress, by definition.
- **The AIA does not substitute for the privacy assessment or for a
  FRIA/DPIA.** It feeds a different lifecycle gate. The analyst
  cross-references the sibling artefacts rather than duplicating them.

## 3. Validation steps

1. Complete every AIA question. Missing answers mean the computed
   Impact Level is not valid.
2. For every risk answer scored upward, confirm it is **supported by
   evidence** from the intake record, the risk assessment, or the
   model card. The AIA is not a vibes check.
3. For every mitigation answer scored downward, confirm there is a
   **named owner, an artefact, and a verification method** (e.g., the
   fairness peer review exists and has a signed report).
4. Walk the declared Impact Level against the Directive's appendix of
   required mitigation measures for that level. If any required
   measure is absent, note it as an open commitment, not as an
   invisible gap.
5. Peer check: hand the completed AIA to a reviewer outside the project
   team. They should be able to describe, in two sentences, what the
   system does and why it scored where it did.

## 4. Rubric or review checklist

### Per-answer scoring (0–3)

| Score | Criterion |
| --- | --- |
| **3 — strong** | Answer selected matches the system's actual state; a one-line justification cites the intake / risk assessment / model card. |
| **2 — acceptable** | Answer correct but justification is thin or missing. |
| **1 — weak** | Answer correct but justification contradicts a sibling artefact. |
| **0 — fail** | Answer incorrect, or mitigation answer takes credit for a control that does not actually exist. |

### AIA-level checks (binary, all must pass)

- [ ] Every AIA question is answered; none left blank.
- [ ] The system description (Section 1 of the AIA) matches the intake
      record.
- [ ] Risk answers are supported by cited evidence.
- [ ] Mitigation answers point to concrete artefacts (not plans).
- [ ] The computed Impact Level matches the Directive's thresholds for
      the given raw score.
- [ ] Required mitigation measures for the declared Impact Level are
      listed, each with a status (in place / committed / gap).
- [ ] Cross-references to risk assessment, DPIA (if applicable), and
      model card are present.
- [ ] A named approver with authority to accept the Level and its
      conditions is recorded.

## 5. Common mistakes

- **Taking mitigation credit for plans.** An un-shipped GBA+ is not a
  mitigation; it is a commitment.
- **Treating "partial human in the loop" as "yes, human in the loop".**
  The AIA is sensitive to whether the model's output ever flows to an
  irreversible action without a human in the path. Fast-lane auto-
  approvals are the classic trap.
- **Ignoring data provenance for labels.** Labels inherited from human
  adjudicators carry the biases of those adjudicators; the AIA has a
  specific question on this.
- **Skipping the vulnerable-population question.** This question moves
  the score meaningfully; it is the single most common silent omission.
- **Confusing Impact Level with a legal risk rating.** The AIA Level is
  a policy instrument of the Treasury Board Directive; it is not a
  cross-regulatory risk score. Keep it separate from FRIA / DPIA
  outputs.
- **Inflating or deflating to hit a target Level.** Governance teams
  that notice a pattern of "scored II after adjustment" from one line
  of business should audit.

## 6. References

- Government of Canada, *Directive on Automated Decision-Making*,
  Treasury Board of Canada Secretariat. The Directive and its
  appendices define the Impact Levels and the required mitigation
  measures per level.
  <https://www.tbs-sct.canada.ca/pol/doc-eng.aspx?id=32592>
- Government of Canada, *Algorithmic Impact Assessment tool*
  (questionnaire) and its source repository (MIT-licensed).
  <https://canada.ca/en/government/system/digital-government/digital-government-innovations/responsible-use-ai/algorithmic-impact-assessment.html>
- NIST, *AI Risk Management Framework (AI RMF 1.0)*, January 2023 —
  used for cross-walk vocabulary (Map 1 context; Map 5 impacts).
  <https://www.nist.gov/itl/ai-risk-management-framework>
- OWASP Machine Learning Security Top 10 — practitioner lens for
  model-level risks that feed the AIA's "about the system" block.
  <https://owasp.org/www-project-machine-learning-security-top-10/>
- MITRE ATLAS — adversarial-ML tactics referenced in misuse scenarios
  for the AIA risk block. <https://atlas.mitre.org/>
