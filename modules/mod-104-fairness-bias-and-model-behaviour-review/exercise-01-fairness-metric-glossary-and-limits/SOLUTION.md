# Exercise 01 — Fairness Metric Glossary and Limits — Reference Solution

## 1. Solution overview

Fairness metrics are shorthand for very different questions. A governance
analyst who treats them as interchangeable will approve systems that satisfy
one metric while violating another and will under-specify what a model team
should report. This solution delivers:

- A compact glossary of the fairness metrics an analyst must recognise on
  sight — formula, plain-English meaning, the "worldview" it assumes, and
  what it will not tell you.
- A mutual-exclusion note (Chouldechova / Kleinberg impossibility) so the
  analyst can explain to a council why three metrics cannot all be optimised
  at once on a non-trivial data set.
- A rubric for grading a learner's own glossary.

Scope: group fairness metrics on a binary classifier with a binary
sensitive attribute A ∈ {0,1}, true label Y ∈ {0,1}, and prediction
Ŷ ∈ {0,1}. Individual-fairness and causal-fairness notions are called out
at the end but not graded in detail.

Notation used throughout:

- TP, FP, TN, FN per group — true/false positives and negatives for group a.
- TPR = TP / (TP + FN) — true-positive rate (sensitivity, recall).
- FPR = FP / (FP + TN) — false-positive rate.
- PPV = TP / (TP + FP) — positive predictive value (precision).
- FNR = 1 − TPR, TNR = 1 − FPR, NPV = TN / (TN + FN).
- Base rate = P(Y = 1 | A = a).

## 2. Worked answer or implementation

### 2.1 Core glossary

| Metric | Formal definition | Plain English | Worldview it assumes | What it does NOT tell you |
|---|---|---|---|---|
| **Demographic parity (statistical parity)** | P(Ŷ = 1 \| A = 0) = P(Ŷ = 1 \| A = 1) | Selection rate is equal across groups. | "We Are All Equal" — base rates in the ground truth either are equal or should be treated as equal because the label itself is contested. | Whether errors are distributed fairly; whether the selected individuals are actually qualified. |
| **Disparate impact (ratio)** | P(Ŷ = 1 \| A = 0) / P(Ŷ = 1 \| A = 1) | Selection-rate ratio. Compared against a 0.80 threshold under the EEOC four-fifths rule of thumb. | Same as demographic parity; adds a US-employment-law threshold convention. | Causation; justification; any downstream outcome. A four-fifths pass is **not** a legal safe harbor. |
| **Equal opportunity** | TPR_A=0 = TPR_A=1 | Among people who *should* get a positive outcome (Y = 1), the rates of actually getting it are equal. | "What You See Is What You Get" — the label Y is a trustworthy signal of the underlying quality. | FPR disparities; whether the label Y itself is biased. |
| **Equalised odds** | TPR and FPR equal across groups | Both false negatives and false positives are distributed equally. | Same as equal opportunity, stricter: cares about harmed-when-innocent too. | Calibration; whether the base rate differs. |
| **Predictive parity (calibration within groups)** | PPV_A=0 = PPV_A=1 | Among people predicted positive, the probability they truly are positive is equal across groups. | Users of the score should be able to take it at face value regardless of group. | Error-rate balance; it is **incompatible** with equalised odds when base rates differ. |
| **Calibration (in general)** | For every predicted score s, P(Y = 1 \| Ŝ = s, A) is independent of A | A "70% risk" means the same thing for everyone. | Score consumers rely on the number's meaning, not on which group the subject is in. | Who gets the threshold cut — calibration says nothing about selection rates. |
| **Treatment equality** | FN/FP ratio equal across groups | The *mix* of errors is proportional across groups. | Both error types matter and their ratio is the meaningful quantity. | Absolute error rates; raw selection rates. |
| **Conditional use accuracy equality** | PPV and NPV equal across groups | Positive and negative predictions mean the same thing across groups. | Downstream decision-makers rely on prediction labels symmetrically. | Base-rate differences; selection-rate parity. |
| **Counterfactual fairness** | Ŷ unchanged under a counterfactual world in which A is flipped and nothing else that A causally drives changes | The decision would be the same if the person were in the other group, all else equal. | Causal model of the world is specified and credible. | How to validate the causal DAG you had to assume. |
| **Individual fairness** | Similar individuals (per a task-specific metric d) receive similar predictions | Similar inputs → similar outputs. | A fair similarity metric d(·,·) exists and is agreed. | Group-level impact; d itself is the whole problem. |

