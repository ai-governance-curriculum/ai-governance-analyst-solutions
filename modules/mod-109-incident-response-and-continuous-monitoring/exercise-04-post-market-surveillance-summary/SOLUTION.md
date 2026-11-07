---
exercise_id: exercise-04-post-market-surveillance-summary
module: mod-109-incident-response-and-continuous-monitoring
artifact: SOLUTION.md
---

# Exercise 04 — Post-Market Surveillance Summary — Reference Solution

## 1. Solution overview

A post-market surveillance (PMS) summary is the periodic, written
artefact that aggregates everything the organisation observed about
a deployed AI system over a reporting window — monitoring dashboard
readings, incidents, follow-ups, control changes, user feedback,
measurement results, and any interaction with a regulator — and
turns it into a document the AI council, the provider's own
market-surveillance file, and (where applicable) a regulator can
read. It is the cumulative evidence that the organisation is
*operating* the system, not just deploying it.

For high-risk systems under Regulation (EU) 2024/1689 (the EU AI
Act), post-market monitoring is a provider obligation (Art. 72):
the provider must establish a post-market monitoring system that
"actively and systematically" collects data on performance
throughout the lifetime of the system, documents it in a
post-market monitoring plan, and uses the data to evaluate
continuous compliance with the Chapter III requirements. The
Commission is tasked with adopting a template for the plan by
implementing act. The PMS summary is how the provider shows that
plan is being executed.

Outside the EU AI Act, the same discipline is good practice:
NIST AI RMF 1.0 `MANAGE 4.1`–`MANAGE 4.3` and ISO/IEC 42001:2023
Clause 9 and Annex A A.6 all expect periodic review of operating
evidence.

A good summary is short, cites every claim, groups findings by
severity (incidents first, trends second, within-band last), and
closes with a next-period posture. Done badly, the summary is a
dashboard screenshot and a paragraph.

This solution provides:

- A reporting-window template (quarterly cadence) with every
  required section and the fields each section expects.
- A fully worked quarterly summary for `sys-0034-credit-score`
  covering Q3 2026, aggregating exercise-03's dashboard reading
  and exercise-02's incident record pattern.
- Decision rationale, validation steps, a grading rubric, and the
  common failure modes.

Scope: the PMS summary is a **governance** artefact aggregating
operational evidence. It is not a legal regulatory submission.
Legal owns the decision whether the summary is shared outside the
organisation and in what form. The summary is designed to be a
drop-in input to a market-surveillance submission or an auditor
review, not the submission itself.

## 2. Worked answer or implementation

### 2.1 Required sections

Every PMS summary has the following sections in this order.
Section headings should match verbatim across summaries so
multi-period reads diff cleanly.

```
1. System identification and scope
2. Reporting period and author
3. Operating posture (headline)
4. Monitoring evidence (what the dashboard and measurement
   activities showed)
5. Incidents (what was filed, status, regulatory interactions)
6. Follow-ups and control changes (what was opened and closed)
7. User feedback and complaints (appeals, contested decisions,
   agent/customer reports)
8. Regulator and external interactions (correspondence, requests,
   inspections — "none" is an allowed, logged value)
9. Changes to the post-market monitoring plan (if any, and why)
10. Cross-links (risk register, controls, obligations, prior
    summaries)
11. Next-period posture and planned checks
```

### 2.2 Summary schema (YAML)

