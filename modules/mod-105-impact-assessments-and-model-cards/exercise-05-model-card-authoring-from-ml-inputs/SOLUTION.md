# Exercise 05 — Model Card Authoring From ML Inputs — Reference Solution

## 1. Solution overview

A **model card** (Mitchell, Wu, Zaldivar, Barnes, Vasserman, Hutchinson,
Spitzer, Raji, Gebru, *Model Cards for Model Reporting*, FAT* 2019) is
a short, structured document that reports *what a model is, what it is
meant for, how it was evaluated, and where it is known to fail*. The
nine sections of a canonical model card are:

1. Model Details
2. Intended Use
3. Factors
4. Metrics
5. Evaluation Data
6. Training Data
7. Quantitative Analyses (unitary and intersectional)
8. Ethical Considerations
9. Caveats and Recommendations

In a governance programme, the model card is rarely written by the
model's own developers. The **ML team** hands the governance analyst a
training doc, an evaluation report, and a limitations memo; the
**governance analyst authors the model card**, applying the template,
and routes it for ML sign-off.

This solution delivers:

- A worked model card for a reference resume-screening model
  (`ResumeMatch`), authored from a supplied set of ML inputs (training
  doc, eval report, limitations memo).
- Decision rationale for what makes it into each section and what gets
  pushed to the AIIA / FRIA / DPIA.
- A rubric for grading a learner's own model card.

## 2. Worked answer or implementation

### 2.1 Supplied ML inputs (reference exercise context)

> **Training doc (abstracted)**: Model is a sentence-transformer-based
> encoder fine-tuned with contrastive loss on ~450k (resume, job-post)
> pairs. Positive pairs are "hired or advanced" outcomes; negatives are
> declines. Training frozen at commit `abc123`, 2025-11-14.
> **Eval report (abstracted)**: NDCG@10 on held-out job posts;
> precision at various thresholds; subgroup metrics on inferred gender
> (first-name proxy) and inferred ethnicity (BIFSG proxy on name and
> ZIP); limitations include noisy "advanced" labels, English-only
> training set, no coverage for roles in skilled trades.
> **Limitations memo (abstracted)**: Model sensitive to resume length;
> candidates who use GPT-polished resumes tend to receive higher
> similarity scores; unknown behaviour for applicants from career-gap
> populations.
> **Decision context (abstracted)**: Model output is a similarity
> score used to rank applicants for a human recruiter's long-list; it
> does not auto-reject. Used under NYC LL144, so a bias audit was
> performed per that rule.

The scenario is a reference training/eval setup built for the drill,
not a real system.

### 2.2 Model card

