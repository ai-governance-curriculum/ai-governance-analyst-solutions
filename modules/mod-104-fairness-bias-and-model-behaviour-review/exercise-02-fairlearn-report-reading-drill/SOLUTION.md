# Exercise 02 — Fairlearn Report Reading Drill — Reference Solution

## 1. Solution overview

Fairlearn is an open-source Python library maintained by the Fairlearn
project (Project Fairlearn, Linux Foundation AI & Data) that produces
group-wise disaggregated metrics and summary disparity numbers for a
trained classifier. A governance analyst rarely runs Fairlearn; they
read its output and must decide what it does and does not establish.

This solution provides:

- A reading protocol: the order to read a Fairlearn report in so that no
  key artefact is missed.
- A worked read of a representative Fairlearn `MetricFrame` plus
  `selection_rate` / `true_positive_rate` / `false_positive_rate` outputs
  produced by `fairlearn.metrics`.
- A rubric for grading a learner's written reading.

The worked example uses a *synthetic* illustrative binary classifier on a
loan-approval task with sensitive attribute A ∈ {group X, group Y}. No
real-world dataset or incident is claimed.

## 2. Worked answer or implementation

### 2.1 Reading protocol (do in this order)

1. **Metadata header.** What model, which version, which data slice,
   which sensitive feature(s), what time window? A Fairlearn report with
   no metadata is unusable — refuse it.
2. **Overall performance.** Accuracy, precision, recall on the aggregate.
   Needed to anchor every group comparison: a 5-point TPR gap matters
   differently at 0.95 vs. 0.55 aggregate accuracy.
3. **Per-group counts (`count` on `MetricFrame`).** Smallest-group sample
   size sets the ceiling on what the disparities can tell you. Below ~100
   positive examples in the smaller group, disparities are mostly noise.
4. **Selection rate per group.** This is the demographic-parity view.
5. **TPR and FPR per group.** This is the equal-opportunity / equalised-odds
   view. Note which gap is larger.
6. **Precision / PPV per group (if label is trustworthy).** This is the
   predictive-parity view.
7. **Summary disparities: `difference()` and `ratio()` on the MetricFrame.**
   Check both the absolute difference and the four-fifths-style ratio;
   they can disagree on which group is worse off.
8. **Intersectional view.** If `sensitive_features` was a DataFrame with
   two or more columns, read the `by_group` table for intersectional
   cells before accepting any one-dimensional summary.
9. **Threshold sensitivity.** If the model is a scorer, a report at a
   single operating threshold hides the full picture. Ask for a
   threshold sweep.
10. **Narrative.** Is there a written rationale for the metric choice? If
    not, the report is incomplete regardless of the numbers.

### 2.2 Worked read of an illustrative Fairlearn output

Synthetic numbers from a trained binary classifier on a loan-approval
task. Sensitive feature `group ∈ {X, Y}`. Threshold fixed at 0.5. Numbers
are illustrative only.

```
MetricFrame.by_group
                 count  selection_rate  true_positive_rate  false_positive_rate  precision
group
X                 8200           0.420                0.78                0.19       0.74
Y                 1800           0.265                0.61                0.11       0.72

MetricFrame.overall
count              10000
selection_rate     0.392
true_positive_rate 0.74
false_positive_rate 0.17
precision          0.74

Summary disparities
selection_rate_difference      = 0.155
selection_rate_ratio           = 0.265 / 0.420 = 0.631
true_positive_rate_difference  = 0.17
true_positive_rate_ratio       = 0.61 / 0.78 = 0.782
false_positive_rate_difference = 0.08
false_positive_rate_ratio      = 0.11 / 0.19 = 0.579
precision_difference           = 0.02
```

Reading, section by section:

- **Metadata.** The report covers 10,000 scored applicants, sensitive
  attribute `group`, threshold 0.5. There is no time window given — flag
  for the model team to add.
- **Counts.** 1,800 applicants in group Y and 8,200 in group X. With
  these numbers, disparities up to a few percentage points can be real;
  below that, bootstrap CIs are needed before acting.