```yaml
post_market_surveillance_summary:
  # --- 1. System identification ---
  system_id: string                    # FK to mod-102 inventory
  short_name: string
  intended_purpose: string             # as declared in the system record
  high_risk_classification:
    assessment: enum                   # "eu-ai-act-annex-iii" |
                                       # "eu-ai-act-annex-i-embedded" |
                                       # "eu-ai-act-gpai" | "not-high-risk" |
                                       # "legal-review-pending" | "n/a-non-eu-only"
    source_ref: string                 # link to Legal's classification memo
  applicable_regimes: [string]         # e.g. ["eu-ai-act-2024", "gdpr-2016-679",
                                       # "nyc-local-law-144-2021"]

  # --- 2. Reporting period and author ---
  period_start: date
  period_end: date
  period_type: enum                    # "monthly" | "quarterly" | "ad-hoc"
  cadence_ref: string                  # link to PMS plan / SOP
  authored_by: string                  # role
  reviewed_by: [string]                # roles
  approved_by: string|null             # role; null until approved
  approved_at: datetime|null

  # --- 3. Operating posture (headline) ---
  overall_posture: enum                # "steady-state" | "watch" | "action-required" |
                                       # "incident-in-remediation"
  headline: string                     # 2–3 sentences — what the period looks like

  # --- 4. Monitoring evidence ---
  monitoring_evidence:
    dashboard_readings:
      - reading_ref: string            # link to exercise-03 worksheet
        date: date
        overall_verdict: enum
        headline: string
    measurement_activities:
      - name: string                   # "quarterly fairness audit", "red-team cycle 24"
        date: date
        outcome_ref: string            # link to the measurement report
        summary: string                # one sentence

  # --- 5. Incidents ---
  incidents:
    - incident_id: string              # FK to exercise-02 record
      opened_at: date
      closed_at: date|null
      severity: enum                   # "sev-1"… "sev-4"
      status: enum                     # "open" | "contained" | "closed" |
                                       # "closed-with-follow-ups"
      regulatory_interactions:
        - regime: string               # e.g. "eu-ai-act-art-73"
          action: enum                 # "none" | "escalated-to-legal" |
                                       # "notification-filed" | "regulator-inquiry-received"
          date: date|null
          summary: string              # one sentence

  # --- 6. Follow-ups and control changes ---
  follow_ups_opened_count: integer
  follow_ups_closed_count: integer
  overdue_follow_ups_count: integer
  overdue_follow_ups:
    - id: string
      days_overdue: integer
      owner: string
      reason: string                   # one sentence; "resource", "upstream dependency"
  control_changes:
    - control_id: string               # FK to mod-108 control catalogue
      change_type: enum                # "added" | "amended" | "retired"
      rationale: string
      effective_date: date

  # --- 7. User feedback and complaints ---
  feedback:
    appeals_count: integer
    appeals_overturn_rate_pct: number
    contested_decisions_count: integer
    complaint_categories:
      - category: string               # "unexpected-decline", "explanation-quality",
                                       # "data-correction-request", ...
        count: integer
    commentary: string                 # 2–3 sentences; what the complaints say

  # --- 8. Regulator and external interactions ---
  external_interactions:
    - at: date
      counterparty: string             # "DCWP", "CNIL", "auditor X", "customer press"
      nature: enum                     # "inquiry" | "inspection" | "information-request" |
                                       # "notification-ack" | "other"
      summary: string
      owner: string                    # Legal role
      status: enum                     # "open" | "closed"

  # --- 9. PMS plan changes ---
  pms_plan_changes:
    - at: date
      section: string
      change: string
      rationale: string
      ref: string                      # link to the diff

  # --- 10. Cross-links ---
  cross_links:
    nist_ai_rmf:
      manage: [string]
      measure: [string]
    iso_42001: [string]
    eu_ai_act_articles: [string]       # e.g. ["Art. 9", "Art. 61", "Art. 72", "Art. 73"]
    related_risks: [string]            # mod-103
    related_obligations: [string]      # mod-106
    prior_summary_ref: string|null     # link to the previous period

  # --- 11. Next-period posture ---
  next_period_posture:
    posture: enum                      # same enum as overall_posture
    planned_checks:
      - name: string                   # "repeat quarterly fairness audit",
                                       # "retrain validation", "review threshold CTRL-AI-04"
        owner: string
        target_date: date
    open_questions: [string]
```

### 2.3 Worked summary — sys-0034-credit-score, Q3 2026

Reporting window: 2026-07-01 → 2026-09-30. Authored 2026-10-14;
reviewed and approved for the AI council. Numbers below are
illustrative teaching data that extend the exercise-03 dashboard
reading backward into the preceding quarter.