```markdown
# Model Card — ResumeMatch v1.3

## 1. Model Details
- **Model name / version**: ResumeMatch v1.3
- **Date**: 2025-11-14
- **Developed by**: Talent Platform team, <Organisation>; governance
  review by AI Governance Office.
- **Type**: Sentence-transformer encoder (dual-encoder) fine-tuned
  with contrastive loss.
- **Architecture summary**: Base transformer encoder; fine-tuned on
  (resume, job-post) pairs. Score is cosine similarity between
  encoded resume and encoded job-post.
- **Training commit**: `abc123` (model registry id `rm-v1.3`).
- **License / use terms**: Internal only; not for external licensing.
- **Owner contact**: `ai-governance@<org>.internal`.
- **Related documents**: AIIA AI-0902, FRIA N/A (deployer out of
  Article 27 scope), DPIA DPIA-0902, NYC LL144 bias-audit report
  BA-0902-2025.

## 2. Intended Use
- **Primary intended use**: Rank candidates for a given job post to
  produce a recruiter long-list.
- **Primary intended users**: Internal recruiters only.
- **Out-of-scope uses** (do not use):
  - As an auto-reject tool or an auto-advance tool.
  - For performance evaluation of employees.
  - For roles in skilled trades (training distribution does not cover
    these).
  - On resumes not in English (model is monolingual).
  - To compare candidates across distinct job posts; the score is
    calibrated for a single job post at a time.

## 3. Factors
- **Relevant factors**:
  - Candidate's inferred gender (BIFSG / name proxy).
  - Candidate's inferred ethnicity (BIFSG proxy).
  - Candidate's age band (inferred from graduation year).
  - Resume length (bucketed into short/medium/long).
  - Resume language (model is English-only; non-English resumes are
    flagged upstream and excluded).
  - Career-gap indicator (presence of a gap > 12 months).
- **Evaluation factors**: All of the above. Intersectional slices on
  (inferred gender × inferred ethnicity) and (inferred gender × career
  gap).
- **Factor caveats**: All protected-attribute factors are **inferred**
  from proxies; results are an estimate, not a measurement. They are
  reported for impact-audit purposes only, not used as model inputs.

## 4. Metrics
- **Headline metric**: NDCG@10 on a held-out set of job posts, using
  "hired or advanced within 90 days" as the relevance label.
- **Reporting thresholds**: NDCG@10 reported overall and per slice
  from §3.
- **Supporting metrics**: Precision@10, mean reciprocal rank,
  selection-rate ratio per subgroup pair (for the NYC LL144 bias
  audit).
- **Threshold notes**: ResumeMatch does not have a single decision
  threshold — the downstream long-list cut is set by the recruiter.
  Reported impact numbers are at the top-N=10 cut.
- **Statistical approach**: Bootstrap 95% CIs on all per-slice numbers;
  cell counts below a minimum are reported but flagged as unreliable.

## 5. Evaluation Data
- **Evaluation dataset**: 50k job posts drawn from 2024-Q4, with
  attached applicants and 90-day outcomes.
- **Motivation**: Covers the full 2024-Q4 recruitment season and all
  job families currently in scope.
- **Preprocessing**: Resumes stripped of HTML; names redacted *after*
  proxy-label extraction; English-only filter.
- **Known limitations**: Skilled-trades roles under-represented
  (~2% of posts); temporary-contract roles under-represented.
- **Gold labels**: "Hired or advanced within 90 days" is noisy; a
  non-advance can reflect a recruiter choice, candidate withdrawal,
  or an unfilled req.

## 6. Training Data
- **Training dataset**: ~450k (resume, job-post) pairs drawn from
  internal recruiting records, 2021–2024.
- **Positive label definition**: Candidate was hired or advanced past
  recruiter screen.
- **Preprocessing**: Deduplication by applicant hash; English-only;
  PII redaction after demographic-proxy extraction.
- **Known limitations**: Inherits any historical hiring bias in the
  "advanced" label; career-gap applicants under-represented; skilled
  trades under-represented; GPT-polished resumes over-represented in
  recent quarters.
- **Access**: Training data not externally shared.

## 7. Quantitative Analyses
- **Unitary results**:
  - Overall NDCG@10 and precision@10 with CIs.
  - NDCG@10 per factor slice from §3, with CIs.
  - Selection-rate ratios per subgroup pair (for LL144).
- **Intersectional results**:
  - (inferred gender × inferred ethnicity) NDCG@10 and selection-rate
    ratios.
  - (career gap × inferred gender) NDCG@10 and selection-rate ratios.
- **Observed disparities**:
  - Candidates with career gaps > 12 months receive materially lower
    scores at the same seniority band — this is consistent with the
    training signal and is flagged as a model bias, not a data
    anomaly.
  - Short resumes score lower on average; some subgroups write
    shorter resumes on average, producing an indirect path from
    resume length to subgroup disparity.

## 8. Ethical Considerations
- **Allocation-of-opportunity harm.** A false-negative at the long-
  list cut suppresses a candidate; recruiters may not re-examine
  suppressed candidates. The worked AIIA addresses this.
- **Proxy-feature leakage.** Resume length, ZIP-code embeddings, and
  school names can act as proxies for protected attributes. The team
  removes the explicit proxies it can identify; residual leakage is
  measured via the subgroup metrics in §7.
- **Automation bias.** Recruiters may over-trust the ranking.
  Mitigations: training, UI showing "model-ranked, not model-picked"
  framing, and mandatory sampling of lower-ranked candidates.
- **Reasonably foreseeable misuse.** Use for auto-reject, use outside
  recruiting (performance reviews), use on languages other than
  English.
- **Governance ties.** NYC LL144 bias audit BA-0902-2025; AIIA
  AI-0902; DPIA DPIA-0902 covers inferred-proxy processing with the
  DPO's sign-off.

## 9. Caveats and Recommendations
- **Caveat.** All demographic breakdowns are proxy-based and have
  measurement error; do not report them as measurements of real
  disparity without a corresponding CI and caveat.
- **Caveat.** Model is sensitive to resume length and style; results
  are not stable across resume-format changes.
- **Caveat.** Model has not been evaluated on resumes in languages
  other than English.
- **Recommendation.** Any job-family added to scope must have a
  slice-specific evaluation run before ResumeMatch is deployed on it.
- **Recommendation.** Refresh the bias audit on every retrain or every
  12 months, whichever is sooner.
- **Recommendation.** Any operator-visible threshold change requires a
  re-audit; log and alert on threshold edits.
```

### 2.3 Decision rationale

- **Intended / out-of-scope uses are the single most load-bearing
  section.** A model card without explicit out-of-scope uses invites
  the misuse it was meant to prevent. The out-of-scope list is
  specific, not generic.
- **Factors are split from Metrics for a reason.** Factors name the
  axes you cut by; Metrics name the numbers you cut. Combining them
  hides axes that the team collected but never evaluated on.
- **Protected-attribute inference is called out as inference.** The
  BIFSG / first-name proxy is widely used (including under NYC LL144's
  guidance), but it has measurement error. Reporting proxy-derived
  disparities without the caveat is a factual overclaim.
