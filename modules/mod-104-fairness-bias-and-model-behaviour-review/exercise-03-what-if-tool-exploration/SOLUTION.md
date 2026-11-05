# Exercise 03 — What-If Tool Exploration — Reference Solution

## 1. Solution overview

The What-If Tool (WIT) is an interactive visual probe for trained ML
models, originally released as part of Google's People + AI Research
group and shipped as a TensorBoard plug-in / Jupyter + Colab widget. An
analyst uses it to:

- Inspect individual predictions and nearest counterfactuals.
- Compare performance slices across values of a feature (including a
  sensitive attribute).
- Move the classification threshold per group and watch confusion-matrix
  cells and fairness-criterion dashboards update live.
- Do partial-dependence probing — change one feature on one data point
  and observe the score shift.

This solution gives the analyst a repeatable exploration protocol, a
worked walk-through on an illustrative scenario, and a rubric for
grading a learner's write-up.

The worked example uses an illustrative binary classifier predicting
"candidate advances to interview" on a *synthetic* resume-screening data
set. No real company, dataset, or incident is claimed.

## 2. Worked answer or implementation

### 2.1 Exploration protocol

Run the five probes below in order. Each probe produces one artefact
(screenshot or copied table) and one written finding.

1. **Datapoint Editor — sanity ping.** Pick five randomly chosen rows.
   For each, read the features and the predicted score. Confirm the
   model's prediction matches what a domain expert would expect. If any
   row is already surprising, stop and investigate before continuing.
2. **Nearest counterfactual.** For a sample of 10 datapoints, flip the
   sensitive attribute A (or let WIT find the nearest counterfactual by
   L1/L2 distance). Record how many flip the decision. A high flip rate
   under a tiny feature change is a signal that A (or its proxies) is
   load-bearing.
3. **Partial-dependence probing.** For 3 datapoints, sweep one feature
   at a time (e.g., years_of_experience from 0–20) and record how the
   score moves. Look for cliffs and discontinuities that are hard to
   justify.
4. **Performance & Fairness tab — slice view.** Set the sensitive feature
   as the slicing attribute. Record per-group confusion-matrix cells,
   selection rate, and the available fairness criterion dropdowns
   (demographic parity, equal opportunity, equal accuracy).
5. **Threshold optimisation.** Switch the fairness-criterion dropdown
   between the available criteria and record how WIT re-sets the
   per-group threshold for each. The threshold table is the artefact
   the council needs to see — it shows the price of each fairness
   framing in explicit numbers.

### 2.2 Worked example (illustrative — synthetic data)

Set-up:

- Model: binary classifier, `advance_to_interview ∈ {0,1}`.
- Features: years_of_experience, degree_level, has_portfolio,
  prior_employer_tier, referral_source, applied_role_category,
  derived `candidate_group ∈ {A, B}`.
- Sensitive attribute for slicing: `candidate_group`.
- Holdout rows: 5,000 (approx. 3,800 A / 1,200 B).

#### Probe 1 — Datapoint Editor

Five randomly sampled rows reviewed. Four look reasonable; one is
surprising: a `years_of_experience = 2, has_portfolio = True,
prior_employer_tier = 1` candidate scores 0.18 while an otherwise
identical candidate with `prior_employer_tier = 3` scores 0.71. Finding
flagged for Probe 3.

#### Probe 2 — Nearest counterfactual by candidate_group

Of 10 sampled candidates, 3 have their decision flipped when the nearest
counterfactual swaps `candidate_group`. That is 30%, which is high. WIT
shows that the flipped rows differ from their counterfactuals on only
one or two additional features (`referral_source`,
`prior_employer_tier`), suggesting A has strong proxies in the feature
set rather than acting through a legitimate skill variable.

Written finding: "Group membership predicts the decision through
`prior_employer_tier` and `referral_source`. Removing
`candidate_group` from the training set will not fix this — those two
features will carry the signal."

#### Probe 3 — Partial-dependence sweep