```yaml
post_market_surveillance_summary:
  # 1. System identification and scope
  system_id: sys-0034-credit-score
  short_name: Credit Scoring (SB Lending)
  intended_purpose: >
    Decision-assist model producing a creditworthiness score for
    small-business lending applications; approve / decline is
    decided by a human underwriter above a threshold, with the
    model's score and top-five feature contributions presented.
  high_risk_classification:
    assessment: eu-ai-act-annex-iii
    source_ref: /legal/2026-02/sys-0034-annex-iii-memo.pdf
  applicable_regimes:
    - eu-ai-act-2024
    - gdpr-2016-679
    - nyc-local-law-144-2021       # review pending — applicability unresolved

  # 2. Reporting period and author
  period_start: 2026-07-01
  period_end: 2026-09-30
  period_type: quarterly
  cadence_ref: /sops/pms/sys-0034-v3.md
  authored_by: AI Governance Analyst
  reviewed_by:
    - Head of AI Governance
    - Head of Model Risk
    - Data Protection Officer
  approved_by: Head of AI Governance
  approved_at: 2026-10-14T16:00:00+01:00

  # 3. Operating posture (headline)
  overall_posture: watch
  headline: >
    System operated in band for most of Q3; two incidents were
    opened and closed within the quarter; a fairness-slice
    trend on a new partner-channel cohort emerged in late September
    and is now being tracked for the Q4 cycle. No regulator
    interactions in the quarter.

  # 4. Monitoring evidence
  monitoring_evidence:
    dashboard_readings:
      - reading_ref: /records/mod-109/exercise-03/sys-0034-2026-07-07.yaml
        date: 2026-07-07
        overall_verdict: within-band
        headline: Steady state; no tile out of band.
      - reading_ref: /records/mod-109/exercise-03/sys-0034-2026-08-04.yaml
        date: 2026-08-04
        overall_verdict: within-band
        headline: Steady state; serving latency recovered after a
                  one-day capacity incident on 2026-07-21.
      - reading_ref: /records/mod-109/exercise-03/sys-0034-2026-09-01.yaml
        date: 2026-09-01
        overall_verdict: watch
        headline: >
          Input drift appearing on collateral_type; traffic
          composition shifting toward partner channel; aggregate
          fairness metrics still in band.
      - reading_ref: /records/mod-109/exercise-03/sys-0034-2026-09-29.yaml
        date: 2026-09-29
        overall_verdict: watch
        headline: >
          Partner channel share up 3pp; new-tenant cohort TPR gap
          -0.07 (within band, trending unfavourably).
    measurement_activities:
      - name: Quarterly fairness audit — Q3
        date: 2026-09-15
        outcome_ref: /audits/sys-0034/2026-q3-fairness.md
        summary: >
          All protected-attribute slices above 0.80 impact ratio
          at quarter close; new-tenant cohort at 0.86 and trending
          down — flagged for next quarter.
      - name: Scheduled red-team cycle
        date: 2026-08-26
        outcome_ref: /redteam/sys-0034/cycle-7.md
        summary: >
          Model-theft probing techniques attempted; rate limits
          held. No successful extraction.

  # 5. Incidents
  incidents:
    - incident_id: inc-2026-07-21-004-sys-0034-serving-capacity
      opened_at: 2026-07-21
      closed_at: 2026-07-29
      severity: sev-3
      status: closed-with-follow-ups
      regulatory_interactions:
        - regime: eu-ai-act-art-73
          action: none
          date: null
          summary: >
            Serving capacity incident; no customer harm, no data
            exposure; not a serious incident under Art. 73.
    - incident_id: inc-2026-08-14-007-sys-0034-explanation-template
      opened_at: 2026-08-14
      closed_at: 2026-08-21
      severity: sev-3
      status: closed
      regulatory_interactions:
        - regime: gdpr-art-22
          action: escalated-to-legal
          date: 2026-08-14
          summary: >
            Explanation template rendered feature contributions
            with a wrong label pair in 2.1% of declines for two
            days; Legal confirmed no meaningful-information-about-
            logic failure under GDPR Art. 22(3) given corrected
            copy within one working day.

  # 6. Follow-ups and control changes
  follow_ups_opened_count: 11
  follow_ups_closed_count: 9
  overdue_follow_ups_count: 1
  overdue_follow_ups:
    - id: fua-sys-0034-2026-09-012
      days_overdue: 6
      owner: Head of Vendor Risk
      reason: >
        Partner-contract annex revision pending at the vendor; not
        a resourcing issue. Weekly status to AI council.
  control_changes:
    - control_id: CTRL-AI-04-fairness-slice-monitor
      change_type: amended
      rationale: >
        Added `new_tenant_cohort` as a monitored slice following
        the Q3 fairness-audit trend.
      effective_date: 2026-09-22
    - control_id: CTRL-AI-14-capacity-alert
      change_type: added
      rationale: >
        Serving-capacity incident on 2026-07-21 motivated a
        capacity-saturation alert feeding the on-call.
      effective_date: 2026-08-02

  # 7. User feedback and complaints
  feedback:
    appeals_count: 182
    appeals_overturn_rate_pct: 7.1
    contested_decisions_count: 61
    complaint_categories:
      - category: unexpected-decline
        count: 37
      - category: explanation-quality
        count: 18
      - category: data-correction-request
        count: 6
    commentary: >
      Appeals volume within the 150–220 band for Q3; overturn
      rate at the upper edge of the target range; "unexpected
      decline" category dominated by partner-channel cases,
      consistent with the late-September fairness trend.

  # 8. Regulator and external interactions
  external_interactions: []

  # 9. PMS plan changes
  pms_plan_changes:
    - at: 2026-09-22
      section: "Monitored slices"
      change: >
        Added `new_tenant_cohort` as a monitored fairness slice;
        cadence quarterly; alert on impact ratio < 0.85.
      rationale: >
        Early-warning threshold set above the four-fifths line to
        catch trends before they become breaches.
      ref: /sops/pms/sys-0034-v3.md#monitored-slices

  # 10. Cross-links
  cross_links:
    nist_ai_rmf:
      manage: [MANAGE 2.4, MANAGE 4.1, MANAGE 4.2, MANAGE 4.3]
      measure: [MEASURE 2.7, MEASURE 2.11, MEASURE 2.12]
    iso_42001:
      - "A.6 Operational monitoring"
      - "Clause 9 Performance evaluation"
      - "Clause 10 Improvement"
    eu_ai_act_articles:
      - "Art. 9 — Risk management system"
      - "Art. 10 — Data governance"
      - "Art. 72 — Post-market monitoring"   # confirm article numbering against OJ text
      - "Art. 73 — Reporting of serious incidents"
    related_risks:
      - RISK-sys-0034-adverse-impact
      - RISK-sys-0034-covariate-shift
    related_obligations:
      - eu-ai-act-art-9-risk-management-system
      - eu-ai-act-art-10-data-governance
      - eu-ai-act-art-72-post-market-monitoring
      - nyc-ll144-annual-bias-audit
    prior_summary_ref: /records/pms/sys-0034/2026-q2.yaml

  # 11. Next-period posture
  next_period_posture:
    posture: watch
    planned_checks:
      - name: Repeat quarterly fairness audit including new slice
        owner: AI Governance Analyst
        target_date: 2026-12-15
      - name: Retrain-validation review on updated training window
        owner: Head of ML Platform
        target_date: 2026-11-30
      - name: Close vendor-contract annex revision (fua-sys-0034-2026-09-012)
        owner: Head of Vendor Risk
        target_date: 2026-11-07
      - name: Re-review Annex III classification given possible
              product-scope changes (partner-channel expansion)
        owner: Deputy General Counsel — Technology
        target_date: 2026-12-01
    open_questions:
      - Is sys-0034 in scope of NYC LL 144 as currently deployed
        for US partners? Pending Legal.
      - At what impact-ratio threshold does the PMS plan trigger a
        model-pause rather than a monitored-watch?
```

