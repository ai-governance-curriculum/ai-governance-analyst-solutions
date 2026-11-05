# Exercise 05 — Residual Risk Routing Drill — Reference Solution

## 1. Solution overview

Once the analyst has scored residual risk after planned controls (see
`exercise-03`), the next step is to **route** each row to the correct
decision maker. NIST AI RMF 1.0 Manage 1.3 is explicit: responses to
risk — accept, mitigate further, transfer, or avoid — are decisions that
belong to accountable parties, not to the analyst drafting the register.
ISO 31000 / ISO/IEC 23894 draw the same line between risk assessment and
risk treatment decisions.

Routing is a drill because the mechanics are mostly the same each time:
match residual score to a band, apply policy triggers (regulatory,
rights-impacting, safety-of-person), identify the accountable party,
and record the decision with an expiry date. Done badly it becomes "the
analyst decided"; done well it produces a transparent decision trail a
regulator or auditor can follow.

This solution provides:

- A reference routing policy (bands → routing actions → decision maker).
- Policy triggers that **force escalation regardless of band**.
- A worked routing table for the twelve scored risks of
  `exercise-03`'s PolicyAssistant plus the credit model from
  `exercise-04`.
- Decision rationale and rubric.

## 2. Worked answer or implementation

### 2.1 Reference routing policy (local exercise context)

Replace with the organisation's routing policy in production.

**Band → default routing**

| Residual band | Default routing action | Default decision maker | Default evidence captured |
| --- | --- | --- | --- |
| **Low (1–4)** | Accept. | System owner. | Signed risk-acceptance note; expiry on next scheduled revalidation. |
| **Moderate (5–9)** | Accept with monitoring. | System owner + Head of function (first-line leader). | Signed acceptance + named monitoring metric + threshold + review cadence. |
| **High (10–14)** | Mitigate further, or escalate to accept. | Function leader + second line (MRM / Risk / Compliance). | Treatment plan with target, owner, due date; or escalated acceptance with sunset. |
| **Critical (15–25)** | Avoid, transfer, or executive-level acceptance. | Executive risk committee; board where policy requires. | Committee minutes; acceptance tied to specific compensating controls and a hard sunset. |

**Policy triggers that force escalation beyond the band**

A row that meets **any** of these is routed upward irrespective of
numeric score:

- **R1 — Rights-impacting**: automated adverse decision affecting an
  individual's rights (credit, employment, housing, insurance,
  education, essential services, migration).
- **R2 — Safety-of-person**: risk of physical or psychological harm to
  a person.
- **R3 — Regulated advice**: output presented as, or reasonably mistaken
  for, legal / medical / financial / tax advice without appropriate
  qualification.
- **R4 — External disclosure**: risk of disclosing confidential or
  personal data outside the organisation.
- **R5 — Reportable incident potential**: plausible event is
  notifiable under GDPR, HIPAA, state breach laws, EU AI Act Art. 73
  (serious incidents for high-risk systems), SR 11-7 outcomes
  analysis, or similar regimes.
- **R6 — Jurisdiction / regulator attention**: the use case is in a
  jurisdiction's current enforcement focus (verify current scope
  against local regulator priorities).

When a trigger fires: route to the lowest level that holds the
authority named in the trigger's policy mapping. A Low-score row with
an R1 trigger is **not** accepted by the system owner alone — it is
routed to the function leader + second line at minimum.

**Required columns on every routed row**

- Residual score and band.
- Decision (Accept / Accept with monitoring / Mitigate further /
  Transfer / Avoid).
- Decision maker (named role, not team).
- Evidence (document, meeting, signed acceptance).
- Expiry date or re-review trigger.
- Policy triggers fired, if any.

### 2.2 Worked routing — PolicyAssistant (from `exercise-03`)

Using the residual scores from `exercise-03` §2.2. Policy triggers
checked per row.