Sweep `prior_employer_tier` 1 → 5 on three candidates. Score moves
0.18 → 0.41 → 0.36 → 0.68 → 0.71 on one candidate: a dip between
tiers 2 and 3 (non-monotone) and the largest jump, 0.32, between
tiers 3 and 4. The feature is not monotone in a justifiable way —
tiers are categorical employer brackets, and the model has learned a
sharp bracket boundary.

Written finding: "`prior_employer_tier` is a load-bearing feature with
a non-monotone, cliff-shaped effect. This is the most likely proxy
channel for `candidate_group`."

#### Probe 4 — Performance & Fairness slice (threshold 0.5)

Illustrative numbers:

```
candidate_group   count   selection_rate   TPR    FPR    accuracy
A                  3,800        0.34       0.72   0.14      0.81
B                  1,200        0.21       0.55   0.09      0.82
```

(The per-group accuracy figures are derived from the confusion-matrix
cells behind each row — prevalence ≈ 0.35 for A and ≈ 0.26 for B — so
selection rate, TPR, FPR, and accuracy are mutually consistent.)

Written finding: "At threshold 0.5, group B selection rate is 0.21 vs.
0.34 for A (ratio 0.62, below the four-fifths rule-of-thumb). TPR gap
0.17 — equal-opportunity view is the one that most directly maps to the
harm theory (missed interview invitations)."

#### Probe 5 — Fairness-criterion dropdown and threshold table

Switching the dropdown in WIT produces these per-group thresholds (all
illustrative):

```
Fairness criterion              threshold_A   threshold_B
Single threshold (0.5 for both)     0.50         0.50
Demographic parity                  0.56         0.41
Equal opportunity                   0.53         0.44
Equal accuracy                      0.51         0.49
```

Written finding: "Equal opportunity requires lowering B's threshold
from 0.50 → 0.44 and raising A's from 0.50 → 0.53. Demographic parity
pushes further in the same direction. These are the explicit prices of
each fairness framing; council must pick one (or deliberately pick the
single-threshold option and accept the equal-opportunity gap)."

### 2.3 Decision rationale

- **Counterfactual probing before slice statistics.** A slice view tells
  you *that* groups differ; counterfactual probing tells you *where* the
  model is routing the signal. Running counterfactuals first keeps the
  analyst from blaming "the data" when it is a feature-engineering
  issue.
- **Partial-dependence sweep is the bridge to feature-level remediation.**
  It is the artefact the model team can act on: "retrain without
  `prior_employer_tier`, or re-bucket it monotonically."
- **The fairness-criterion dropdown is the council-facing artefact.**
  Translating a fairness framing into a per-group threshold change is
  what makes the trade-off legible to non-ML stakeholders.
- **No conclusion is drawn from a single operating threshold.** The
  write-up names threshold 0.5 as a modelling default and uses the
  dropdown to expose sensitivity.

### 2.4 Limits of the What-If Tool

The learner must state these in the write-up:

- **WIT is association-only.** A nearest counterfactual by L1/L2
  distance is not a causal counterfactual. "The model flips when A
  flips" is a signal to investigate, not a proof of discriminatory
  intent or effect.
- **It operates on the deployed model + the loaded dataset.** It will
  not surface harms from populations that are absent from the dataset
  (coverage bias) or from upstream filtering stages (pre-selection
  bias).
- **Threshold-optimisation views assume the model stays fixed.** In
  practice a council may decide to retrain, which changes everything.
- **WIT nearest-counterfactual uses feature distance, not a causal
  graph.** The "nearest" candidate may differ on features that are
  causally downstream of A; interpret accordingly.
- **WIT shows counts, not confidence intervals.** For small slices,
  shelf judgement until you have a bootstrap CI.

## 3. Validation steps

1. Confirm each per-group number quoted in the write-up matches the
   WIT screenshot / exported JSON for the same slice and threshold.
2. Confirm the fairness-criterion thresholds in §2.2 probe 5 are the
   ones WIT actually produced (do not hand-edit; export them).