- **Training-data limitations are owned.** Skilled-trades under-
  representation, English-only training, and historical hiring-bias
  inheritance are facts the ML team supplied; the governance analyst
  elevates them to the card.
- **Governance cross-references are listed, not duplicated.** AIIA,
  DPIA, and the LL144 bias audit each have a document ID. The model
  card does not re-run them.
- **Thresholds and refresh triggers appear in §9, not §4.** §4 is
  about how performance is measured; §9 is about how the model is
  operated. Mixing them produces model cards that are out of date the
  moment the recruiter changes a UI setting.

## 3. Validation steps

1. For each section 1–9, confirm the content is **grounded in the
   supplied ML inputs**. Any claim not traceable to the training doc,
   eval report, or limitations memo is either removed or flagged with
   an HTML comment marker that requests follow-up research from the ML
   team (e.g., `<!-- follow-up: confirm with ML -->`).
2. Confirm the out-of-scope uses in §2 include every use case the ML
   team explicitly flagged as unsafe plus any obvious extrapolation
   failure mode.
3. Cross-check §3 factors against §7 analyses: every factor must have
   at least one quantitative result, or the card must say why not.
4. Verify §4 metric definitions match the eval report (label
   definition, aggregation, confidence-interval method).
5. Confirm §8 lists at least one harm per harm category that applies
   (allocation, representation, quality-of-service, automation bias)
   or an explicit "not applicable, because …".
6. Verify §9 recommendations include a refresh trigger and a scope-
   change trigger.
7. ML sign-off: the ML team re-reads the card and confirms no section
   overstates or understates the model's behaviour.

## 4. Rubric or review checklist

### Section scoring (0–3 per section)

| Score | Criterion |
| --- | --- |
| **3 — strong** | Section complete; every claim is traceable to an ML input; language is specific to this model. |
| **2 — acceptable** | Section complete; one or two claims are vague or boilerplate. |
| **1 — weak** | Section present but key sub-item missing (e.g., no out-of-scope uses in §2; no intersectional analysis in §7). |
| **0 — fail** | Section missing; or model card substitutes marketing language for behaviour documentation; or demographic proxy results are presented as measurements. |

### Card-level checks (binary, all must pass)

- [ ] All nine sections present.
- [ ] §1 lists owner, version, training commit, and sign-off references.
- [ ] §2 lists at least three concrete out-of-scope uses.
- [ ] §3 factors cover protected attributes (or proxies for them) *and*
      technical factors (e.g., resume length).
- [ ] §4 metric definitions match the eval report exactly.
- [ ] §5 and §6 state known limitations of the data, not just its
      composition.
- [ ] §7 has at least one intersectional slice.
- [ ] §8 names the harm theory, not just the metric.
- [ ] §9 lists a refresh trigger and a scope-change trigger.
- [ ] Every governance artefact referenced (AIIA, DPIA, FRIA, LL144 /
      other bias audits) has a document ID.
- [ ] Model card is signed off by both ML and governance.

## 5. Common mistakes

- **No out-of-scope uses.** The most common defect. Without this,
  §2 reads as marketing.
- **Metrics without definitions.** Reporting "accuracy = 0.87" with
  no statement of what "correct" means allows the number to be
  interpreted in several incompatible ways.
- **Factors collected but not evaluated.** If the team records race
  proxies but reports no per-race metric, the card misleads by
  omission.
- **Proxy disparities reported as measurements.** BIFSG-inferred
  gender/ethnicity carries measurement error; results must be
  reported with CIs and the caveat.
- **Silent training-data caveats.** Known limitations in the training
  doc do not appear in §6 or §9.
- **Marketing voice.** "State of the art" belongs in a blog post, not
  a model card. §8 and §9 should be directly usable by someone trying
  to decide whether to turn the model on.
- **Mixing model-card content with AIIA / FRIA content.** The model
  card documents the model; the AIIA / FRIA assess the deployment.
  Duplicating creates drift.

## 6. References

- Margaret Mitchell, Simone Wu, Andrew Zaldivar, Parker Barnes, Lucy
  Vasserman, Ben Hutchinson, Elena Spitzer, Inioluwa Deborah Raji,
  Timnit Gebru. *Model Cards for Model Reporting*. Proceedings of the
  Conference on Fairness, Accountability, and Transparency (FAT*),
  2019. The nine model-card sections used in this solution follow
  this paper. <https://arxiv.org/abs/1810.03993>
- NIST, *AI Risk Management Framework (AI RMF 1.0)*, January 2023 —
  Measure 2 "Risks and benefits are examined and documented" is the
  AI-RMF analogue for the model-card discipline.
  <https://www.nist.gov/itl/ai-risk-management-framework>
- OWASP Machine Learning Security Top 10 — practitioner lens for the
  security-oriented content in §8 (model skewing, poisoning, membership
  inference). <https://owasp.org/www-project-machine-learning-security-top-10/>
- MITRE ATLAS — adversarial-ML tactics referenced where §8 names
  attack-path harms. <https://atlas.mitre.org/>
