# Exercise 04 — NYC LL 144 and EEOC Hiring Drill — Reference Solution

## 1. Solution overview

NYC Local Law 144 of 2021 regulates the use of **automated employment
decision tools (AEDTs)** by employers and employment agencies hiring
for positions in New York City. It took effect on 1 January 2023 and
began enforcement on 5 July 2023. The rule is codified in the New York
City Administrative Code title 20, chapter 5, subchapter 25 and in
the implementing rules of the NYC Department of Consumer and Worker
Protection (DCWP) in Title 6 of the Rules of the City of New York,
chapter 5, subchapter T.

At the US-federal level, the **EEOC Uniform Guidelines on Employee
Selection Procedures** (29 C.F.R. Part 1607) have governed adverse-impact
screening since 1978, and the EEOC issued a technical-assistance document
in May 2023 on applying Title VII to employer software, algorithms, and
AI (the "Technical Assistance on AI and Title VII").

This solution gives the analyst:

- A side-by-side of what each regime requires and what it does not.
- A worked read of an AEDT bias-audit summary and a decision memo
  recommending go / block / remediate.
- A rubric for grading a learner's drill answer.

The worked artefacts are illustrative only. Any specific employer,
vendor, or audit result named below is a stand-in.

## 2. Worked answer or implementation

### 2.1 Side-by-side: NYC LL 144 vs. EEOC Title VII / Uniform Guidelines

| Dimension | NYC Local Law 144 (AEDT) | EEOC Title VII + Uniform Guidelines |
|---|---|---|
| **Jurisdiction** | Employers and employment agencies using an AEDT to screen candidates for positions located within New York City. | All US employers with 15+ employees (Title VII). Guidelines apply when a procedure is used as a basis for an employment decision. |
| **Trigger** | Use of an AEDT — defined in §20-870 as a computational process derived from ML, statistical modelling, data analytics, or AI that issues a simplified output used to substantially assist or replace discretionary decision-making in employment. | Any selection procedure (test, algorithm, interview, etc.) used to make a hiring, promotion, or termination decision. |
| **Core duty** | (1) Independent **bias audit** within the preceding year; (2) **public summary** of the audit on the employer's website; (3) **candidate notice** at least 10 business days before AEDT use, with alternative-process option. | No prospective audit mandate. Employer is liable if the procedure causes disparate impact under 29 C.F.R. §1607 and cannot be justified by job-relatedness and business necessity (Griggs line). |
| **Required metrics in the audit** | Per DCWP rules: **selection rate** per category, **impact ratio** (selection rate of a category ÷ selection rate of the most-selected category) for each of the race/ethnicity categories (per EEO-1) and sex categories, and for intersectional sex × race/ethnicity categories. For scoring AEDTs, **scoring rate** = share of candidates scoring above the sample median, with impact ratio computed on that. | Four-fifths rule of thumb (§1607.4(D)): the selection rate of a protected group less than 80% of the highest group's rate is "generally regarded" as evidence of adverse impact triggering further inquiry. Not a legal safe harbor. |
| **Who performs the test** | Independent auditor — a person or firm not involved in using, developing, or distributing the AEDT and with no financial interest in the employer or vendor (DCWP rules §5-301). | The employer is responsible for validation under §§1607.5 and 1607.14 (criterion-related, content, or construct validity). |
| **What is published** | Date of audit, source and explanation of data used, number of applicants/candidates in each category, selection/scoring rates, and impact ratios, plus distribution date of the AEDT. | Nothing is published; records must be kept under §1607.4 and produced on enforcement request. |
| **Candidate rights** | Written notice at least 10 business days before AEDT use; disclosure of job qualifications and characteristics the AEDT uses; on request, information about the type of data collected, its source, and the retention policy. Candidates may request an alternative selection process (NYC LL 144 does not require the alternative to be granted). | No individual-level pre-use notice duty under Title VII itself; some state laws and the ADA separately impose notice/accommodation duties. |
| **Enforcement** | DCWP civil penalties per violation; each day of non-compliance and each unlawful AEDT use is a separate violation (NYC Admin. Code §20-872). | EEOC administrative charge → investigation → conciliation or litigation; private Title VII suits. |

Two anchors the analyst must say out loud:

- **A passed LL 144 audit is not a Title VII safe harbor.** LL 144
  imposes a disclosure regime on top of Title VII, not instead of it.