3. For every "A flips to B" counterfactual used in Probe 2, verify the
   two rows are genuinely similar — WIT occasionally picks a
   counterfactual that differs on a load-bearing feature; those cases
   should be excluded from the flip-rate count or annotated.
4. State the model version, data snapshot date, and WIT version in the
   write-up header.
5. Peer check: hand the write-up to a non-ML reviewer. Can they state
   the harm, the fairness lens chosen, and the threshold cost from the
   document alone? If not, the write-up is incomplete.

## 4. Rubric or review checklist

Grade the exploration write-up out of 15 (3 points each for the five
probes).

| Probe | 3 — strong | 2 — acceptable | 0–1 — weak |
|---|---|---|---|
| **1 Sanity ping** | 5 rows reviewed; at least one unexpected pattern surfaced and taken into Probe 3. | 5 rows reviewed; no unexpected patterns discussed. | Fewer than 5 rows, or rows reported without features. |
| **2 Counterfactuals** | Flip rate reported with denominator; load-bearing proxy features named. | Flip rate reported but no proxy analysis. | No counterfactual probing, or "fairness through unawareness" prescription. |
| **3 Partial dependence** | At least one non-obvious feature-shape finding; named as the remediation target. | Sweeps performed but no findings. | No sweeps. |
| **4 Slice view** | Per-group counts, selection rate, TPR, FPR reported with the harm theory that selects the lens. | Numbers reported without a harm theory. | Numbers reported from aggregate only. |
| **5 Fairness-criterion thresholds** | Per-group threshold table under ≥ 3 criteria exported from WIT; trade-off explicitly named. | Table under 1 criterion only. | No threshold-cost analysis. |

Deck-level checks (binary, all must pass):

- [ ] Model version, data snapshot date, and WIT version recorded.
- [ ] At least one limit of WIT (association-only, coverage bias, etc.)
      acknowledged in writing.
- [ ] No "fair because we dropped A" conclusion.
- [ ] The threshold-cost table is presented as a decision that belongs
      to the council, not a determination made by the analyst alone.

## 5. Common mistakes

- **Treating WIT counterfactuals as causal.** Nearest-neighbour in
  feature space is not a causal intervention.
- **Concluding "fair" from a single threshold.** Threshold choice can
  flip the conclusion; use the fairness-criterion dropdown.
- **Blaming the sensitive attribute.** WIT frequently reveals that the
  sensitive attribute enters through proxies, not directly.
- **Reporting only `by_group` selection rates.** Error-rate disparities
  are often more harm-relevant than selection-rate disparities.
- **Skipping Probe 1.** Sanity-pinging individual rows is the cheapest
  way to catch upstream data-pipeline problems that no slice statistic
  will reveal.
- **Letting the analyst pick the fairness criterion silently.** The
  per-group threshold table should be a council artefact.
- **Ignoring coverage bias.** WIT cannot see populations that never
  entered the dataset.

## 6. References

- Wexler et al., *The What-If Tool: Interactive Probing of Machine
  Learning Models*, IEEE Transactions on Visualization and Computer
  Graphics, 2020 (open access via Google Research). Project home and
  documentation: <https://pair-code.github.io/what-if-tool/>
- NIST, *AI Risk Management Framework (AI RMF 1.0)*, January 2023 —
  Measure 2.11 (fairness) and Measure 3 (ongoing measurement) as the
  functions this exercise supports. <https://www.nist.gov/itl/ai-risk-management-framework>
- NIST, *Special Publication 1270: Towards a Standard for Identifying
  and Managing Bias in Artificial Intelligence*, March 2022 — for the
  three-category bias taxonomy used in the decision rationale.
  <https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.1270.pdf>
- U.S. EEOC, *Uniform Guidelines on Employee Selection Procedures*,
  29 C.F.R. §1607.4(D) — four-fifths rule-of-thumb context.
  <https://www.ecfr.gov/current/title-29/subtitle-B/chapter-XIV/part-1607>
- OWASP Machine Learning Security Top 10 — ML08 Model Skewing as a
  mechanism by which disparities visible in WIT can arise. <https://owasp.org/www-project-machine-learning-security-top-10/>
