# Exercise 01 — Control Cycle Analyst Runbook — Reference Solution

## 1. Solution overview

Controls are the day-to-day plumbing of an AI governance programme.
Policy sets the "we will"; the control catalogue sets the "how we know
we did"; the control cycle is the recurring beat that turns that
catalogue into operating evidence an auditor and a council can rely on.

The analyst's job in the control cycle is not to *own* controls — that
sits with engineering, security, product, HR, or vendor-management — but
to drive the cadence: who is testing what, by when, with what evidence,
and what to route when a test fails. Without a runbook the analyst
re-invents the cadence every quarter, controls drift to "whoever asks
loudest", and evidence is assembled frantically ahead of audits.

This solution provides:

- A reference runbook organising the control cycle into four phases
  (plan → test → close → report) mapped to NIST AI RMF `GOVERN /
  MAP / MEASURE / MANAGE` functions and to ISO/IEC 42001 AIMS
  clauses.
- A worked quarterly cycle for a seven-control slice (two technical,
  two documentation, two process, one third-party control).
- Role definitions, routing rules, and the escalation ladder.
- Decision rationale and a grading rubric.

Scope: this is a design + process exercise. The runbook is written
to live next to the organisation's existing AIMS / ISMS documentation
and to be used by one analyst with support from a council.

## 2. Worked answer or implementation

### 2.1 Runbook structure

The control cycle operates on a **90-day quarterly beat** with an
annual re-baselining. Each quarter runs four phases:

| Phase | Purpose | NIST AI RMF function | ISO/IEC 42001 clause (illustrative) |
|---|---|---|---|
| **Plan** | Decide which controls get tested, who tests them, what evidence they will produce. | `GOVERN` 1.*, 2.*, 4.* | Clause 6.1–6.2 (planning), 7.2 (competence) |
| **Test** | Operate the control; record the evidence; note deviations. | `MEASURE` 1.*, 2.*, 3.* | Clause 8.1 (operational planning and control), 9.1 (monitoring and measurement) |
| **Close** | Review evidence; mark pass / partial / fail; raise findings; agree remediation. | `MANAGE` 1.*, 2.*, 4.* | Clause 10.1 (continual improvement), 10.2 (nonconformity and corrective action) |
| **Report** | Summarise to the council; publish the quarter-end register; feed exceptions into the risk register. | `GOVERN` 5.*, `MANAGE` 4.* | Clause 9.3 (management review) |

Clause-level mapping above is illustrative; cross-check against the
organisation's own ISO/IEC 42001 clause index before publishing.

The analyst's runbook assumes:

- A control catalogue exists (per-control: id, intent, owner role,
  frequency, evidence type, mapped risks, mapped regulatory
  obligations, mapped AI systems).
- A risk register exists (`mod-103`).
- An inventory exists (`mod-102`) with a stable `ai_system.id`.
- An evidence store exists (immutable with timestamp and author).

### 2.2 Control record — minimum fields

```yaml
control:
  id: string                      # "CTRL-DOC-02-model-card-published"
  intent: string                  # one sentence: what outcome this control exists to guarantee
  owner_role: string              # role, not team, not person
  secondary_owner_role: string|null
  frequency: enum                 # "continuous", "on-change", "monthly", "quarterly",
                                  # "semi-annual", "annual", "on-incident"
  evidence_type: [enum]           # "attestation", "log-sample", "artefact-link",
                                  # "test-output", "screenshot", "signed-record"
  test_procedure: string          # how the test is run; references the SOP file
  mapped_risks: [string]          # FK to risk register ids
  mapped_obligations: [string]    # FK to regulatory tracker obligation ids (mod-106)
  mapped_frameworks:
    nist_ai_rmf: [string]         # subcategory refs ("GOVERN 1.4", "MEASURE 2.11")
    iso_42001: [string]           # clause / Annex A refs
    nist_ai_600_1: [string]       # GAI profile action refs, where applicable
    owasp_ml_top_10: [string]     # OWASP ML Sec Top 10 category ids, where applicable
    owasp_llm_top_10: [string]    # OWASP LLM Top 10 category ids, where applicable
    mitre_atlas: [string]         # ATLAS tactic / technique ids, where applicable
  applies_to_systems: [string]    # FK to ai_system.id; "*" for cross-cutting
  last_result: enum|null          # "pass" / "partial" / "fail" / "not-run"
  last_run_date: date|null
  next_due_date: date
  exceptions: [string]            # open exception ids
```

### 2.3 The quarterly cycle — week by week

**Week 1 — Plan.**
The analyst publishes the quarter's test plan to the council one week
before the quarter begins. For each control the plan names the
responsible role, the test procedure reference, the evidence expected,
and the due date. Plan rules:

