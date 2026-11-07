# Exercise 01 — Audience-Adapted Briefing Drill — Reference Solution

## 1. Solution overview

Analysts repeatedly brief the same AI-governance fact pattern to very
different audiences: an executive who wants the bottom-line decision,
engineers who want the technical detail, Legal who want the obligation
map, a Council that wants the risk/benefit trade-off, and the business
owner who wants to know what they have to change on Monday. The content
is the same; the frame, length, and vocabulary are not.

NIST AI RMF 1.0 makes the point explicitly in **GOVERN 5.1** —
communication must be *tailored* to the recipient's role in managing AI
risk — and in **GOVERN 5.2** — governance information must reach the
accountable decision makers in a form they can act on. Adapting the
same briefing across audiences is the drill.

This solution provides:

- A reference fact pattern (a bias finding on a hiring-screen model).
- Five audience-adapted briefings (Executive, Engineering, Legal,
  AI Council, Business Owner) with identical facts but different
  frames, lengths, and calls-to-action.
- A side-by-side table showing what changes and what must not change.
- Decision rationale and a grading rubric.

Scope: this is a design exercise. The wording below is a model, not a
template to be used verbatim — organisation names, roles, and systems
must be substituted for local context.

## 2. Worked answer or implementation

### 2.1 Reference fact pattern (local exercise context)

> **System**: `sys-0012-resume-ranker` (fictional) — a candidate-ranking
> model used to screen applicants for software-engineering roles.
> **Finding**: The latest quarterly fairness review (see `mod-104`) shows
> a selection-rate ratio of **0.74** for one protected subgroup against
> the highest-selected reference group. The four-fifths rule threshold
> commonly used in US employment contexts is **0.80**; the ratio is
> therefore below the conventional screening threshold.
> **Deployment**: In use across six regions including New York City
> (NYC Local Law 144 of 2021 applies to the NYC use).
> **Decision asked of each audience**: whether to pause NYC use pending
> remediation, or continue with compensating controls.

Core facts that must never shift between briefings:

- The selection-rate ratio is **0.74**, computed on the Q3 review set.
- The applicable external reference point is the four-fifths rule
  (0.80) commonly cited in US employment decisioning.
- NYC LL 144 applies to the NYC use; EU AI Act Annex III(4) applies to
  the EU use (classified high-risk once Chapter III obligations take
  effect in August 2026).
- The reviewer is the AI Governance Analyst; the accountable owner is
  the Head of Talent Operations.

Everything else — length, framing, vocabulary, call-to-action — adapts.

### 2.2 Audience 1 — Executive Committee (CEO, COO, CPO)

**Format**: one page, three paragraphs, one decision, one date.
**Vocabulary**: outcome, risk exposure, cost, timeline. No model terms.
**Length target**: under 250 words.

> **Subject**: Decision needed by Friday — pause NYC use of the
> resume-ranker pending remediation?
>
> **What happened.** Our quarterly fairness review of the
> resume-ranker (used to screen applicants for engineering roles)
> flagged a selection-rate ratio of 0.74 for one protected subgroup
> against the reference group. The widely-cited screening threshold
> for adverse-impact concern in US employment is 0.80, so we are
> below the threshold. The finding is from Q3 data; it is not a
> customer-reported complaint.
>
> **Why it matters.** NYC's Local Law 144 (in force since 2023)
> requires an annual independent bias audit for automated hiring
> tools used on NYC-located candidates; a public summary of that
> audit must be posted. A selection-rate ratio below 0.80 does not
> automatically make use unlawful, but it is the finding our auditor
> and the DCWP (the NYC enforcement body) will key off. The EU AI
> Act treats this system as high-risk in the EU from August 2026, so
> the same finding becomes a Chapter III obligation question next
> year.
>
> **What we are recommending.** Pause NYC use for 30 days while
> Engineering ships a mitigation (threshold tuning plus re-training
> on a rebalanced set), and fall back to the pre-AI shortlist
> workflow for NYC roles during the pause. Keep other regions in
> service with a tightened human-in-the-loop (no auto-advance; an
> Operator must sign off on every rejection). Total cost of the
> pause: an estimated 10 extra recruiter-hours per week for 4 weeks.
> We need an answer by **Friday 2026-11-13** to hold the retraining
> slot.
>
> **Owner**: Head of Talent Operations. **Analyst**: AI Governance.
> **Appendix**: see Council pre-read for technical detail.