- **A failed four-fifths screen is not an automatic Title VII
  violation.** It triggers an inquiry; the employer may still justify
  the procedure under §1607.5.

### 2.2 Worked AEDT bias-audit read

Illustrative data. Employer "ExampleCo" uses a resume-ranking AEDT for
NYC-based software engineer roles. 2025 audit summary published under
LL 144 reports:

```
Selection rates (binary selection, threshold set by employer)
Category                                   n       selected   selection_rate
Hispanic or Latino (men)                   220          45         0.205
Hispanic or Latino (women)                  60          10         0.167
White (not Hispanic or Latino, men)       1,100        341         0.310
White (not Hispanic or Latino, women)       420        118         0.281
Black or African American (men)             180         39         0.217
Black or African American (women)           110         20         0.182
Asian (men)                                 880        246         0.280
Asian (women)                               310         83         0.268
...

Impact ratios (reference = highest rate category, White men at 0.310)
Hispanic (men)              0.205 / 0.310 = 0.661     ← below 0.80
Hispanic (women)            0.167 / 0.310 = 0.539     ← below 0.80
Black (men)                 0.217 / 0.310 = 0.700     ← below 0.80
Black (women)               0.182 / 0.310 = 0.587     ← below 0.80
Asian (men)                 0.280 / 0.310 = 0.903
Asian (women)               0.268 / 0.310 = 0.865
White (women)               0.281 / 0.310 = 0.906

Sex (collapsed)
Men                         0.286 / 0.286 = 1.000
Women                       0.251 / 0.286 = 0.878
```

Reading, section by section:

- **Duty posture.** The audit was performed by "Acme Audit Co."
  Confirm independently that Acme meets the DCWP §5-301 independence
  criteria — no financial interest in ExampleCo or the AEDT vendor and
  no involvement in using, developing, or distributing the AEDT.
  Auditor independence is a gating LL 144 requirement, but note that
  the §5-303 required public-summary fields (audit date, data source,
  per-category counts, selection/scoring rates, impact ratios, AEDT
  distribution date) do not themselves include an independence
  attestation; absence of such an attestation from the public summary
  is not by itself a §5-303 disclosure violation, so the independence
  check belongs in the audit file, not on the summary line.
- **Categories.** Selection and scoring rates are reported for EEO-1
  race/ethnicity × sex intersections as the DCWP rules require. If an
  intersectional cell is suppressed because n < the audit's reporting
  threshold, that suppression must be disclosed.
- **Impact ratios on the intersectional view.** Four intersectional
  cells are below 0.80 (Hispanic men, Hispanic women, Black men, Black
  women). The rule-of-thumb screen is triggered.
- **Sex view.** The collapsed sex view shows a 0.878 ratio — above the
  four-fifths threshold. **This is a classic LL 144 reading trap**: the
  collapsed view can clear the screen while the intersectional view
  fails. The DCWP rules require the intersectional view to be reported
  and read.
- **Mapping to EEOC Title VII.** Four protected groups show selection
  rates less than 80% of the top group's rate. This is evidence of
  adverse impact sufficient to trigger §1607.5 validation inquiry. The
  employer must be prepared to show (a) job-relatedness via a
  §1607.14 validation study, (b) that a less-discriminatory
  alternative does not exist. The May 2023 EEOC Technical Assistance
  on Title VII and AI expressly says the employer remains responsible
  for disparate-impact outcomes of a vendor-supplied algorithm.

### 2.3 Decision memo (recommend: remediate, do not block)

**To:** AI Governance Council — ExampleCo
**From:** AI Governance Analyst
**Re:** LL 144 audit 2025 for resume-ranking AEDT — recommended action

1. **Finding.** The 2025 LL 144 bias audit shows impact ratios below
   0.80 for four intersectional protected categories: Hispanic men
   (0.66), Hispanic women (0.54), Black men (0.70), Black women
   (0.59). The collapsed sex view (0.88) and the collapsed race view
   for Asian men and women (0.90 / 0.87) clear the rule-of-thumb
   screen. Under DCWP rules the intersectional view is the binding
   one.
2. **LL 144 compliance status.** We can publish the audit summary and
   remain *procedurally* compliant as long as: (a) independent-auditor
   attestation is on file, (b) the public summary includes all required
   fields (date, data source, candidate counts per category,
   selection/scoring rates, impact ratios), and (c) the 10-business-day
   candidate notice is live on the application flow. Procedural
   compliance with LL 144 does **not** resolve the Title VII exposure.