- Every control with `frequency` ∈ {`quarterly`, `semi-annual` due
  this quarter, `annual` due this quarter, `on-change` with a
  change last quarter, `on-incident` with an open incident} is
  in-scope.
- Every control whose `mapped_obligations` deadline is in this
  quarter or the next is in-scope regardless of base frequency.
- Every control whose `last_result` was `partial` or `fail` is in-
  scope with a corrective-action re-test.
- Every new system added to the inventory this quarter triggers a
  one-off "intake-landing" control test sweep.

**Weeks 2–10 — Test.**
Owners operate controls on their own cadence. The analyst runs a
weekly sweep:

- Monday: pull the open-evidence list; nudge owners whose evidence is
  due this week.
- Thursday: triage new evidence into pass / partial / fail / need-
  more-info. Pass goes to the evidence store; partial opens an
  action item; fail opens a finding and routes per §2.6.
- Any `MEASURE`-function technical control that reports from an
  automated pipeline (fairness pipeline, bias monitor, prompt-
  injection evaluation, drift monitor) is read **inside** the test
  phase, not at close — a failing signal in week 3 is a week-3
  finding, not a week-11 surprise.

**Weeks 11–12 — Close.**
Each open test is driven to one of four states:

- `pass` — evidence on file, control met.
- `partial` — evidence shows the control operated, with deviations
  that do not change the risk posture. Corrective action due next
  cycle.
- `fail` — control not operated, or evidence shows it operated but
  did not achieve its intent. Finding opened; risk register updated;
  owner commits to a date; executive escalation if ownership does
  not land.
- `not-run` — owner did not operate the control at all. This is a
  `fail` for cycle reporting but is tracked separately so repeat
  `not-run` by the same owner becomes a council-visible pattern.

**Week 13 — Report.**
The analyst publishes:

1. The quarter-end control register (one row per control, result,
   evidence pointer, exceptions, next due).
2. A findings pack (per-finding owner, severity, agreed fix, due
   date) routed into the risk register.
3. The council agenda item: three lines per control-type bucket
   (technical / documentation / process / third-party) plus the
   top three exceptions.
4. A delta memo against last quarter — what moved and why.

### 2.4 Worked quarter — seven-control slice

The table below models one quarter for a fictional scope of two
hiring-adjacent systems. Dates assume quarter start **2026-10-01**.

| Control id | Intent | Owner (role) | Frequency | Evidence | Result | Notes |
|---|---|---|---|---|---|---|
| **CTRL-TEC-04-pre-release-eval** | Every release of an AI system runs the release-gate evaluation suite; failing suites block the release. | Head of ML Engineering | on-change | eval-run log; release ticket with gate verdict | pass | 3 releases in-quarter; gate blocked 1 release (model-v2-rc3) due to below-threshold calibration ECE. |
| **CTRL-TEC-07-fairness-monitor** | Monthly fairness monitoring runs on production hiring systems; impact-ratio breach routes to the fairness-review committee. | Head of ML Engineering | monthly | monitor run ids; breach routing tickets | partial | Monitor ran 3/3 months; routing SLA (5 business days) missed once (6 days). Corrective action: automate routing email on alert. |
| **CTRL-DOC-02-model-card-published** | Every production AI system has a published model card at current major version. | AI Governance Analyst | on-change + quarterly check | model-card URL; git SHA | pass | 2/2 systems on current version. |
| **CTRL-DOC-05-fria-on-file** | Any high-risk system under the EU AI Act Art. 27 definition has a FRIA on file, reviewed within the last 12 months. | Head of AI Governance | annual, on-change | FRIA pdf; review signature | fail | sys-0012 FRIA is dated 2025-08-01; Art. 27 trigger (new deployment to EU) was 2026-07-14 — a new FRIA was required within 6 months. Finding opened; due 2026-11-30. |
| **CTRL-PROC-03-intake-complete** | No AI system goes to production without a completed intake record (mod-102). | Head of AI Governance | on-change | intake record link; inventory entry | pass | 1 new system onboarded; intake closed before go-live. |
| **CTRL-PROC-08-incident-postmortem** | Every AI incident (per mod-109) produces a postmortem within 15 business days of closure. | AI Governance Analyst | on-incident | postmortem pdf; approval signature | not-run | No AI incidents in-quarter; control not triggered. Not a failure. |
| **CTRL-TP-02-vendor-attestation** | Every tier-1 AI vendor supplies an annual attestation covering the questions in `mod-108` exercise-04. | Head of Procurement | annual | signed attestation; effective date | partial | 4/5 tier-1 vendors returned. Vendor E missed the deadline; corrective action: escalate at the business-review meeting; replacement sourcing started as contingency. |

**End-of-quarter rollup.**

- Pass: 3
- Partial: 2
- Fail: 1
- Not-run (not triggered): 1