### 2.3 Audience 2 — Engineering / ML team

**Format**: technical memo. Numbers, metric definitions, next-step
tasks. Include the data slices.
**Vocabulary**: selection-rate, FPR/FNR, confusion matrix, drift,
feature importance.
**Length target**: whatever it needs to be — but front-load the ask.

> **Subject**: Resume-ranker Q3 fairness finding — remediation plan
> request.
>
> **1. Finding.** Selection-rate ratio of 0.74 (subgroup A against
> reference group B), computed on the Q3 2026 scoring set (N ≈ 42k
> applicants, pre-dedup). Ratio defined as
> `P(score ≥ τ | A) / P(score ≥ τ | B)` at the deployed threshold
> `τ = 0.62`. The four-fifths rule threshold (0.80) is the industry
> screening heuristic; the current ratio is below it.
>
> **2. Secondary metrics.**
> - False-negative-rate parity: 0.71 (A/B). Qualified candidates in A
>   are being screened out at a higher rate than in B.
> - False-positive-rate parity: 1.09 (A/B). Close to parity.
> - Calibration-by-group: within ±2.1pp across score deciles — not
>   the primary failure mode.
>
> The pattern is consistent with a threshold effect compounded by
> feature-weight drift — `years_of_experience_bucket` and
> `prior_company_tier` moved up in importance between v4.2 and v4.6
> and both are known to be correlated with historical hiring biases.
>
> **3. Ask.** By **2026-11-20**: ship one of (a) threshold reset at
> `τ = 0.58` with rebalanced training set, or (b) a post-processing
> calibration layer applied group-wise. Decision between (a) and (b)
> is yours; (a) is simpler but trades precision for recall.
>
> **4. Scope of impact.** NYC region is paused (exec decision pending
> Friday 2026-11-13). Other regions stay live with
> human-in-the-loop — Recruiter signs off on every reject.
>
> **5. Validation.** Re-run the slice table (`mod-104` fairness
> harness) and the LL 144 audit template on the mitigated model
> before promotion. Target: ratio ≥ 0.85 and FNR parity ≥ 0.85.
> Hold-out: Q2 scoring set, not Q3 (don't train on the test).
>
> **6. Artefacts.** Finding row: `RISK-sys-0012-adverse-impact` in
> the risk register. Experiment tracking: ML-6621. Dashboards:
> `mod-109` monitoring board, "resume-ranker · fairness" panel.

### 2.4 Audience 3 — Legal and Compliance

**Format**: obligation-oriented memo. Cite the rule, state the
trigger, state the posture, state the action, flag the open
question for Legal to resolve.
**Vocabulary**: applicability, obligation, evidence, deadline.
**Length target**: whatever it needs to be. Full citations are the
point.

> **Subject**: Resume-ranker Q3 bias finding — obligation map and
> items for Legal review.
>
> **Instrument(s) engaged**:
>
> 1. **NYC Local Law 144 of 2021** (NYC Admin. Code §§20-870 to
>    20-874; 6 RCNY ch. 5, subch. T, implementing rules). The
>    system is an Automated Employment Decision Tool in use on NYC
>    candidates; the LL 144 bias-audit obligation
>    (`nyc-ll144-annual-bias-audit` in the tracker) applies. Our
>    most recent independent audit is dated 2026-10-15. The Q3
>    internal review is *not* that audit, but it will be the input
>    the auditor keys off next cycle.
> 2. **Regulation (EU) 2024/1689 (EU AI Act)**, Annex III(4) —
>    AI systems intended to be used for the recruitment or
>    selection of natural persons are high-risk. Chapter III
>    obligations (Arts. 8–15) apply to the EU use from 2026-08-02.
>    Art. 10 (data governance) and Art. 15 (accuracy, robustness,
>    cybersecurity) are the primary clauses that a 0.74 subgroup
>    selection-rate ratio speaks to.
> 3. **Regulation (EU) 2016/679 (GDPR)**, Art. 22 — any automated
>    decision with legal or similarly significant effect is in
>    scope. Our EU use keeps a human-in-the-loop on all rejections,
>    so our current posture is "Art. 22(1) does not apply because
>    the decision is not solely automated" — Legal should confirm
>    that this posture still holds post-pause-and-remediation.
>
> **Items we need Legal to resolve** (not analyst-decidable):
>
> - Does the Q3 internal review change the LL 144 public-summary
>   wording (posted under 6 RCNY §5-301) before next annual audit?
> - Does the 30-day pause of NYC use constitute an "unlawful AEDT
>   use" avoidance measure, or does it need to be disclosed to
>   candidates currently in-pipeline under 6 RCNY §5-303?
> - Under EU AI Act Art. 72 (post-market monitoring), do we have
>   any serious-incident reporting duty on this finding today?
>   (Preliminary read: no — Art. 72 serious-incident obligations
>   are tied to the Art. 3(49) definition, which is narrower than
>   "subgroup selection-rate ratio below 0.80". Need Legal
>   confirmation.)
>
> **Evidence package ready**: Q3 fairness-review report; LL 144
> audit (2026-10-15); risk-register row; mitigation plan.
>
> **Posture we are recommending**: pause NYC, keep other regions
> live with heightened human-in-the-loop, remediate by 2026-11-20,
> re-audit in Q4 under LL 144 template.

### 2.5 Audience 4 — AI Governance Council

**Format**: pre-read block (single page), appended to the Council
agenda. Structured as Situation, Impact, Options, Recommendation
(SIOR). The Council members include the executive sponsor, Legal, HR,
Risk, and (where policy requires) an independent/external member.
**Vocabulary**: risk, obligation, control, trade-off. Mix of exec
and legal vocabulary.
**Length target**: 400–600 words.

> **Pre-read item 3 — Resume-ranker Q3 fairness finding: pause NYC?**
>
> **Situation.** Quarterly fairness review of the resume-ranker
> produced a selection-rate ratio of 0.74 against the four-fifths
> conventional threshold of 0.80. Secondary metrics confirm an
> uplift in false-negative rate for the affected subgroup. The
> finding is internal; no complaint has been filed and no
> regulator has been notified. Our most recent LL 144 independent
> audit (2026-10-15) did not surface this because it was computed
> on an earlier scoring set.
>
> **Impact.** Three rings of impact:
>
> 1. **Candidates** — qualified applicants in the affected
>    subgroup are being screened out at a higher rate than their
>    peers on the current threshold.
> 2. **Business** — pausing NYC adds ~10 recruiter-hours/week for
>    four weeks across the NYC roles in flight. Other regions
>    continue to operate.
> 3. **Regulatory** — NYC LL 144 bias-audit obligation is already
>    being met on the 2026 cycle; the Q3 finding will feed the
>    next audit. EU AI Act Annex III obligations on the EU use
>    begin August 2026. Chapter III (Arts. 9, 10, 14, 15) will
>    require a documented remediation path on a finding like this.
>
> **Options.**
>
> - **A — Pause NYC, remediate, re-audit, resume (recommended).**
>   30-day pause of NYC use; Engineering ships either a threshold
>   reset or a group-wise calibration layer; LL 144 template
>   re-run before resume; other regions stay live with tightened
>   human-in-the-loop. Cost: ~40 recruiter-hours; one engineering
>   sprint. Residual risk after mitigation: moderate; monitored
>   monthly.
> - **B — Continue all regions, remediate in parallel.** Lowest
>   operational cost; higher residual risk; harder to defend to
>   an auditor if a complaint is filed during remediation. Not
>   recommended.
> - **C — Pause globally.** Lowest residual risk during
>   remediation; highest operational cost; probably
>   disproportionate given LL 144 is the only in-force binding
>   instrument today.
>
> **Recommendation.** Option A. Decision asked of the Council:
> approve the pause of NYC use for 30 days and the remediation
> plan dated 2026-11-20; delegate resume-of-service decision to
> the Head of Talent Operations on evidence that the ratio is
> ≥ 0.85 on the Q4 slice table.
>
> **Dissent room.** The Council member responsible for workforce
> matters noted that pause windows must be communicated to
> candidates in the NYC pipeline today; see HR's communication
> plan appended as item 3a. Legal to confirm whether 6 RCNY
> §5-303 requires a formal candidate-side notice.

### 2.6 Audience 5 — Business Owner (Head of Talent Operations)

**Format**: operational brief. What changes, who does what, by when,
what to watch. One page, action-oriented.
**Vocabulary**: pipeline, recruiter task, SLA.
**Length target**: under 300 words.

> **Subject**: What changes on Monday for NYC-region recruiting.
>
> **Starting 2026-11-16** (Monday), resume-ranker scoring is paused
> for all NYC-located roles. For every candidate currently in the
> NYC pipeline:
>
> 1. **Already scored by the model** — proceed as today; the score
>    does not retroactively change. Note in the ATS that the score
>    was produced on the pre-pause model.
> 2. **Not yet scored** — route to a recruiter for manual review
>    under the pre-AI shortlist process (ATS workflow
>    `NYC-no-ml-2024`). Expected throughput: ~10 extra
>    recruiter-hours/week for four weeks across the team.
> 3. **New applicants** — the ATS banner message updates
>    automatically (owned by HR Ops).
>
> **Other regions** stay live, but every rejection must be
> acknowledged by a recruiter before it goes out — no auto-send
> reject emails. This is the "tightened human-in-the-loop" posture
> the Council approved.
>
> **What to watch (daily)**:
> - Backlog of unscored NYC applicants (dashboard: `mod-109`
>   monitoring board, "resume-ranker · NYC pause" panel).
> - Recruiter acknowledgement rate across all regions.
> - Candidate-side complaints or inquiries about the pause
>   (escalate immediately to HR Business Partner and the AI
>   Governance Analyst).
>
> **Resume-of-service decision** is yours (delegated by the
> Council), on Engineering's evidence that the Q4 slice table
> shows ratio ≥ 0.85. Expected date: around 2026-12-15.
>
> **Point of contact**: AI Governance Analyst for questions on the
> finding; Engineering lead for questions on the mitigation.

### 2.7 Side-by-side comparison — what changed, what must not

| Element                    | Exec        | Engineering     | Legal           | Council      | Business Owner  |
| :------------------------- | :---------- | :-------------- | :-------------- | :----------- | :-------------- |
| Length target              | <250 w      | full            | full            | 400–600 w    | <300 w          |
| Leads with                 | Decision    | Metric          | Instrument      | Situation    | What changes    |
| Metric precision           | threshold   | full breakdown  | summary + cite  | summary      | none            |
| Full citations             | no          | no              | yes             | summary      | no              |
| Call-to-action             | yes/no      | pick a path     | confirm posture | approve plan | operate plan    |
| Vocabulary                 | outcome     | technical       | obligation      | risk/benefit | operational     |
| Owner named                | yes         | yes             | yes             | yes          | yes             |
| Numbers that must not drift | 0.74, 0.80 | 0.74, 0.80, τ=0.62 | 0.74, 0.80 | 0.74, 0.80 | (no numbers)    |

The core facts — the 0.74 ratio, the 0.80 reference threshold, the
instrument names, the system owner, the deadline — must appear
identically wherever they appear. The *frame* is what adapts.

### 2.8 Decision rationale

- **Facts first, then frame.** Write the core facts once; adapt
  length and emphasis per audience. Audiences notice when numbers
  drift between briefings and lose trust.
- **One decision per briefing to the decider.** The exec and the
  Council each get a single concrete ask with a date. Everything
  else is supporting material.
- **Legal gets citations; the exec does not.** The exec brief that
  cites `NYC Admin. Code §20-871(a)(1)` reads as cover-your-tail
  rather than as a decision. The Legal brief that omits the cite
  cannot be acted on.
- **Engineering gets the thresholds used to compute the number.**
  A ratio without `τ` or without the subgroup definition cannot be
  reproduced. NIST AI RMF 1.0 MEASURE 2.11 asks for exactly this.
- **Business owner gets the Monday morning instruction.** If the
  briefing does not change what the owner does, it has no effect.
- **Dissent surfaces on the Council pre-read, not the exec brief.**
  The Council is where minority views are recorded (see `mod-110`
  exercise 02 on minutes). The exec brief is a decision instrument.
- **No audience gets a watered-down version of the finding.** The
  number is the number. "A minor fairness signal" is a drift from
  the facts and should not appear in any briefing.
- **Each briefing names the next decision maker.** An audience that
  cannot act on the briefing should know who can.

## 3. Validation steps

1. **Fact-consistency check.** Diff every numeric or cited fact
   across all five briefings; they must match (0.74, 0.80, dates,
   instrument titles, owner role names).
2. **Call-to-action check.** Every briefing ends with either a
   decision (exec, Council), a task (engineering, business owner),
   or an open-question-to-the-expert (Legal). None end with
   "please consider."
3. **Length check.** Exec ≤ 250 words; business owner ≤ 300;
   Council 400–600; engineering/Legal as long as needed but
   front-loaded.
4. **Vocabulary check.** The exec brief contains no model-internal
   terms (no `τ`, no FNR, no "calibration"). The engineering brief
   contains no non-technical hedging ("some impact"). The Legal
   brief contains full citations the first time an instrument is
   named.
5. **Owner check.** Every briefing names a role (not a team, not a
   person-by-name) as owner. See `mod-103` exercise-05 on routing.
6. **Audience-pull-test.** Give each briefing to a reader who
   matches that audience and ask them one question: *what are you
   expected to do after reading this?* If any reader answers "not
   sure," the briefing failed.
7. **No-watered-down check.** Grep the five briefings for softening
   phrases ("minor", "mild", "a bit", "somewhat") where the
   underlying number is below the 0.80 reference threshold. Those
   phrases are a drift from the facts.

## 4. Rubric or review checklist

Grade the learner's submission on six dimensions, 3 points each
(total 18). Pass = total ≥ 13 with no 0.

| Criterion | 3 — strong | 2 — acceptable | 0–1 — weak |
|---|---|---|---|
| **Facts consistent across audiences** | Core numbers, dates, citations, and owner identical across all briefings. | One minor inconsistency. | Material drift between briefings (number or owner changes). |
| **Audience framing** | Each briefing leads with what that audience acts on (decision / metric / obligation / risk-benefit / operational task). | Most do; one is mis-framed. | Same text copied with a title change. |
| **Vocabulary fit** | Exec brief avoids model internals; Engineering brief shows the threshold and slice definitions; Legal brief cites instruments; Council brief uses risk/benefit; Business brief uses operational language. | Mostly fits; occasional slip. | Jargon in exec brief; vague language in engineering brief. |
| **Explicit call-to-action** | Every briefing has a specific ask, an owner, and a date. | 4/5 have a specific ask. | Briefings end with "please consider" or no ask. |
| **Length discipline** | Exec ≤ 250; Business ≤ 300; Council 400–600; others reasonable. | One section 10–20% over. | Exec brief exceeds 400 words; business owner brief reads like a legal memo. |
| **Dissent / open-question handling** | Legal brief lists open questions; Council brief captures dissent; neither ends up in the exec brief. | Dissent is noted but in the wrong briefing. | Dissent omitted or obscured. |

Document-level checks (binary, all must pass):

- [ ] No single person's name appears as an owner; only role names.
- [ ] Every external instrument is cited in full the first time it
      appears in the Legal brief.
- [ ] The four-fifths rule is referenced as a widely-used screening
      heuristic, not as a statute.
- [ ] No briefing promises a specific regulatory outcome
      ("the regulator will accept", "we will pass audit").
- [ ] The engineering brief gives enough detail to reproduce the
      ratio from the raw data.

## 5. Common mistakes

- **Writing one briefing and renaming it five times.** The content is
  the same because the facts are the same; the framing is what
  adapts. A single-document solution fails the drill.
- **Numbers drifting between briefings.** The exec brief says "about
  0.75", the engineering brief says "0.74", Legal says "under 0.8".
  All of these should say the same number.
- **Softening the finding for the exec.** Executives are the audience
  most likely to ask whether this was watered down; it is the fastest
  way to lose trust.
- **Citations in the exec brief.** A full `NYC Admin. Code
  §20-871(a)(1)` citation in the exec brief signals defensiveness,
  not analysis.
- **No citations in the Legal brief.** Legal needs the instrument
  name, article, and effective date to do their job.
- **No call-to-action.** A briefing that doesn't end with a specific
  ask is a status update, not a brief. NIST AI RMF 1.0 GOVERN 5.2
  asks that governance information reach accountable decision makers
  *in a form they can act on*.
- **Dissent shoved into the exec brief.** Dissent is surfaced to the
  Council for the record; the exec brief is for a decision.
- **Naming a team, not a role.** "Compliance will handle it" is not
  an audit trail. Name the role (e.g., "Head of Talent Operations").
- **Promising a regulator's reaction.** The analyst does not control
  what DCWP or a European market-surveillance authority will do.
  State the posture, name the evidence, stop there.
- **Omitting the business owner's brief.** The business owner is the
  audience that makes the plan real on Monday morning. The drill is
  incomplete without it.

## 6. References

- NIST, *AI Risk Management Framework (AI RMF 1.0)*, January 2023 —
  particularly `GOVERN 5.1` (tailored communication) and
  `GOVERN 5.2` (information reaches accountable decision makers);
  `MEASURE 2.11` (reproducible fairness measurements);
  `MANAGE 2.3` (communicate residual risk).
  <https://www.nist.gov/itl/ai-risk-management-framework>
- New York City Local Law 144 of 2021 — Automated Employment
  Decision Tools; NYC Admin. Code §§20-870 to 20-874.
  <https://legistar.council.nyc.gov/LegislationDetail.aspx?ID=4344524&GUID=B051915D-A9AC-451E-81F8-6596032FA3F9>
- NYC Department of Consumer and Worker Protection, *Final Rules on
  Automated Employment Decision Tools*, 6 RCNY ch. 5, subch. T.
  <https://rules.cityofnewyork.us/rule/automated-employment-decision-tools-updated/>
- Regulation (EU) 2024/1689 of 13 June 2024 (EU AI Act), Annex III
  §4 (employment), Arts. 9, 10, 14, 15, 72.
  <https://eur-lex.europa.eu/eli/reg/2024/1689/oj>
- Regulation (EU) 2016/679 (GDPR), Art. 22 — automated individual
  decision-making. <https://eur-lex.europa.eu/eli/reg/2016/679/oj>
- US EEOC and the "four-fifths rule" in the *Uniform Guidelines on
  Employee Selection Procedures*, 29 C.F.R. §1607.4(D). Cited here
  as the widely-used screening threshold, not as a safe-harbour.
  <https://www.ecfr.gov/current/title-29/subtitle-B/chapter-XIV/part-1607>
- OWASP Machine Learning Security Top 10 — referenced for the
  engineering brief's mention of model-threat context.
  <https://owasp.org/www-project-machine-learning-security-top-10/>
- MITRE ATLAS — referenced where an adversary-technique framing is
  needed in a security-adjacent briefing.
  <https://atlas.mitre.org/>
- Local exercise context: `mod-104` fairness review template;
  `mod-106` regulatory tracker; `mod-103` exercise-05 routing;
  `mod-109` monitoring dashboard.