3. **Title VII exposure.** Four categories fail the four-fifths screen.
   Under the EEOC Uniform Guidelines §1607.4(D) this triggers an
   inquiry. The 2023 EEOC Technical Assistance confirms the employer,
   not the vendor, is responsible. We should treat this as a
   substantive adverse-impact signal and move to validation review.
4. **Recommendation — remediate before the next audit cycle.**
   - Request the vendor's §1607.14 validation study (criterion-related
     or content validity) and the feature-importance evidence that
     supports job-relatedness.
   - Run a What-If Tool / Fairlearn pass to identify load-bearing
     proxy features for race/ethnicity (see exercise-02, exercise-03).
   - Pilot a less-discriminatory alternative (e.g., structured human
     review for candidates at the current threshold boundary) and
     measure the impact ratio under the alternative.
   - Do not take the AEDT out of production immediately: an abrupt
     switch may itself produce adverse-impact signals on in-flight
     candidates and does not resolve the statutory issue.
5. **Candidate notice and alternative process.** Confirm the current
   notice names the job qualifications and characteristics the AEDT
   evaluates, discloses the data categories and sources, and offers
   the alternative-process request channel. These are LL 144
   requirements and are independent of the audit numbers.
6. **Reauthorisation.** The next bias audit must be completed and
   published within one year of the prior audit (NYC Admin. Code
   §20-871). Calendar the date and the independent-auditor
   engagement.

### 2.4 Decision rationale

- **Intersectional view is a required disclosure under LL 144.** The
  DCWP implementing rules explicitly require intersectional sex ×
  race/ethnicity impact ratios to be reported in the public summary.
  LL 144 itself does not impose a pass/fail threshold on those ratios
  — the DCWP FAQ is explicit that the law requires the audit, the
  summary, and the candidate notice, not any specific action based on
  the audit results — so an intersectional cell below 0.80 does not
  by itself mean the employer has failed to clear LL 144. It does,
  however, drive the substantive Title VII analysis, which is why the
  intersectional view, not the collapsed view, is the binding one for
  the council's go / remediate / block decision.
- **"Procedural compliance ≠ Title VII safety" is the headline for the
  council.** Many councils read a published LL 144 audit summary as
  closing the file; the memo names Title VII as the other, still-open
  file.
- **Remediate rather than block.** An abrupt shutdown creates its own
  disparate-impact risk and is not required by LL 144. §1607.3(B)
  anticipates remediation via a less-discriminatory alternative.
- **Validation study is the lever.** If the vendor cannot produce a
  §1607.14-compatible validation study, the AEDT should not continue.
  That is the Title VII leverage point, not the four-fifths ratio
  itself.
- **Independent-auditor attestation is a gating check.** Without it
  the audit is non-compliant per DCWP §5-301 regardless of the
  numbers.

## 3. Validation steps

1. Verify the audit summary includes all fields required by DCWP rules
   §5-301: date of audit; source and explanation of data; number of
   candidates in each category; selection and/or scoring rates; impact
   ratios on race/ethnicity, sex, and intersectional categories;
   distribution date of the AEDT. A missing field invalidates the
   public summary.
2. Verify the independent-auditor attestation is on file and that the
   auditor has no financial interest in the employer or the AEDT
   vendor (DCWP §5-301).
3. Confirm the candidate-notice content and 10-business-day lead time
   are implemented in the application flow (NYC Admin. Code §20-871).