### 2.2 Known impossibilities and tensions

- **Chouldechova (2017) / Kleinberg–Mullainathan–Raghavan (2016)
  impossibility.** If base rates differ across groups and the predictor is
  not perfect, you cannot simultaneously achieve (a) calibration within
  groups, (b) equal false-positive rates, and (c) equal false-negative
  rates. Any two of the three are in tension with the third. Councils that
  demand "all fairness metrics pass" are asking for something mathematically
  unavailable on real data.
- **Demographic parity vs. equal opportunity.** When base rates differ,
  forcing equal selection rates will usually push TPRs apart (or vice
  versa). Pick the one tied to the harm theory.
- **Calibration vs. equalised odds.** A well-calibrated score across groups
  with different base rates will produce different FPR and FNR. Fixing one
  will break the other.
- **Group vs. individual.** A system can satisfy demographic parity while
  treating very similar individuals very differently across the group
  boundary (classic "fair through blind randomisation" failure).

### 2.3 How to pick the right metric

The metric follows the harm, not the other way round.

1. **Name the harm.** Allocation of a scarce resource? Punitive screening?
   Risk score consumed by a human decision-maker?
2. **Name the error that produces the harm.** False negatives (missed
   loans, missed diagnoses) vs. false positives (wrongful flag, wrongful
   arrest) vs. miscalibration (over- or under-stated risk).
3. **Map harm → metric.**
   - Scarce opportunity, missed-chance harm dominant → **equal
     opportunity** (equal TPR).
   - Punitive flag, wrongful-flag harm dominant → focus on **FPR equality**;
     with both harms live → **equalised odds**.
   - Score consumed as a probability by a downstream decision-maker →
     **calibration within groups** / **predictive parity**.
   - Legal screen for adverse impact in US employment → **disparate-impact
     ratio** against the 0.80 heuristic plus a substantive review.
   - "Representation" or contested label → **demographic parity**, with an
     explicit note that you are overriding the label.
4. **Write the trade-off down.** Document which other metrics will *not*
   be optimised and why that is acceptable.

### 2.4 Decision rationale

- **The glossary pairs a worldview with every metric.** Friedler, Scheidegger
  and Venkatasubramanian frame the choice as "We Are All Equal" vs. "What
  You See Is What You Get". Analysts who do not state a worldview end up
  arguing about numbers when the real disagreement is about whether the
  label Y should be trusted.
- **The "what it does NOT tell you" column is non-negotiable.** Most
  real-world governance failures come from treating a metric as sufficient
  when it is only necessary.
- **The four-fifths rule is listed but labelled a rule of thumb.** The EEOC
  Uniform Guidelines on Employee Selection Procedures (29 C.F.R. § 1607.4(D))
  describe it as a benchmark for an enforcement inquiry, not a legal
  safe-harbor threshold.
- **Impossibility results are front-and-centre.** A council that asks for
  "calibration and equal error rates and equal selection rates" is asking
  for something that does not exist on realistic data; the analyst must be
  able to say so.

## 3. Validation steps

1. For every metric in the glossary, confirm the formula and the "what it
   does NOT tell you" column against NIST AI RMF §3.4 (Fair — with harmful
   bias managed) and the NIST Special Publication 1270 three-category
   taxonomy of bias (systemic, statistical/computational, human-cognitive).