| # | Risk | Res. | Band | Triggers | Decision | Decision maker | Expiry / review |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | CBRN | 1 | Low | — | Accept. | People-Ops system owner. | Next annual revalidation. |
| 2 | Confabulation | 6 | Mod | R3 (regulated-advice framing possible) | Accept with monitoring (factuality threshold); escalation to Head of People-Ops + Compliance review of UX disclaimers. | Head of People-Ops + Compliance. | Monthly factuality-eval review; next revalidation. |
| 3 | Dangerous content | 2 | Low | — | Accept. | System owner. | Next revalidation. |
| 4 | Data privacy | 6 | Mod | R4 (confidential data) | Accept with monitoring: retention + role-based retrieval evidence; Privacy sign-off. | Head of People-Ops + Privacy Officer. | Quarterly retention audit; next revalidation. |
| 5 | Environmental | 3 | Low | — | Accept. | System owner. | Next revalidation. |
| 6 | Bias / homogenisation | 6 | Mod | R1 potential if extended to decisioning | Accept with monitoring on current scope; **scope expansion requires re-routing**. | Head of People-Ops + Fairness reviewer. | Review on any scope change; next revalidation. |
| 7 | Human–AI configuration | 6 | Mod | R3 (user may treat as advice) | Accept with monitoring: UX training completion metric; disclaimer A/B outcomes. | Head of People-Ops. | Semi-annual UX / training review. |
| 8 | Information integrity | 2 | Low | — | Accept. | System owner. | Next revalidation. |
| 9 | Information security | 6 | Mod | R4, R5 | Accept with monitoring: red-team cadence; incident runbook tested; Security sign-off. | Head of People-Ops + CISO delegate. | Quarterly red-team; incident drill annually. |
| 10 | Intellectual property | 1 | Low | — | Accept. | System owner. | Next revalidation. |
| 11 | Obscene / abusive content | 2 | Low | — | Accept. | System owner. | Next revalidation. |
| 12 | Value chain | 6 | Mod | R5 (vendor outage notifiable) | Accept with monitoring: vendor change-notice SLA; eval-on-version-change; secondary vendor evaluated. | Head of People-Ops + Vendor Risk. | Quarterly vendor review. |

**Summary**: 6 Low rows accepted by system owner; 6 Moderate rows
accepted-with-monitoring at function-leader level with named
second-line partners. No row routed to executive committee.

### 2.3 Worked routing — SmallDollarUW credit model (from `exercise-04`)

Using a representative residual scoring (not fully worked in the SR 11-7
mapping). Policy triggers fire differently because this system makes
automated adverse decisions.

| # | Risk (representative) | Res. | Band | Triggers | Decision | Decision maker | Expiry / review |
| --- | --- | --- | --- | --- | --- | --- | --- |
| C1 | Fair-lending disparate impact on reportable segments. | 9 | Mod | R1, R5, R6 | **Escalated** — mitigate further to meet bank fair-lending thresholds before go-live; no acceptance at this stage. | Fair-Lending Officer + Chief Compliance + Model Risk Committee. | Treatment plan with target date; re-score after mitigation. |
| C2 | Adverse-action reason-code accuracy (FCRA). | 6 | Mod | R1, R5 | Accept with monitoring post-launch; **go-live blocked** until Legal signs off reason-code wording. | Chief Compliance + Legal. | Quarterly audit of top reason codes. |
| C3 | Data-quality incident on bureau pull affects scoring population. | 8 | Mod | R1 | Accept with monitoring: input schema validation, PSI, drift alerts; incident runbook. | Head of Consumer Lending + MRM. | Monthly PSI review; incident drill annually. |
| C4 | Model concept drift (change in applicant mix) degrades calibration. | 8 | Mod | R1 | Accept with monitoring; revalidation trigger if PSI / calibration thresholds breached. | Head of Consumer Lending + MRM. | Monthly monitoring; annual revalidation. |
| C5 | Governance gap — missing inventory entry or documentation. | 4 | Low | — | Accept once populated. | MRM. | Close before go-live; recheck at annual revalidation. |
| C6 | Vendor dependency on bureau API. | 6 | Mod | R5 | Accept with monitoring: BIA entry, failover, SLA. | Head of Consumer Lending + Vendor Risk. | Quarterly vendor review. |
| C7 | UDAAP exposure from edge-case decline messaging. | 9 | Mod | R1, R3 | **Escalated** — mitigate further; comms review. | Chief Compliance + Legal. | Treatment plan with target date; re-score. |

**Summary**: Two rows escalated for mitigation before go-live (fair
lending; UDAAP messaging). FCRA reason-code accuracy carries a hard
go-live block in the acceptance itself. All automated-decision rows
(R1) escalate to compliance leadership, consistent with policy.

### 2.4 Decision rationale

- **The band is a default, not a verdict.** Policy triggers (R1–R6)
  routinely override the numeric band. A Low-scored row that affects
  someone's credit is not an owner-level acceptance.
- **Named role, not team.** "Signed off by Compliance" is unauditable;
  "Signed off by Chief Compliance Officer" is. The rubric flags
  team-level sign-offs as weak evidence.
- **Expiry or re-review trigger on every acceptance.** Acceptance
  without a sunset is a quiet escape from the register. The default
  "next annual revalidation" is a minimum; events (incident, scope
  change, vendor change, regulatory change) are explicit triggers.
- **Escalation is not a punishment.** It is a request for authority.
  Analysts who treat escalation as failure dilute it; analysts who
  decline to escalate transfer authority to themselves.
- **Rights-impacting triggers (R1) apply to automated adverse
  decisions.** The credit model has them; the HR assistant is
  flagged for a possible future scope expansion but does not have
  them today.