- **Selection rate.** 26.5% vs. 42.0%. Selection-rate ratio is 0.63,
  which is below the EEOC four-fifths rule-of-thumb threshold (0.80).
  By itself this is **not** a legal finding — it is a screen that
  triggers substantive review under the EEOC Uniform Guidelines.
- **TPR.** 0.78 vs. 0.61. Group Y is 17 percentage points less likely to
  be approved when they would have paid the loan back. This is the
  equal-opportunity gap, and it is the *harm-causing* number in a
  credit-access allocation framing.
- **FPR.** 0.19 vs. 0.11. Group Y is also *less* likely to be wrongly
  approved. This is not inherently a harm in a credit-access framing
  (fewer wrongful approvals ≈ fewer unaffordable loans), but it is a
  harm in a punitive framing. State the framing.
- **Precision / PPV.** 0.74 vs. 0.72. Approximately equal — the score
  means the same thing for both groups among those approved. This looks
  like near-predictive-parity.
- **Impossibility corner.** The approximate precision parity with
  unequal TPR/FPR is exactly the pattern the Chouldechova / Kleinberg
  impossibility predicts when base rates differ. It is not a bug in the
  report; it is a mathematical constraint.
- **Intersectionality.** The report only slices by `group`. Ask for a
  cut by `group × age_band` or `group × product_type` before concluding.
- **Threshold sensitivity.** Threshold 0.5 is a modelling default, not a
  business decision. Ask for a sweep of 0.3 / 0.4 / 0.5 / 0.6 / 0.7 so
  the council sees whether disparities are a threshold artefact.
- **Written rationale.** None attached — mark the report incomplete and
  send back.

### 2.3 Minimal reproduction skeleton (for reviewers)

Any Fairlearn report an analyst reviews should be reproducible from
code of roughly this shape. Analysts do not need to write it, but they
should be able to recognise that the report was produced this way.

```python
from fairlearn.metrics import (
    MetricFrame,
    selection_rate,
    true_positive_rate,
    false_positive_rate,
)
from sklearn.metrics import precision_score

metrics = {
    "selection_rate": selection_rate,
    "true_positive_rate": true_positive_rate,
    "false_positive_rate": false_positive_rate,
    "precision": precision_score,
}

frame = MetricFrame(
    metrics=metrics,
    y_true=y_test,
    y_pred=y_pred,
    sensitive_features=sensitive_test,  # single Series or multi-col DF
)

print(frame.overall)
print(frame.by_group)
print(frame.difference(method="between_groups"))
print(frame.ratio(method="between_groups"))
```

### 2.4 Decision rationale

- **Counts before metrics.** Any downstream statement about group Y is
  bounded by n in group Y; reading counts first keeps the analyst from
  over-reading noisy ratios.
- **Both difference and ratio.** Fairlearn exposes both because they
  surface different failures. A 0.20 TPR gap at TPR 0.78/0.58 is severe;
  the same gap at 0.98/0.78 would look better on the ratio and worse on
  the difference. Report both.
- **Four-fifths is a screen.** The 0.80 line triggers inquiry under the
  EEOC Uniform Guidelines; the solution calls this out so the learner
  does not present a 0.63 ratio as "legally failing".
- **Framing drives which gap counts as the harm.** Loan approval is an
  allocation problem; FPR and TPR carry different meanings depending on
  whether you frame access or predatory lending as the primary harm.
- **The pattern of "precision parity with unequal error rates" is
  expected, not suspicious.** Learners frequently flag this as
  internally inconsistent; the solution attaches it to the known
  impossibility.

## 3. Validation steps

1. Re-check each numeric claim above by recomputing from the illustrative
   counts (e.g., selection-rate ratio 0.265 / 0.420 = 0.631).
2. For each metric reported, point to the Fairlearn user-guide page or
   API reference that defines it (`fairlearn.metrics.selection_rate`,
   `true_positive_rate`, `false_positive_rate`, `MetricFrame.difference`,
   `MetricFrame.ratio`).