Findings opened this quarter: 1 (CTRL-DOC-05, sys-0012 FRIA refresh).
Corrective actions opened this quarter: 2 (routing automation on
CTRL-TEC-07; vendor sourcing contingency on CTRL-TP-02).

### 2.5 Roles

- **AI Governance Analyst** — drives the cycle; publishes the plan,
  the register, and the findings pack. Does not own technical
  controls.
- **Head of AI Governance** — chairs the council; owns policy-level
  controls and the FRIA/AIA cadence.
- **Head of ML Engineering** — owns technical controls on model
  behaviour (release gate, fairness monitor, drift, security
  evaluations).
- **Head of Security (or AI Security Lead)** — owns adversarial-ML
  and AI-security controls (prompt-injection evaluation, data-
  poisoning checks, model-supply-chain controls) — see mod-108
  exercise-02 and exercise-03.
- **Head of Procurement / Vendor Risk Lead** — owns third-party AI
  vendor controls — see exercise-04.
- **Legal** — reviews obligation mapping and exception requests;
  does not own controls but signs on policy-adjacent language.
- **Council** — receives the quarterly report; approves exceptions;
  decides escalations.

### 2.6 Routing table — what the analyst does with each result

| Result | Immediate route | 24-hour route | Cycle-end route |
|---|---|---|---|
| `pass` | File evidence in the evidence store. | — | Register row. |
| `partial` | Open corrective action; tag owner; set re-test date. | Confirm owner's acceptance of the action. | Register row + corrective-action list. |
| `fail` | Open finding (severity per §2.7); notify owner and owner's manager; create risk-register entry. | Confirm owner's acceptance and target date. | Register row + findings pack to the council. |
| `not-run` (expected trigger did not occur) | Record "not triggered — no activity". | — | Not a finding. |
| `not-run` (expected trigger did occur, but owner did not operate) | Treat as `fail`; escalate to owner's manager within 24 hours. | Confirm the trigger occurred. | Pattern check against prior quarters — repeat behaviour escalates to the council. |

### 2.7 Severity ladder

- **S1 — Material.** Non-compliance with a bind-level obligation
  (EU AI Act Art. 5, 9, 13, 14, 27; GDPR Art. 22; NYC LL 144 audit
  requirement; sector-specific equivalents) or a security control
  whose failure would surface in an incident within the cycle.
  Escalates to the council within 5 business days.
- **S2 — Significant.** Documentation-material gap (missing FRIA,
  outdated model card on a high-risk system), monitoring gap that
  blocks a council-level decision, or a vendor control failure on
  a tier-1 vendor.
- **S3 — Minor.** Procedural slip with no impact on decision-making
  or compliance posture (late routing, formatting, naming
  conventions). Register row; no council escalation unless
  recurring.

### 2.8 Decision rationale

- **One quarterly beat beats ad-hoc testing.** An ad-hoc regime
  silently de-prioritises slow-signal controls (FRIAs, model cards,
  vendor attestations) in favour of noisy ones; the quarterly beat
  forces coverage.
- **Analyst drives, owners operate.** If the analyst owns technical
  controls, independence is lost and the quarterly register becomes
  a self-grade. The analyst owns the cadence and the aggregation.
- **Clear separation between fail and not-run.** "Control did not
  run because it was not triggered" is very different from "control
  did not run because the owner skipped it". Collapsing both into
  one bucket hides owner-level accountability.
- **Mapping to multiple frameworks per control.** A single control
  often satisfies a NIST AI RMF subcategory, an ISO/IEC 42001 clause,
  and a regulatory obligation. Carrying the mapping on the control
  record (not on the obligation, not on the framework) keeps the
  crosswalk cheap to maintain (see exercise-05).
- **Role-level ownership.** People rotate; roles do not. Audit trails
  survive personnel change only if `owner_role` is a role.
- **Escalation ladder is rule-bound, not discretionary.** The analyst
  does not get to decide whether a FRIA miss is material; the ladder
  decides. Discretion creates the appearance of grading-on-a-curve.
- **Weekly sweep, not weekly meeting.** The sweep is a 20-minute
  evidence-triage pass by the analyst. Standing meetings every week
  for a 90-day cycle burn capacity without adding signal.

## 3. Validation steps

1. Open the quarter's register. Confirm every row has a result and a
   next-due date.
2. For every `partial` or `fail`, confirm a corresponding entry exists
   in the risk register with owner, severity, and target date.
3. For every `pass`, confirm the evidence artefact resolves to the
   evidence store and is dated within the control's acceptable window.
4. For every `not-run`, confirm the trigger-did-not-occur rationale is
   recorded (not an empty cell).