- **Two routing views exist for one register.** The internal view
  (who signed what, when) and the external view (executive summary of
  High and Critical rows). The rubric expects both.

## 3. Validation steps

1. Confirm every residual-scored row has exactly one routing decision.
   Blank rows mean the drill was not completed.
2. Confirm every decision has a named decision maker (role), evidence
   reference, and expiry or re-review trigger.
3. Re-check policy triggers (R1–R6) row by row. A Low-score row with a
   trigger that was missed is the single most common failure mode.
4. Confirm no acceptance is signed at a level below what policy
   requires for the trigger. An R1 row accepted only by the system
   owner is a finding in audit.
5. Cross-check against the organisation's acceptance thresholds and
   delegations of authority. The reference bands in this solution are
   examples; the operational bands are what govern.
6. Confirm the executive summary covers every High / Critical row and
   names the sunset condition. Executives should not have to read the
   full register to see what they are being asked to sign.
7. Confirm the routing table is linked from the risk assessment and
   from the model inventory record. Routing that lives only in an
   analyst's working file does not satisfy the governance element.

## 4. Rubric or review checklist

Score each routed row out of 4; passing average ≥ 3.0 with no 0s.

| Score | Criterion |
| --- | --- |
| **4 — strong** | Band and triggers both evaluated; decision maker is a named role at the right level; evidence pointer specific; expiry or trigger stated. |
| **3 — acceptable** | Decision correct; one of (triggers / evidence / expiry) under-specified. |
| **2 — weak** | Decision correct; triggers missed on at least one row, or evidence is "operational controls"; expiry absent. |
| **1 — poor** | Decision maker is a team, not a role; multiple triggers missed; no expiry. |
| **0 — fail** | Rights-impacting row accepted at owner level; or acceptance without evidence; or acceptance without expiry. |

Drill-level checks (binary, all must pass):

- [ ] Every residual-scored row is routed.
- [ ] Every policy trigger (R1–R6) has been evaluated per row.
- [ ] Every decision has a named role, evidence pointer, and
      expiry/trigger.
- [ ] No rights-impacting or safety-of-person row is accepted below
      the policy-required level.
- [ ] Executive summary of High / Critical rows exists and names the
      sunset for each.
- [ ] Routing table is linked from the risk assessment and inventory.

## 5. Common mistakes

- **Routing on score alone, ignoring triggers.** A Low-scored
  credit-model row is still rights-impacting and must escalate.
- **Team-level sign-off.** "Compliance signed it" cannot be audited
  later. Name the role.
- **No expiry.** Risk acceptance without a sunset is a permanent
  concession.
- **Analyst as decision maker.** The analyst routes; the accountable
  party decides. If the analyst's name is in the decision-maker column,
  the governance model has drifted.
- **One routing table, no executive view.** Reviewers at the
  committee level will not read the full register; they need the
  High / Critical slice with sunsets.
- **Confusing "accept with monitoring" with "mitigate".** Acceptance
  with monitoring keeps the current control stack and watches for
  deterioration; mitigation changes the controls. A plan that calls a
  mitigation "monitoring" hides work that should have an owner and a
  due date.
- **Reusing the HR-assistant routing policy for a credit model.** The
  defaults differ because the triggers differ. Keep routing tied to
  the policy, not to the last system reviewed.

## 6. References

- NIST, _AI Risk Management Framework (AI RMF 1.0)_, January 2023 —
  Manage function; Manage 1.3 (responses to risk: accept, mitigate,
  transfer, avoid). <https://www.nist.gov/itl/ai-risk-management-framework>
- NIST, _AI 600-1: Generative AI Profile_, July 2024 — risk list and
  action mappings feeding the routed rows. <https://nvlpubs.nist.gov/nistpubs/ai/NIST.AI.600-1.pdf>
- ISO 31000:2018 — _Risk management — Guidelines_ — basis for risk
  treatment and residual-risk acceptance process. <https://www.iso.org/iso-31000-risk-management.html>
- ISO/IEC 23894:2023 — _Information technology — Artificial
  intelligence — Guidance on risk management_ — AI-specific overlay. <https://www.iso.org/standard/77304.html>
- Board of Governors of the Federal Reserve System, _SR 11-7:
  Guidance on Model Risk Management_, 4 April 2011 — basis for
  independent effective-challenge sign-off for US bank use cases. <https://www.federalreserve.gov/supervisionreg/srletters/sr1107.htm>
- OWASP, _Machine Learning Security Top 10_ — referenced for
  information-security routing anchors. <https://owasp.org/www-project-machine-learning-security-top-10/>
- MITRE ATLAS — referenced for adversary-technique vocabulary in
  security-related routing rows. <https://atlas.mitre.org/>