### 2.4 Decision rationale

- **One summary per system per period.** PMS is per-system
  evidence. Multi-system "program summaries" are a different
  artefact (`mod-110` reports to the AI council); they are not a
  substitute for the per-system record.
- **The headline is the first thing read.** AI council readers
  rarely read past section 3. The headline must carry the period's
  posture in 2–3 sentences.
- **Incidents before trends, trends before within-band.** The
  order is for the reader's benefit and matches how a
  market-surveillance authority or auditor will triage the
  document.
- **Bounded enums for `overall_posture` and `severity`.** Free-text
  posture ("mostly fine, a few issues") is not comparable across
  quarters.
- **PMS plan changes are logged.** Any change to what we monitor,
  at what cadence, with what threshold, is a change to the PMS
  plan and must be recorded with a rationale and a diff ref.
- **Overdue follow-ups are named.** Hiding them in an aggregate
  count is the fastest way to lose AI council trust. One row per
  overdue item, with the owner and the reason.
- **External interactions default to the empty list.** "None in
  this period" is a positive assertion; blank is unreviewable.
- **Cross-link to the prior summary.** Period-over-period reading
  is the single most valuable feature of a PMS; the previous
  summary link enables it.
- **`high_risk_classification` cites Legal's memo.** The analyst
  does not classify; the analyst records the classification with a
  pointer to the authoritative document.