3. For every disparity > 0, name the harm theory that makes it a harm.
   If no harm theory applies, say so — not every disparity is a harm.
4. Ask whether the smaller group's confidence interval overlaps the
   larger group's point estimate. If yes, disparities are plausibly
   noise and need more data or bootstrapping.
5. Confirm the Fairlearn version used — API surface area has changed
   (e.g., `MetricFrame` replaced `GroupMetricResult` in 0.6; `method=`
   defaults have moved). Mismatched versions change what numbers mean.

## 4. Rubric or review checklist

Grade the learner's written read of the report out of 15 (3 points each
for the five criteria below).

| Criterion | 3 — strong | 2 — acceptable | 0–1 — weak |
|---|---|---|---|
| **Metadata hygiene** | Flags missing data window, model version, or population before discussing numbers. | Mentions metadata but accepts a thin header. | Dives into numbers without checking what the report covers. |
| **Counts first** | States smallest-group n and its implication for what disparities mean. | Mentions counts late in the read. | Treats small-n ratios as reliable. |
| **Metric meaning** | Correctly distinguishes selection rate, TPR, FPR, precision; names which fairness lens each corresponds to. | Minor slip (e.g., calls TPR gap a "demographic parity" issue). | Confuses two of the four. |
| **Difference vs. ratio** | Reads both, notes when they disagree, states the four-fifths rule as a screen not a verdict. | Reads only one summary. | Treats the ratio threshold as a legal pass/fail. |
| **Framing and impossibility** | Names the harm theory the report is being read under; explains precision-parity-with-unequal-errors as the Chouldechova / Kleinberg impossibility. | Names a harm theory but not the impossibility. | Treats the report as if all metrics should pass simultaneously. |

Pass = total ≥ 10 with no 0 on any row. A write-up that scores 3 on
"framing and impossibility" but 0 on "counts first" is not a pass — the
analyst would approve a report built on 40 examples.

## 5. Common mistakes

- **Reading only the `overall` row.** Aggregate accuracy is what the ML
  team shows up with; reading only it is reading no fairness report at
  all.
- **Interpreting the four-fifths ratio as legally dispositive.** It is a
  screen, not a verdict.
- **Concluding "fair" from a single operating threshold.** Threshold
  changes can flip which group is worse off.
- **Ignoring intersectionality.** `by_group` on a single sensitive
  attribute hides harms that only show up at intersections.
- **Treating precision parity as proof of fairness.** Predictive parity
  can coexist with large error-rate gaps and often does when base rates
  differ.
- **Not asking which Fairlearn version produced the report.** API and
  default behaviours change between minor versions.
- **Reading disparities as causal.** Fairlearn reports association, not
  causation. A gap does not by itself identify which feature or process
  stage produced it.

## 6. References

- Fairlearn project documentation — `fairlearn.metrics.MetricFrame`,
  `selection_rate`, `true_positive_rate`, `false_positive_rate`,
  `MetricFrame.difference`, `MetricFrame.ratio`. <https://fairlearn.org/>
- NIST, *AI Risk Management Framework (AI RMF 1.0)*, January 2023 — the
  trustworthy-characteristic "Fair — with harmful bias managed" and the
  Measure function categories under which this report would live.
  <https://www.nist.gov/itl/ai-risk-management-framework>
- NIST, *Special Publication 1270: Towards a Standard for Identifying and
  Managing Bias in Artificial Intelligence*, March 2022.
  <https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.1270.pdf>
- U.S. EEOC, *Uniform Guidelines on Employee Selection Procedures*,
  29 C.F.R. §1607.4(D) — four-fifths rule context.
  <https://www.ecfr.gov/current/title-29/subtitle-B/chapter-XIV/part-1607>
- OWASP Machine Learning Security Top 10 — ML02 Data Poisoning and
  ML08 Model Skewing as risks that can produce the *appearance* or
  *reality* of disparate error rates. <https://owasp.org/www-project-machine-learning-security-top-10/>
