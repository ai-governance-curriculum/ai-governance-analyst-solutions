# Exercise 06 — Datasheet for a Dataset — Reference Solution

## 1. Solution overview

A **datasheet for a dataset** (Gebru, Morgenstern, Vecchione,
Vaughan, Wallach, Daumé III, Crawford, *Datasheets for Datasets*,
Communications of the ACM, Vol. 64, No. 12, Dec. 2021) is the dataset
analogue of a model card. It is a short, structured document that
answers a fixed set of questions about **how a dataset came to be**,
**what is in it**, and **what it should and should not be used for**.
The paper organises the questions into seven sections:

1. Motivation
2. Composition
3. Collection Process
4. Preprocessing / Cleaning / Labeling
5. Uses
6. Distribution
7. Maintenance

The datasheet travels with the dataset. A downstream model trained on
the dataset must be able to point at the datasheet for its training-
data documentation, and governance reviewers depend on it when they
assess fairness, legality, and misuse risk of any model that uses the
dataset.

This solution delivers:

- A worked datasheet for a reference training dataset
  (`ResumeMatch-Train-v1`, the dataset used by the ResumeMatch model
  in `mod-105/exercise-05`).
- Decision rationale explaining what to include, what to redact, and
  what to defer to the DPIA / AIIA.
- A rubric for grading a learner's own datasheet.

## 2. Worked answer or implementation

### 2.1 Reference dataset (local exercise context)

> **Dataset name**: `ResumeMatch-Train-v1`.
> **Owner**: Talent Platform team, with joint stewardship by the DPO
> and the AI Governance Office. Reference scenario for the drill;
> not a real dataset.
> **Shape**: ~450k (resume, job-post, outcome) triples drawn from
> internal recruiting records 2021-01 – 2024-12.
> **Used by**: ResumeMatch v1.3 (see `mod-105/exercise-05`).

### 2.2 Datasheet