- **Article numbers.** Where Art. 72 numbering is quoted, flag for
  verification against the OJ L-series text before the summary is
  shared externally; the EU AI Act went through multiple drafts
  with different numbering, and the final OJ text is authoritative.

## 3. Validation steps

1. Schema check: every required section present; `period_start <
   period_end`; `period_end` is in the past; `authored_by`,
   `reviewed_by`, and (after approval) `approved_by` are all roles.
2. Posture consistency: `overall_posture = steady-state` requires
   no open incidents and no overdue follow-ups;
   `incident-in-remediation` requires at least one open incident
   referenced.
3. Incident cross-reference: every `incident_id` resolves to an
   exercise-02 record; `severity` and `status` match the current
   state of that record.
4. Follow-up accounting: `follow_ups_opened_count -
   follow_ups_closed_count = net open` must reconcile to the
   action tracker.
5. Measurement activity resolution: every `outcome_ref` and
   `reading_ref` resolves to a file or URL in the governance
   repository.
6. Regulatory-article citation: every entry in
   `cross_links.eu_ai_act_articles` and `applicable_regimes`
   matches an entry in the regulatory tracker from `mod-106`.
7. Period-over-period chain: `cross_links.prior_summary_ref`
   resolves; if this is not the first period, the field is
   non-null.
8. Approval gate: `overall_posture != steady-state` requires an
   AI council review step logged in `reviewed_by`.
9. No customer PII in free-text narrative anywhere.
10. The PMS plan change log in section 9 references a diff; the
    rationale is one or two sentences, not a paragraph.

## 4. Rubric or review checklist

Grade on six dimensions, 3 points each (total 18). Pass = ≥ 14 with
no 0.

| Criterion                                     | 3 — strong                                                                                                             | 2 — acceptable                                                             | 0–1 — weak                                                        |
| --------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------- | ----------------------------------------------------------------- |
| **Section completeness**                      | All 11 sections populated or "none" with a one-sentence rationale; headings verbatim.                                  | 9–10 sections populated; a few renamed.                                    | Sections missing or merged; template deviates.                     |
| **Headline quality**                          | 2–3 sentences that carry the posture of the period; readable without the rest of the document.                         | Headline present but requires the full report to understand.               | Headline is a paragraph or missing.                                |
| **Monitoring-evidence linkage**                | Dashboard readings cross-link to exercise-03 worksheets; measurement activities link to outcome files.                 | Dashboard readings summarised but not cross-linked.                        | Monitoring evidence is a prose paragraph.                          |
| **Incident and follow-up reconciliation**      | Each incident cross-links to an exercise-02 record; follow-up counts reconcile; overdues are named.                    | Incidents linked but follow-ups reported only in aggregate.                | Incidents listed as prose; overdues hidden.                        |
| **Regulatory and PMS-plan discipline**        | Articles cited by number with a "verify" note where appropriate; PMS plan changes logged with a diff ref.              | Articles cited but no plan-change log.                                     | Regulatory references vague or absent; no plan-change discipline.  |
| **Next-period posture**                       | Specific planned checks with owners (role) and target dates; open questions written as questions.                      | Planned checks listed but no owners or dates.                              | "Continue monitoring" with no next action.                         |

Document-level binary checks (all must pass):

- [ ] `system_id` matches the inventory record.
- [ ] `period_type` matches the cadence in the PMS SOP.
- [ ] `authored_by`, `reviewed_by`, `approved_by` are roles.
- [ ] `overall_posture` is one of the four enum values.
- [ ] `external_interactions` is a list (empty list if none);
      never absent.