5. Cross-check: for every `mapped_obligation` whose deadline fell in
   the quarter, at least one control with that obligation mapping was
   tested and closed.
6. Cross-check: for every `applies_to_systems` id, the system exists
   in the inventory and is not retired.
7. Confirm the council report named S1 findings explicitly and
   included owner-level target dates.
8. Confirm the delta memo against last quarter is a real diff (which
   controls were added, retired, re-scoped) and not a copy-paste.

## 4. Rubric or review checklist

Grade the runbook submission on six dimensions, 3 points each (total
18). Pass = total ≥ 13 with no 0.

| Criterion | 3 — strong | 2 — acceptable | 0–1 — weak |
|---|---|---|---|
| **Phase structure** | Four phases (plan / test / close / report) with named activities and a week-by-week timeline. | Phases named but activities fuzzy. | One monolithic process with no phases. |
| **Role separation** | Analyst drives the cadence; owners operate controls; council decides exceptions. | Separation named but not maintained in the worked example. | Analyst owns technical controls. |
| **Control record schema** | Minimum fields include id, intent, owner role, frequency, evidence type, mapped risks, mapped obligations, framework mappings, next due. | ~80% of fields present. | Missing framework mappings, owner role, or next due. |
| **Routing rules** | Fail vs. not-run disambiguated; severity ladder defined; escalation path explicit. | Severity named but not mapped to routing. | No severity ladder. |
| **Worked quarter** | Seven-control slice with at least one fail, one partial, and one not-run; register, findings, corrective actions all shown. | Worked quarter with uniform results. | Only one or two controls shown. |
| **Framework linkage** | Explicit mapping to NIST AI RMF functions, ISO/IEC 42001 clauses, and at least one regulatory obligation. | Named NIST AI RMF only. | No framework linkage. |

Document-level checks (binary, all must pass):

- [ ] Every control has `owner_role` as a role, not a team or person.
- [ ] Every control has `next_due_date`.
- [ ] Every fail opens a finding with a target date.
- [ ] Every partial opens a corrective action.
- [ ] Not-run with-trigger is treated as fail.

## 5. Common mistakes

- **Analyst-as-operator.** The analyst runs the fairness monitor
  themselves; the control is "pass" every quarter; the council has
  no independent view. Separate the operator from the aggregator.
- **Pass-all-the-time.** A register with 100% pass across quarters
  usually means the controls are not demanding enough or evidence is
  rubber-stamped. Partial and fail exist to surface signal.
- **Not-run as pass.** Treating a non-triggered on-incident control
  as pass inflates the register. Record "not triggered".
- **Ad-hoc escalation.** Deciding at the moment of failure whether to
  escalate makes the register un-auditable. Decide the ladder up
  front.
- **Framework-only mapping.** Mapping every control to a NIST AI RMF
  subcategory but not to any regulatory obligation makes the register
  useful for self-assessment and useless for regulator readiness.
- **Over-engineering evidence.** Asking for a signed PDF per log
  sample creates evidence fatigue and controls get skipped. Match
  evidence weight to control weight.
- **Standing meetings every week.** A 20-minute triage sweep beats a
  60-minute status meeting. Meetings stay for close and report.
- **One quarter at a time.** The runbook needs an annual re-baseline
  (control catalogue review, obligation-map review, framework
  version review) or it drifts out of alignment.

## 6. References

- NIST, *AI Risk Management Framework (AI RMF 1.0)*, January 2023 —
  `GOVERN`, `MAP`, `MEASURE`, `MANAGE` functions and subcategories.
  <https://www.nist.gov/itl/ai-risk-management-framework>
- NIST, *AI RMF Generative AI Profile* (NIST AI 600-1), 2024.
  <https://nvlpubs.nist.gov/nistpubs/ai/NIST.AI.600-1.pdf>
- ISO/IEC 42001:2023 — *Information technology — Artificial
  intelligence — Management system* (clauses and Annex A controls
  are the AIMS reference for this runbook).
  <https://www.iso.org/standard/81230.html>
- Regulation (EU) 2024/1689 — EU AI Act. Articles cited in the
  severity ladder (Art. 5, 9, 13, 14, 27).
  <https://eur-lex.europa.eu/eli/reg/2024/1689/oj>
- NYC Local Law 144 of 2021 — AEDT; NYC Admin. Code §§20-870 to
  20-874.
  <https://legistar.council.nyc.gov/LegislationDetail.aspx?ID=4344524&GUID=B051915D-A9AC-451E-81F8-6596032FA3F9>
- OWASP Machine Learning Security Top 10 — referenced where a
  technical control addresses an ML-security category.
  <https://owasp.org/www-project-machine-learning-security-top-10/>
- MITRE ATLAS — referenced where a technical control addresses an
  adversarial-ML technique.
  <https://atlas.mitre.org/>
