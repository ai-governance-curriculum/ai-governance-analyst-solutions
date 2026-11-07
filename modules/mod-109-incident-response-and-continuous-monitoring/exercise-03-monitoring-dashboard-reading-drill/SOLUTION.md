---
exercise_id: exercise-03-monitoring-dashboard-reading-drill
module: mod-109-incident-response-and-continuous-monitoring
artifact: SOLUTION.md
---

# Exercise 03 — Monitoring Dashboard Reading Drill — Reference Solution

## 1. Solution overview

Reading a monitoring dashboard is the governance analyst's recurring
job — not building it, not instrumenting it, but reading it the way
an auditor would, and routing what the dashboard says into incidents,
follow-ups, or a steady-state "within tolerance" entry in the log.
Done well, the analyst produces a short note every operating cycle
(daily, weekly, or whatever the system's cadence is) that says what
the dashboard showed, what moved, what is within band, what fired,
and what the analyst did about each firing. Done badly, the
dashboard is a wall of charts nobody signs.

The drill's job is narrower than operating the dashboard: it is a
**reading exercise**. The analyst is given a snapshot and must
return (1) a tile-by-tile reading, (2) a three-way routing decision
on each firing (file an incident, open a follow-up, within band), and
(3) a two-paragraph summary suitable for the model's monthly
post-market monitoring log (see exercise-04).

This solution provides:

- A standard dashboard anatomy that the analyst expects for an
  operational AI system — eight tile families with purpose, signals,
  and the thresholds the analyst should look for.
- A worked snapshot with eight tiles for a hypothetical high-risk
  credit-scoring model `sys-0034-credit-score`, with each tile
  called out by name, each firing logged, and each routing decision
  justified.
- A routing worksheet template (YAML) analysts can reuse.
- Decision rationale, validation, rubric, and the common misreads.

Scope: this exercise is about **reading** the dashboard, not
designing the monitors. Monitor design decisions (what to alert on,
with what thresholds) belong with the ML platform team; the governance
analyst challenges those designs through the control catalogue
(`mod-108`) and the impact assessment (`mod-105`).

## 2. Worked answer or implementation

### 2.1 Dashboard anatomy — the eight tile families

Any production AI system worth monitoring presents these tile
families, in roughly this order:

| # | Tile family                         | What it tells the analyst                                                                                                             | Primary signals to read                                                           |
| - | ----------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| 1 | **Serving health**                  | Is the system up and serving at expected request volumes and latency?                                                                | Request rate, error rate, p50/p95/p99 latency, saturation.                        |
| 2 | **Traffic composition**             | Is the request mix the mix the system was validated on?                                                                              | Request counts by segment, cohort, tenant, feature flag, channel.                 |
| 3 | **Input feature drift**             | Are inputs looking like training inputs?                                                                                             | Per-feature population statistics, PSI / KS / Wasserstein distance vs. reference. |
| 4 | **Output distribution**             | Are the model's outputs looking like what we expect at this time of day / week / season?                                             | Score histograms, class-rate time series, confidence distributions.               |
| 5 | **Predictive performance**          | When ground truth lands, how right is the model?                                                                                     | Metric trend (AUC, F1, calibration, Brier); lag note; sample size.                |
| 6 | **Fairness / slice performance**    | Is any protected or high-impact slice degrading faster than the aggregate?                                                            | Per-slice performance + disparity metrics (TPR gap, selection rate gap, impact ratio). |
| 7 | **Safety / policy triggers**        | Are safety, policy, or guardrail layers firing more or less than usual? Are PII / toxicity filters in-band?                          | Guardrail trigger rates, blocklist hits, red-team regression counts.              |
| 8 | **Feedback & complaints**           | Is the humans-in-the-loop channel telling us something the automated monitors are not?                                               | Contested decisions, appeal rates, complaint categories, agent-flag queue depth.  |

Analyst reading rules of thumb:

- **Read in order.** Health first, performance later. A latency
  storm will mis-shape every downstream tile.
- **A single tile does not make a decision.** Correlate across at
  least two tiles before routing anything beyond `within-band`.
- **Trend matters more than point value.** A 3-sigma deviation in
  one hour is a monitor-side problem; a 2-sigma rise sustained over
  a week is a model problem.
- **Fairness tile is never "within band" by aggregate alone.**
  Always read the slice-level numbers, not the roll-up.

### 2.2 Worked dashboard snapshot

*Local exercise context.* System `sys-0034-credit-score`, a
decisioning-assist model used for small-business lending in the EU
and US-NY jurisdictions. The analyst reads the last-30-days snapshot
on 2026-11-05. All numbers below are illustrative teaching data.

#### Tile 1 — Serving health

```
request_rate_qps            = 420 (band: 350–520)                     ok
error_rate_pct              = 0.08% (band: < 0.5%)                    ok
latency_p50_ms              = 85  (band: < 120)                       ok
latency_p95_ms              = 310 (band: < 350)                       ok
latency_p99_ms              = 540 (band: < 600)                       ok
saturation_gpu_pct          = 61% (band: < 75%)                       ok
```

Reading: healthy. Everything within band for the last 7 days. No
latency spike; no error rate elevation. Downstream tiles can be read
at face value.

#### Tile 2 — Traffic composition

```
jurisdiction_mix_eu_pct     = 48% (reference: 46 ± 3)                 ok
jurisdiction_mix_us_pct     = 52% (reference: 54 ± 3)                 ok
channel_mix_web_pct         = 74% (reference: 70 ± 4)                 ok
channel_mix_partner_pct     = 26% (reference: 30 ± 4)                 drift
new_tenant_requests_24h     = 3                                        new
```

Reading: three new partner tenants began sending traffic in the
past week. The partner channel share has moved down 4 points — at
the edge of the reference band but not out. The *shape* of partner
traffic has changed; downstream tiles must be slice-read, not just
aggregate-read.

#### Tile 3 — Input feature drift

```
feature                     PSI (1d vs 90d ref)   KS p-value      verdict
annual_revenue              0.07                  0.21            ok
months_in_business          0.08                  0.14            ok
industry_code               0.19                  0.03            watch
geographic_region           0.29                  0.002           drift
dso_days                    0.11                  0.08            ok
collateral_type             0.34                  < 1e-3          drift
```

Reading: two features have drifted — `geographic_region` and
`collateral_type`. Both are consistent with the new partner tenants
in Tile 2 bringing a different mix of applicants (rural weight up,
collateral types weighted toward equipment). PSI > 0.25 is the
analyst's standard "drift" threshold; both features pass it.

#### Tile 4 — Output distribution

```
score_mean                  = 0.62 (reference: 0.64 ± 0.03)           ok
score_stddev                = 0.18 (reference: 0.17 ± 0.02)           ok
approval_rate_30d           = 58% (reference: 61 ± 3)                 ok (edge)
auto_decline_rate_30d       = 7%  (reference: 6 ± 1.5)                ok (edge)
borderline_band_rate_30d    = 18% (reference: 14 ± 3)                 drift
```

Reading: the borderline-band rate is up 4 points. More applications
are landing in the human-review band; approvals are flat. This is
consistent with the Tile 3 input drift — the model's confidence is
lower on the newer applicant mix.

#### Tile 5 — Predictive performance (lagged)

```
auc_eval_window_end         = 2026-10-08 (lag: 28 days)
auc_overall                 = 0.81 (reference: 0.83, band: ± 0.02)    watch
calibration_ece_overall     = 0.047 (band: < 0.05)                    ok
brier_overall               = 0.149 (reference: 0.142, band: ± 0.01)  watch
sample_size_overall         = 18,400                                   sufficient
```

Reading: the predictive-performance window lags 28 days because
ground truth (90-day repayment) is not available until then. AUC is
one standard reference band below target; Brier is similarly
nudging up. One window is not a trend; two consecutive windows is.
The analyst checks the trailing three windows — they are 0.83,
0.82, 0.81 — this is a trend.

#### Tile 6 — Fairness / slice performance

```
slice                       approval_rate  AUC    TPR_gap_vs_ref     impact_ratio
all                         58%            0.81   -                  -
age_18_24                   49%            0.78   -0.04              0.84
age_25_44                   61%            0.82   +0.01              1.05
age_45_64                   62%            0.82   0.00               1.07
age_65_plus                 55%            0.80   -0.02              0.95
rural_applicant             51%            0.77   -0.05              0.88
urban_applicant             60%            0.82   0.00               1.03
new_tenant_cohort           46%            0.73   -0.10              0.79   ** ALERT **
```

Reading: the new-tenant cohort is at **impact ratio 0.79**, below
the common four-fifths screening threshold used in US employment
contexts and widely referenced as a generic fairness signal (see
also `mod-104`). TPR gap is -0.10; AUC on the cohort is 0.08 below
aggregate. This is the headline finding of the entire dashboard.
The rural-applicant slice is also below band but within the
four-fifths line.

#### Tile 7 — Safety / policy triggers

```
pii_leak_filter_fires_24h   = 0   (band: 0–3)                          ok
decision_override_requests  = 14  (band: 5–20)                         ok
explanation_endpoint_errors = 1.4%   (band: < 2%)                      ok
manual_review_sla_breaches  = 6   (band: 0)                            breach
```

Reading: the manual review SLA has been breached six times in the
window. Human reviewers are not getting to borderline-band cases
within policy time. This correlates with Tile 4's rise in
borderline-band rate — reviewer throughput has not kept up.

#### Tile 8 — Feedback & complaints

```
contested_decisions_30d     = 42 (reference: 15–25)                    watch
contest_by_channel_partner  = 29 of 42 (69%)                           elevated
contest_by_jurisdiction_us  = 24 of 42                                 elevated
appeal_overturn_rate        = 11%  (reference: 6–9%)                   watch
```

Reading: contested decisions are up and concentrated in the new
partner channel and in US-NY. Appeal-overturn rate is elevated,
meaning a non-trivial share of those contests result in a reversal.
Cross-reads with Tile 6 (new-tenant cohort, impact ratio 0.79)
suggest the slice disparity is already reaching the complaint queue.

### 2.3 Cross-tile correlations — reading the whole dashboard

Three correlated signals:

- **Partner-channel onboarding → input drift → output shift.**
  Tiles 2, 3, and 4 agree: new traffic shape has shifted inputs and
  pushed more cases to borderline.
- **New-tenant cohort has a four-fifths-rule disparity.** Tile 6 is
  the highest-severity signal on the dashboard — a slice disparity
  below 0.80 with a confirmed TPR gap, backed up by a visible
  increase in contested decisions (Tile 8).
- **Reviewer throughput is a bottleneck.** Tile 7 shows SLA breaches,
  not a safety failure of the model itself. This is operational.

The right *governance* action is driven by Tile 6 + Tile 8. The
right *operational* action is driven by Tile 7. Both lead to the
same incident record but to different follow-up owners.

### 2.4 Routing worksheet (template)

Reusable analyst worksheet to produce per-cycle.

```yaml
dashboard_reading:
  system_id: string                  # FK to inventory
  window: string                     # "2026-10-06 .. 2026-11-05"
  snapshot_as_of: datetime
  read_by: string                    # role of the analyst
  overall_verdict: enum              # "within-band" | "watch" | "action-required" |
                                     # "incident"
  headline: string                   # one sentence; what moved and why it matters

  tile_readings:
    - tile: enum                     # the eight tile families
      verdict: enum                  # "ok" | "edge" | "watch" | "breach"
      notes: string                  # 1–3 sentences; cite the number
      evidence_ref: string|null      # link to dashboard view or query

  routing_decisions:
    - finding: string                # one sentence
      route: enum                    # "within-band" | "open-follow-up" |
                                     # "file-incident" | "escalate-to-legal"
      rationale: string              # tied to the dashboard reading + severity rubric
      owner: string                  # role, not person
      due: date|null
      incident_id: string|null       # if filed, the id (see exercise-02)

  next_actions_internal:
    - audience: string               # "AI council" | "model risk committee" | "product"
      summary: string
      owner: string
      due: date

  cross_links:
    nist_ai_rmf_measure: [string]    # e.g. ["MEASURE 2.11", "MEASURE 2.12"]
    nist_ai_rmf_manage:  [string]    # e.g. ["MANAGE 2.4", "MANAGE 4.1"]
    related_controls:    [string]    # mod-108
    related_risks:       [string]    # mod-103
    related_obligations: [string]    # mod-106
```

### 2.5 Worked worksheet — sys-0034 snapshot 2026-11-05

```yaml
dashboard_reading:
  system_id: sys-0034-credit-score
  window: "2026-10-06 .. 2026-11-05"
  snapshot_as_of: 2026-11-05T09:00:00+01:00
  read_by: AI Governance Analyst
  overall_verdict: action-required
  headline: >
    New partner-channel traffic has pushed the new-tenant cohort
    below the four-fifths impact-ratio threshold; appeals and
    contested decisions are rising in parallel; manual review SLA
    is intermittently breached.

  tile_readings:
    - tile: serving-health
      verdict: ok
      notes: >
        All serving metrics in band; downstream tiles can be
        read at face value.
      evidence_ref: /dashboards/sys-0034/serving?w=30d
    - tile: traffic-composition
      verdict: edge
      notes: >
        Partner channel share -4pp; three new partner tenants
        in the last week — shifting applicant mix.
      evidence_ref: /dashboards/sys-0034/traffic?w=30d
    - tile: input-drift
      verdict: watch
      notes: >
        geographic_region PSI 0.29 and collateral_type PSI 0.34
        over the 1d-vs-90d-reference compare; consistent with the
        new partner mix.
      evidence_ref: /dashboards/sys-0034/drift?w=30d
    - tile: output-distribution
      verdict: watch
      notes: >
        Borderline-band rate 18% (ref 14 ± 3) — model confidence
        is lower on the new cohort; approvals flat.
      evidence_ref: /dashboards/sys-0034/outputs?w=30d
    - tile: predictive-performance
      verdict: watch
      notes: >
        AUC trend 0.83 → 0.82 → 0.81 across three ground-truth
        windows; Brier nudging up; sample size sufficient.
      evidence_ref: /dashboards/sys-0034/perf?w=90d
    - tile: fairness-slice
      verdict: breach
      notes: >
        new_tenant_cohort impact ratio 0.79 (below four-fifths);
        TPR gap -0.10; AUC on cohort 0.73.
      evidence_ref: /dashboards/sys-0034/fairness?w=30d
    - tile: safety-policy
      verdict: breach
      notes: >
        Manual review SLA breached 6 times in the window; no PII
        leak fires; override queue healthy.
      evidence_ref: /dashboards/sys-0034/safety?w=30d
    - tile: feedback-complaints
      verdict: watch
      notes: >
        Contested decisions 42 (ref 15–25); 69% from partner
        channel; appeal-overturn rate 11% (ref 6–9).
      evidence_ref: /dashboards/sys-0034/feedback?w=30d

  routing_decisions:
    - finding: >
        new_tenant_cohort impact ratio below four-fifths with
        corroborating complaint signal.
      route: file-incident
      rationale: >
        Fairness-slice breach with downstream complaint signal and
        confirmed slice under-performance meets the local severity
        rubric for sev-2 incident with potential regulatory
        exposure under NYC LL 144 (if AEDT-adjacent use emerges)
        and EU AI Act Art. 10 data-governance / Art. 9
        risk-management duties if system is Annex III high-risk.
      owner: AI Governance Analyst
      due: 2026-11-06
      incident_id: inc-2026-11-05-010-sys-0034-fairness-breach
    - finding: >
        Input drift on geographic_region and collateral_type,
        correlated with partner-channel onboarding.
      route: open-follow-up
      rationale: >
        Within monitor-alert tolerance for individual features but
        now explained by a known business event; follow-up to
        revalidate reference window and consider retraining.
      owner: Head of ML Platform
      due: 2026-11-26
      incident_id: null
    - finding: >
        Manual review SLA breaches (6 in 30d) — reviewer throughput
        bottleneck.
      route: open-follow-up
      rationale: >
        Operational control failure (human-oversight control from
        mod-108); not a model failure, but a documented control
        failure that must be rolled up to the AI council.
      owner: Head of Operations
      due: 2026-11-19
      incident_id: null
    - finding: >
        AUC trend three windows in a row below reference.
      route: open-follow-up
      rationale: >
        Not an incident yet; watch for a fourth consecutive
        window. If confirmed, trigger model-risk review per
        internal MRM policy.
      owner: Model Risk Manager
      due: 2026-12-05
      incident_id: null

  next_actions_internal:
    - audience: AI council
      summary: >
        Fairness breach on new_tenant_cohort — incident filed;
        follow-ups opened on drift, reviewer SLA, and AUC trend.
      owner: AI Governance Analyst
      due: 2026-11-10

  cross_links:
    nist_ai_rmf_measure: [MEASURE 2.11, MEASURE 2.12, MEASURE 2.7]
    nist_ai_rmf_manage:  [MANAGE 2.4, MANAGE 4.1, MANAGE 4.2]
    related_controls:
      - CTRL-AI-04-fairness-slice-monitor
      - CTRL-AI-06-review-sla
      - CTRL-AI-08-drift-monitor
    related_risks:
      - RISK-sys-0034-adverse-impact
      - RISK-sys-0034-covariate-shift
    related_obligations:
      - eu-ai-act-art-9-risk-management-system
      - eu-ai-act-art-10-data-governance
      - eu-ai-act-art-61-post-market-monitoring  # confirm article numbering
      - nyc-ll144-annual-bias-audit
```

### 2.6 Decision rationale

- **Eight tile families, read in order.** Covers health, context,
  inputs, outputs, performance, fairness, safety, and feedback.
  Missing any family leaves the analyst blind to a well-known
  failure mode (e.g., safety-only dashboards miss fairness drift).
- **Trend over point.** Single-window spikes are usually monitor-
  side. Three-window trends are model-side. The rubric codifies
  this so the analyst does not escalate every one-sigma blip.
- **Fairness tile is routed differently.** A fairness-slice breach
  is the one signal that moves directly to a sev-2 incident with
  regulatory implications. Other breaches route to follow-ups.
- **The dashboard is always read in cross-tile correlation.** Any
  single-tile verdict without a corroborating second tile goes to
  `watch`, not to `breach`.
- **The analyst does not change thresholds.** Thresholds are
  controls (`mod-108`); changing them is a control-change action
  with a rationale.
- **`overall_verdict` is one of four, nothing finer.** The analyst
  does not invent new verdicts — four buckets keep the queue
  readable.
- **Role, not person, in `owner` and `read_by`.** Audit continuity.
- **Cross-links to the control catalogue and risk register** make
  the worksheet a diffable artefact against both — a dashboard
  reading without those cross-links is impossible to defend in a
  review.

## 3. Validation steps

1. Schema check: every tile in the eight-family list has a verdict
   and notes with at least one cited number.
2. `overall_verdict` consistency: `action-required` or `incident`
   requires at least one `breach` tile; `within-band` requires no
   tile in `breach` or `watch`.
3. Routing-decision coverage: every tile with verdict ∈ `{watch,
   breach}` has a routing decision; a tile that is `breach` but has
   no routing decision fails review.
4. Four-fifths rule: any slice with impact ratio < 0.80 triggers at
   least a `file-incident` routing decision or an explicit, written
   rationale for why not. The threshold is a well-known screening
   convention used in US employment-discrimination analysis (see
   `mod-104`) and is used here as a generic sanity signal — local
   policy may require a stricter threshold.
5. Cross-tile sanity: if `predictive-performance` is `watch` but
   `output-distribution`, `input-drift`, and `fairness-slice` are
   all `ok`, flag the window for lag or data-quality review (the
   performance tile is late signal).
6. Owner-and-due resolution: every routing decision has a role-level
   owner and a due date (nullable only for `within-band`).
7. Cross-links present: at least one entry in
   `cross_links.nist_ai_rmf_measure` and one in
   `cross_links.nist_ai_rmf_manage` on any non-`within-band`
   reading.
8. Narrative hygiene: no customer-identifying language in `notes`;
   no vendor PII; numbers cited with units and reference bands.

## 4. Rubric or review checklist

Grade on five dimensions, 3 points each (total 15). Pass = ≥ 11 with
no 0.

| Criterion                                    | 3 — strong                                                                                                             | 2 — acceptable                                                           | 0–1 — weak                                                        |
| -------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------ | ----------------------------------------------------------------- |
| **Tile-family coverage**                     | Reads all eight tile families; cites a number for each verdict.                                                        | Reads 6–7 tile families with cited numbers.                              | Treats the dashboard as a single chart; skips families.           |
| **Cross-tile correlation**                   | Finds and names at least one cross-tile correlation (e.g., traffic + drift + output).                                  | Names one correlation but misses a second obvious one.                   | Reads each tile in isolation; no correlation.                     |
| **Fairness-slice reading**                   | Reads the slice table, not the aggregate; flags impact-ratio < 0.80 with explicit rationale; cites four-fifths.        | Reads slices but does not explicitly cite the four-fifths threshold.     | Reads the aggregate only; misses the slice breach.                |
| **Routing discipline**                       | Each `watch`/`breach` has a routing decision; each decision names an owner (role) and a due date; incidents have an id. | Most routing decisions are populated but one lacks an owner or date.    | Routing is implicit or absent; no ownership.                      |
| **Cross-links and narrative hygiene**        | Cites NIST AI RMF MEASURE + MANAGE; links controls (mod-108); no customer-identifying language in notes.               | Cites one of MEASURE/MANAGE; some control links missing.                 | No cross-links; customer-identifying strings present in notes.    |

Document-level binary checks (all must pass):

- [ ] Window is a closed date range in one timezone.
- [ ] Every verdict is tied to a number with its unit and
      reference band.
- [ ] Any slice below four-fifths has a routing decision.
- [ ] `read_by` is a role.
- [ ] Linked dashboard URLs are specific views, not the dashboard
      home page.

## 5. Common mistakes

- **Reading the aggregate instead of the slice.** The fairness tile
  is the one tile where this is never acceptable.
- **Treating a single-window dip as a trend.** Three windows matter.
- **Firing an incident on every `watch`.** Signal noise. Watch is a
  follow-up or a re-check, not an incident.
- **Ignoring Tile 2 (traffic composition).** Most "drift" is really
  a traffic-shape change. Reading the input-drift tile first,
  without the composition tile, invents false stories.
- **Confusing the predictive-performance tile with the output
  tile.** Output distribution is available in real time; predictive
  performance is lagged by label acquisition.
- **Treating manual review SLA breaches as a model problem.** It is
  an operational control problem — route to Operations, not to the
  ML team.
- **Narrative with customer-identifying strings.** The reading is a
  governance artefact that will be shared; redact.
- **Owner as a team.** "Compliance" is not an owner; "Head of AI
  Governance" is.
- **No NIST AI RMF cross-link.** The reading is not reviewable
  against the framework the organisation claims to follow.
- **Dropping a `within-band` reading from the log.** Within-band
  readings are the evidence that monitoring happened; keep them.

## 6. References

- NIST, *AI Risk Management Framework (AI RMF 1.0)*, January 2023 —
  `MEASURE 2` (robustness, fairness, privacy, security),
  `MEASURE 2.11` and `MEASURE 2.12` (drift and performance
  monitoring), `MANAGE 2.4` (resource allocation for monitoring),
  `MANAGE 4.1` (incident identification and documentation). <https://www.nist.gov/itl/ai-risk-management-framework>
- NIST, *AI RMF Playbook* — companion actions for MEASURE and
  MANAGE functions. <https://airc.nist.gov/AI_RMF_Knowledge_Base/Playbook>
- ISO/IEC 42001:2023 — *Artificial intelligence management system*,
  Clause 9 (performance evaluation) and Annex A A.6 (operational
  monitoring) controls. <https://www.iso.org/standard/81230.html>
- Regulation (EU) 2024/1689 — EU AI Act, Art. 72 (post-market
  monitoring plan for high-risk AI systems) and Art. 9
  (risk-management system), referenced here for how dashboard
  outputs feed post-market monitoring. <https://eur-lex.europa.eu/eli/reg/2024/1689/oj>
- OWASP Machine Learning Security Top 10 — referenced where a
  dashboard tile monitors for a specific attack surface. <https://owasp.org/www-project-machine-learning-security-top-10/>
- OWASP Top 10 for Large Language Model Applications — referenced
  where LLM-specific monitors are present. <https://owasp.org/www-project-top-10-for-large-language-model-applications/>
- MITRE ATLAS — referenced where guardrail/monitor tiles map to
  adversary techniques. <https://atlas.mitre.org/>
- *Uniform Guidelines on Employee Selection Procedures* (US EEOC,
  29 C.F.R. Part 1607) — origin of the four-fifths-rule screening
  convention used in Tile 6 as a generic fairness signal (mod-104). <https://www.ecfr.gov/current/title-29/subtitle-B/chapter-XIV/part-1607>