- [ ] `cross_links.prior_summary_ref` is set when a prior summary
      exists.

## 5. Common mistakes

- **Treating the summary as a dashboard screenshot.** The summary
  is text with structured fields; screenshots are evidence
  attachments.
- **Omitting within-band readings.** They are the evidence that
  the system was monitored. Skipping them because "nothing
  happened" collapses the period-over-period diff.
- **Describing a trend without naming it.** "Some drift was
  observed" is not a reading; "collateral_type PSI reached 0.21
  by 2026-09-01" is.
- **Burying overdues in an aggregate count.** Name them. One line
  per overdue, with owner and reason.
- **Confusing Art. 72 with Art. 73.** Art. 72 is the post-market
  monitoring obligation; Art. 73 is the serious-incident reporting
  obligation. The PMS summary is the Art. 72 artefact; the
  serious-incident notification extract (see exercise-02) is the
  Art. 73 artefact.
- **Writing the summary to be shared externally.** The summary is
  internal evidence; a regulator-facing extract is a different
  derived artefact owned by Legal.
- **No prior-summary cross-link.** Period-over-period readability
  is the single most important property of the PMS; the chain
  must be preserved.
- **Approving a summary with past-due follow-ups and
  `steady-state` posture.** The approval gate catches this; do
  not override.
- **Confusing the PMS plan with the PMS summary.** The plan
  defines what is monitored, at what cadence, with what
  thresholds; the summary reports what happened. Changes to the
  plan are logged in section 9 of the summary with a diff ref.
- **Omitting the `high_risk_classification.source_ref`.** The
  classification is Legal's decision; the analyst records the
  pointer, not the analysis.

## 6. References

- Regulation (EU) 2024/1689 — EU AI Act, in particular Art. 9
  (risk-management system), Art. 10 (data governance), Art. 61 /
  Art. 72 as applicable to the post-market monitoring obligation
  (verify article numbering against the OJ L-series text before
  external use), and Art. 73 (reporting of serious incidents).
  Legal owns final interpretation. <https://eur-lex.europa.eu/eli/reg/2024/1689/oj>
- Regulation (EU) 2016/679 — GDPR, cited where PMS summaries
  intersect with data-subject rights (Arts. 15, 22, 33). <https://eur-lex.europa.eu/eli/reg/2016/679/oj>
- NIST, *AI Risk Management Framework (AI RMF 1.0)*, January 2023 —
  `MEASURE 2.7` (security and resilience), `MEASURE 2.11` and
  `MEASURE 2.12` (monitoring for drift and performance over time),
  `MANAGE 2.4` (resource allocation to monitoring),
  `MANAGE 4.1`–`MANAGE 4.3` (incident identification, response,
  and lessons learned). <https://www.nist.gov/itl/ai-risk-management-framework>
- NIST, *AI RMF Playbook* — companion actions for MEASURE and
  MANAGE. <https://airc.nist.gov/AI_RMF_Knowledge_Base/Playbook>
- ISO/IEC 42001:2023 — *Artificial intelligence management system*,
  Clause 9 (performance evaluation), Clause 10 (improvement),
  Annex A A.6 (operational planning and monitoring),
  A.8 (AI system life cycle). <https://www.iso.org/standard/81230.html>
- OWASP Machine Learning Security Top 10 — referenced when PMS
  findings implicate a specific ML attack surface. <https://owasp.org/www-project-machine-learning-security-top-10/>
- OWASP Top 10 for Large Language Model Applications — referenced
  when PMS findings implicate an LLM-specific risk. <https://owasp.org/www-project-top-10-for-large-language-model-applications/>
- MITRE ATLAS — referenced when PMS findings implicate adversary
  techniques recorded during the period (red-team cycles,
  attempted exploits). <https://atlas.mitre.org/>
- *Uniform Guidelines on Employee Selection Procedures* (US EEOC,
  29 C.F.R. Part 1607) — origin of the four-fifths screening
  convention referenced in slice-fairness thresholds. <https://www.ecfr.gov/current/title-29/subtitle-B/chapter-XIV/part-1607>