```markdown
# Datasheet — ResumeMatch-Train-v1

## 1. Motivation
- **For what purpose was the dataset created?** To train and evaluate
  ResumeMatch, an internal resume-to-job-post relevance ranker, by
  pairing historical resumes with job posts and the recorded outcome
  ("hired or advanced within 90 days" vs. "did not advance"). Created
  specifically for internal supervised training; not created to study
  hiring patterns at large.
- **Who created the dataset?** Talent Platform team; data stewarded
  jointly with the DPO.
- **Who funded the creation?** Internal engineering budget.
- **Any other comments?** The dataset is strictly internal; see §6.

## 2. Composition
- **What do the instances represent?** A single instance is a tuple
  (resume text, job-post text, outcome label, metadata). Each resume
  and each job-post may appear in multiple instances.
- **How many instances?** ~450,000 instances; ~290,000 unique resumes;
  ~15,000 unique job posts.
- **Recommended splits?** Time-ordered: train 2021-01 – 2024-06, held-
  out 2024-07 – 2024-12. Random splits would leak information because
  the same candidate can recur.
- **Features per instance?** Resume text, job-post text, outcome
  label, job family, requisition country, hiring-channel code,
  year-quarter of submission.
- **Target label?** Binary "hired or advanced within 90 days".
- **Is any information missing?** Some 2021 outcomes were back-
  populated from the ATS and are noisier than 2023–2024 outcomes.
- **Relationships between instances?** Yes: candidates appear in
  multiple instances; same job post appears across candidates.
  Splits respect this.
- **Any recommended cells to exclude?** Yes — skilled-trades roles
  and roles with < 10 applicants; tagged `exclude_from_core_eval`.
- **Errors, redundancies, noise?** Some resumes are near-duplicate
  across candidates (template resumes); flagged via a hash feature.
  Some outcome labels are noisy because declines reflect recruiter
  availability as well as candidate fit.
- **Data from third-party sources?** No — all first-party, internal
  to the ATS.
- **Does the dataset contain data that might be considered
  confidential?** Yes — personal data of applicants, including some
  special-category data where applicants disclosed it (e.g., accommodation
  requests, veteran status). Access controls per §6.
- **Does the dataset contain data that, if viewed directly, might be
  offensive, insulting, threatening, or might otherwise cause anxiety?**
  Resumes include free text that occasionally contains distressing
  content (e.g., gaps attributed to bereavement or illness). Dataset
  users are warned in the dataset README.
- **Does the dataset identify any sub-populations?** The dataset does
  not store protected attributes directly, but attributes such as
  inferred gender and inferred ethnicity are computed via BIFSG /
  first-name proxies for the *evaluation* split only, and stored
  separately from the training split. See §4.
- **Is it possible to identify individuals, either directly or
  indirectly, from the dataset?** Yes — resumes contain names,
  contact information, and employer histories. See §4 for the
  redaction state of each copy.
- **Does the dataset contain data that might be considered sensitive?**
  Yes — see above and §6.

## 3. Collection Process
- **How was data about each instance acquired?** Directly from the
  internal ATS: resumes uploaded by candidates during application, job
  posts drafted by hiring managers, and outcomes recorded by
  recruiters at each stage.
- **What mechanisms / procedures were used to collect the data?** ATS
  export via a governed data pipeline with audit logging; no scraping.
- **If sampling was used, describe the strategy.** Universe for 2021-
  2024 was taken, then sampled proportionally by job family to the
  live job-family mix.
- **Who was involved in collection and how were they compensated?**
  Data-engineering team under a standard employment contract; no
  crowd workers.
- **Over what timeframe was the data collected?** 2021-01 – 2024-12.
  Older records are excluded because the ATS schema prior to 2021
  differs.
- **Were any ethical review processes conducted?** Yes — internal
  Responsible Use review and DPIA (DPIA-0902) completed prior to
  pipeline construction.
- **Did you collect the data from individuals directly or obtain it
  via third parties?** Directly — applicants submitted resumes to the
  ATS as part of applying.
- **Were the individuals notified about the data collection?** Yes —
  the ATS application flow discloses that resume content is processed
  for recruiting purposes and may be used to improve recruiting
  technology (notice version `recruit-notice-2024-Q3`).
- **Did the individuals consent to the use for ML training?** The
  collection relies on the legitimate-interest basis under GDPR for
  applicants in-scope jurisdictions, with opt-out; a consent basis
  applies in jurisdictions where consent is the applicable basis.
  See DPIA-0902 for the per-jurisdiction analysis.
- **Was there a mechanism to revoke consent?** Yes — candidate
  deletion requests are honoured within statutory windows and are
  propagated to the dataset within the next regenerate cycle.

## 4. Preprocessing / Cleaning / Labeling
- **Was any preprocessing / cleaning / labeling done?** Yes:
  - Deduplication by candidate hash within a job post.
  - PII redaction of training copies: names and direct contact
    details replaced with placeholder tokens prior to training; the
    evaluation copy retains names for the demographic-proxy
    computation and is stored in a separate access tier.
  - English-only filter.
  - Low-signal rows (< 30 resume tokens; < 20 job-post tokens)
    dropped.
- **Was the raw data saved?** Yes — the ATS source of truth is
  retained independently per the retention policy; the dataset-
  construction scripts are versioned.
- **Is the software used to preprocess the data available?** Yes —
  internal repository; versioned with the dataset.
- **Who labeled the data?** Labels come directly from the ATS; no
  additional human labeling step.
- **Are labels noisy?** Yes — outcome labels conflate several reasons
  for non-advance (recruiter unavailability, candidate withdrawal,
  requisition cancellation). Flagged in §2.

## 5. Uses
- **Has the dataset been used already?** Yes — to train and evaluate
  ResumeMatch v1.0 through v1.3.
- **What tasks could the dataset be used for?** Training supervised
  rankers; training embedding models; fairness-impact evaluation of
  such rankers.
- **Is there a repository that links to any or all papers or systems
  that use the dataset?** Yes — model registry record `rm-v1.3` points
  back to this dataset via its SHA.
- **What (other) tasks could the dataset be used for?** The dataset
  is deliberately scoped and should not be used for research into
  population-level hiring trends; the sampling makes it unsuitable
  for that.
- **Is there anything about the composition of the dataset or the way
  it was collected that might impact future uses?**
  - Historical hiring decisions are encoded in the outcome label —
    any model trained on it inherits that bias.
  - Skilled-trades roles are under-represented.
  - English-only; do not use for monolingual benchmarking in other
    languages.
  - GPT-polished resumes over-represented in 2024 data.
- **Are there tasks for which the dataset should not be used?**
  - Auto-reject tooling.
  - Any use outside recruiting (e.g., performance evaluation).
  - Any use that routes the dataset outside the internal data
    boundary (see §6).
  - Any use that attempts to re-identify applicants against external
    records.

## 6. Distribution
- **Will the dataset be distributed to third parties?** No. Internal
  only.
- **How will the dataset be distributed?** It will not be. Access is
  restricted to a named engineering group via least-privilege ACLs;
  copies for training are sealed artefacts that are not exportable.
- **Will the dataset be distributed under a licence?** Internal use
  only; no external licence.
- **Have any third parties imposed IP-based or other restrictions?**
  No — all data is first-party.
- **Do any export controls or other regulatory restrictions apply?**
  Data-protection regulation (GDPR and equivalent) restricts transfer
  outside the ATS environment and across jurisdictional boundaries;
  these are enforced by the data pipeline and audited quarterly.

## 7. Maintenance
- **Who is supporting / hosting / maintaining the dataset?** Talent
  Platform team with DPO oversight.
- **How can the owner be contacted?**
  `talent-platform-data@<org>.internal`.
- **Is there an erratum?** Yes — the dataset README logs all known
  errata and the resolution path.
- **Will the dataset be updated?** Yes — regenerated monthly to
  incorporate new outcomes and to honour deletion requests.
- **If the dataset relates to people, are there applicable limits on
  retention?** Yes — per the retention policy and jurisdiction, with
  periodic review by the DPO; deletion requests propagate to the next
  regenerate cycle.
- **Will older versions of the dataset continue to be supported /
  hosted / maintained?** Older versions are archived for
  reproducibility of prior model versions, with the same access
  controls; archived versions are not independently updated for
  deletion requests, so model refreshes against archived versions are
  blocked after a defined window.
- **If others want to extend / augment / build on / contribute to the
  dataset, is there a mechanism for them to do so?** Internal-only;
  changes land via pull request against the dataset-construction
  scripts.
```