4. If any intersectional category represents less than 2% of the
   audit data, confirm it was excluded from the impact-ratio
   calculation under the DCWP §5-301(d) small-category exception
   (this is a statutory exclusion, not generic "suppression under an
   unspecified reporting threshold") and that the exclusion — and the
   count of excluded categories — is disclosed in the public summary.
5. Map each failing cell to a candidate Title VII protected category
   and confirm the EEOC Technical Assistance language on
   algorithm-driven adverse impact applies.
6. If the model is a scoring AEDT (not a binary accept/reject),
   verify the audit used **scoring rate** (fraction above the sample
   median) as the DCWP rules require for scoring tools.

## 4. Rubric or review checklist

Grade the learner's drill answer on five dimensions, 3 points each
(total 15). Pass = total ≥ 10 with no 0.

| Criterion | 3 — strong | 2 — acceptable | 0–1 — weak |
|---|---|---|---|
| **Statutory accuracy** | LL 144 duties (bias audit, candidate notice, public summary, independent auditor) correctly named; EEOC four-fifths correctly described as a rule-of-thumb screen under §1607.4(D). | One duty slightly misstated. | Treats LL 144 as the only applicable regime, or four-fifths as a Title VII safe harbor. |
| **Metric interpretation** | Reads intersectional impact ratios as required; notes any collapsed-view / intersectional divergence; distinguishes selection rate vs. scoring rate. | Reads only the collapsed view. | Reports a single aggregate ratio only. |
| **Duty delineation** | Procedural LL 144 compliance and substantive Title VII exposure are called out as separate questions. | Mentions both but blurs the duties. | Treats a published audit as Title VII clearance. |
| **Remediation choice** | Validation study, less-discriminatory alternative, candidate-notice check, and reauthorisation date all named with owner. | Names remediation but leaves ownership vague. | Recommends immediate shutdown with no transition plan, or recommends no action. |
| **Independent-auditor gate** | Flags the auditor-independence attestation as a gating control. | Mentions independence but not as gating. | Omits the independent-auditor requirement entirely. |

Document-level checks (binary, all must pass):

- [ ] Memo is addressed to the council with a clear recommendation
      (go / remediate / block).
- [ ] Next-audit date is calendared.
- [ ] Vendor's role in validation evidence is named.
- [ ] No claim is made that LL 144 pre-empts or replaces Title VII.

## 5. Common mistakes

- **Treating LL 144 compliance as a Title VII safe harbor.** They are
  separate duties.
- **Reading only the collapsed race or sex view.** DCWP rules require
  the intersectional view, and the intersectional view is where
  harms typically appear first.
- **Confusing selection rate with scoring rate.** For scoring AEDTs,
  DCWP requires the scoring rate (above the sample median) — selection
  rate on a binary cut is not sufficient.
- **Treating four-fifths as a legal pass/fail.** The EEOC Uniform
  Guidelines describe it as a general rule of thumb that triggers
  inquiry (§1607.4(D)); employers can still defend a procedure under
  §§1607.5 and 1607.14.
- **Recommending immediate shutdown with no transition.** A sudden
  change-over mid-cycle can itself create disparate impact on
  in-flight candidates.
- **Blaming the vendor and stopping.** Under the May 2023 EEOC
  Technical Assistance on Title VII and AI, the employer remains
  responsible.
- **Letting the independent-auditor attestation slide.** A technically
  correct bias audit without a compliant independent attestation is
  non-compliant under DCWP §5-301.
- **Forgetting the one-year audit cadence.** The next audit must be
  within one year; calendaring it is part of the remediation.

## 6. References

- City of New York, *Local Law 144 of 2021* — Automated Employment
  Decision Tools. Codified at NYC Admin. Code §§20-870 to 20-874.
  <https://legistar.council.nyc.gov/LegislationDetail.aspx?ID=4344524&GUID=B051915D-A9AC-451E-81F8-6596032FA3F9>
- NYC Department of Consumer and Worker Protection, *Final Rules on
  Automated Employment Decision Tools*, effective 5 July 2023 (Title 6
  RCNY, chapter 5, subchapter T). <https://rules.cityofnewyork.us/rule/automated-employment-decision-tools-updated/>
- U.S. EEOC, *Uniform Guidelines on Employee Selection Procedures*,
  29 C.F.R. Part 1607, in particular §1607.4(D) (four-fifths rule of
  thumb), §1607.5 (validation), §1607.14 (technical standards for
  validity studies). <https://www.ecfr.gov/current/title-29/subtitle-B/chapter-XIV/part-1607>
- U.S. EEOC, *Select Issues: Assessing Adverse Impact in Software,
  Algorithms, and Artificial Intelligence Used in Employment Selection
  Procedures Under Title VII of the Civil Rights Act of 1964*,
  technical assistance document, May 2023.
  <https://www.eeoc.gov/select-issues-assessing-adverse-impact-software-algorithms-and-artificial-intelligence-used>
- NIST, *AI Risk Management Framework (AI RMF 1.0)*, January 2023 —
  Govern 1 (policies and processes) and Measure 2 (trustworthy
  characteristics including fairness) as the frame the LL 144 /
  Title VII duties plug into. <https://www.nist.gov/itl/ai-risk-management-framework>