2. Confirm the impossibility statement by walking a 2×2 numerical example
   with different base rates: pick a confusion matrix per group, show
   calibration holds, then show TPR or FPR cannot also match.
3. For each metric, write one allocation scenario (loan approval, medical
   triage, resume screen) where it is the right lens and one where it is
   the wrong lens. If you cannot write both, the glossary entry is too
   abstract.
4. Peer check: ask a reviewer to pick a metric from the glossary and name
   its harm theory in one sentence. If they cannot, the rationale text is
   missing.

## 4. Rubric or review checklist

Score each glossary entry out of 3. Passing glossary averages ≥ 2.0 and
has no 0s.

| Score | Criterion |
|---|---|
| **3 — strong** | Formula correct; one-sentence plain-English meaning that a non-technical council member could use; worldview named; at least one concrete thing the metric does not tell you. |
| **2 — acceptable** | Formula correct; plain-English meaning present; either worldview or limitation column weak but not wrong. |
| **1 — weak** | Formula correct but no worldview and no limitation statement; or plain-English meaning drifts into a different metric. |
| **0 — fail** | Formula wrong; or metric conflated with a different metric (e.g., "demographic parity means equal TPR"); or "four-fifths rule" presented as a legal pass/fail threshold. |

Glossary-level checks (binary, all must pass):

- [ ] Both group-fairness and individual/counterfactual fairness are present,
      with individual-fairness clearly labelled as different in kind.
- [ ] The Chouldechova / Kleinberg impossibility is stated.
- [ ] At least one metric is explicitly marked as *incompatible* with at
      least one other metric listed.
- [ ] Four-fifths rule is present and correctly labelled as a rule of
      thumb from the EEOC Uniform Guidelines, not a safe harbor.
- [ ] Each metric has at least one scenario where it is the wrong choice.

## 5. Common mistakes

- **Conflating demographic parity with equal opportunity.** They coincide
  only when base rates are equal. Assume they diverge in practice.
- **Treating the four-fifths rule as a pass/fail legal test.** It is a
  screen that triggers further inquiry under the EEOC Uniform Guidelines,
  not a safe harbor.
- **Reporting a single ratio with no confidence interval or sample size.**
  A 0.78 impact ratio on 40 applicants is not the same signal as 0.78 on
  40,000; without a CI the analyst cannot distinguish noise from harm.
- **"The model is fair because we dropped the protected attribute."**
  Fairness through unawareness is widely known to fail when proxies for A
  remain in the feature set (ZIP code, school name, name embeddings).
- **Demanding every metric pass.** With different base rates this is
  impossible; it signals the analyst has not internalised the impossibility
  results.
- **Ignoring intersectionality.** A model can clear every pairwise group
  check and still harm an intersectional subgroup (e.g., Black women);
  the glossary should flag that the metrics are defined for whatever
  grouping you supply.

## 6. References

- NIST, *AI Risk Management Framework (AI RMF 1.0)*, January 2023 —
  §3.4 "Fair — with harmful bias managed" under the trustworthy
  characteristics. <https://www.nist.gov/itl/ai-risk-management-framework>
- NIST, *Special Publication 1270: Towards a Standard for Identifying and
  Managing Bias in Artificial Intelligence*, March 2022 — three-category
  taxonomy of bias. <https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.1270.pdf>
- U.S. EEOC, *Uniform Guidelines on Employee Selection Procedures*,
  29 C.F.R. Part 1607, §1607.4(D) — the four-fifths rule of thumb.
  <https://www.ecfr.gov/current/title-29/subtitle-B/chapter-XIV/part-1607>
- OWASP Machine Learning Security Top 10 — practitioner-facing framing of
  bias-related ML risks (ML02 Data Poisoning, ML08 Model Skewing) that
  intersect fairness metric choice. <https://owasp.org/www-project-machine-learning-security-top-10/>