### 2.3 Decision rationale

- **The datasheet is a questionnaire, not an essay.** The seven
  sections map to specific questions in Gebru et al. (2021). A
  datasheet that paraphrases away from those questions loses the
  comparability that makes datasheets useful.
- **Composition is the longest section on purpose.** It is where
  reviewers find the gaps that drive downstream fairness and legal
  risk.
- **Sensitive-content questions are not optional.** Resumes can
  contain distressing content; dataset users deserve a heads-up.
- **Protected-attribute proxies are called out as proxies.** The
  evaluation split stores BIFSG-inferred gender/ethnicity for
  impact-audit purposes; it is deliberately kept out of training
  features and held in a separate access tier.
- **Consent / legal basis belongs in §3, but the DPIA is
  authoritative.** The datasheet cites DPIA-0902; it does not
  re-adjudicate the legal basis.
- **"Should not be used for" is a required answer.** Datasets without
  an explicit misuse list invite the misuse they were meant to
  prevent.
- **Maintenance names deletion-request propagation.** Datasheets that
  do not describe how deletion requests flow back to the dataset are
  opaque about GDPR compliance.

## 3. Validation steps

1. Confirm all seven datasheet sections are present and answered
   question-by-question. Blanket "N/A" is only acceptable with a
   reason.
2. Confirm §2 composition includes known errors, noise, duplicates,
   and under-represented segments — not just the happy stats.
3. Confirm §3 names the collection timeframe, the notice version
   applicants received, and the legal basis (citing the DPIA).
4. Confirm §4 distinguishes training-copy preprocessing from
   evaluation-copy preprocessing where they diverge (e.g., PII
   redaction state).
5. Confirm §5 contains explicit **should-not-be-used-for** items; not
   only use cases.
6. Confirm §6 states whether the dataset will be distributed and
   under what controls.
7. Confirm §7 names an owner, an erratum log, and a deletion-request
   propagation mechanism.
8. Peer check: an ML engineer handed only the datasheet should be able
   to decide whether the dataset fits their proposed task and what
   caveats they must repeat in their model card.

## 4. Rubric or review checklist

### Section scoring (0–3 per section)

| Score | Criterion |
| --- | --- |
| **3 — strong** | Section answers every applicable question from Gebru et al. (2021); claims are specific to this dataset; sensitive content questions are answered honestly. |
| **2 — acceptable** | Section answers most questions; one or two are glossed. |
| **1 — weak** | Section present but skips multiple sub-questions; or §5 lacks a should-not-be-used list; or §7 lacks deletion-request mechanics. |
| **0 — fail** | Section missing; or dataset contains personal data and §3 does not cite a legal basis / DPIA; or §2 omits a known limitation. |

### Datasheet-level checks (binary, all must pass)

- [ ] All seven sections present.
- [ ] §2 composition includes errors, noise, duplicates, and
      under-represented segments.
- [ ] §2 and §3 state whether the dataset identifies individuals and
      how.
- [ ] §4 describes preprocessing and labeling; raw-data retention is
      stated.
- [ ] §5 contains at least three should-not-be-used items.
- [ ] §6 states distribution scope and access controls.
- [ ] §7 names an owner, contact, erratum log, deletion-request
      mechanism, and version strategy.
- [ ] DPIA / AIIA / model-card cross-references are present where the
      dataset trains a governed model.

## 5. Common mistakes

- **Writing it as a README.** Datasheets inherit specific questions.
  A README-style prose summary loses comparability with other
  datasheets.
- **No should-not-be-used list.** Section 5's most load-bearing
  question.
- **Silent protected-attribute proxies.** Teams compute BIFSG-inferred
  demographics for an audit, then do not disclose this in the
  datasheet, giving downstream users no way to know the proxy exists.
- **Composition = happy stats.** Cell counts, mean lengths, class
  balances — but no noise, dedup, or under-representation.
- **Legal basis absent.** The datasheet does not need to re-adjudicate
  the DPIA, but it must reference it.
- **No deletion-request mechanics.** Datasets that cannot describe
  how deletion requests propagate are opaque on retention compliance.
- **Datasheet drifts from the source of truth.** Datasets regenerate;
  the datasheet must version with them, not with a one-off date.
- **Confusing the datasheet with the AIIA.** The datasheet documents
  the dataset; the AIIA assesses the deployment that uses it.

## 6. References

- Timnit Gebru, Jamie Morgenstern, Briana Vecchione, Jennifer Wortman
  Vaughan, Hanna Wallach, Hal Daumé III, Kate Crawford. *Datasheets
  for Datasets*. Communications of the ACM, Vol. 64, No. 12, Dec. 2021.
  The seven-section structure and the question lists used in this
  solution follow this paper. <https://arxiv.org/abs/1803.09010>
- Margaret Mitchell et al. *Model Cards for Model Reporting*. FAT*
  2019. Used as the companion to this datasheet.
  <https://arxiv.org/abs/1810.03993>
- NIST, *AI Risk Management Framework (AI RMF 1.0)*, January 2023 —
  Map 4 ("Risks and benefits are mapped for all components, including
  third-party software and data") provides the AI-RMF framing for
  dataset-level documentation.
  <https://www.nist.gov/itl/ai-risk-management-framework>
- OWASP Machine Learning Security Top 10 — data-centric items
  (ML02 Data Poisoning, ML04 Membership Inference) that a datasheet
  should give enough context to assess.
  <https://owasp.org/www-project-machine-learning-security-top-10/>
- MITRE ATLAS — adversarial-ML tactics against data pipelines, used
  as the attack-path lens when assessing dataset provenance.
  <https://atlas.mitre.org/>
