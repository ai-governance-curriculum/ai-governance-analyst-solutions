# Exercise 03 — GenAI Profile Risk Scoring — Reference Solution

## 1. Solution overview

NIST **AI 600-1 — Generative AI Profile** (July 2024) lists twelve risks
that generative AI uniquely creates or materially amplifies. The profile
maps each risk to actions across the RMF's Govern, Map, Measure, and
Manage functions, but it **does not prescribe a scoring scheme** — scoring
is left to each organisation's risk framework (consistent with the
ISO 31000 / ISO/IEC 23894 posture that scales are organisation-specific).

The analyst's job in this exercise is to turn each AI 600-1 risk for a
specific system into a defensible score — **inherent** and then **residual**
(after planned controls) — using a scale that the organisation already
uses for non-AI risks. Scoring is not statistics. It is a judgement with a
visible trail: what likelihood evidence, what impact evidence, what
control credit, and what reviewer signed off.

This solution provides:

- A reference scoring scale aligned with ISO 31000 / ISO/IEC 23894 (the
  scale is a reference; teams use their organisation's scale instead).
- A worked scoring sheet for the twelve GenAI risks on the
  **PolicyAssistant** system from `exercise-01`.
- Decision rationale for the scores.
- A rubric for grading a learner's scoring sheet.

## 2. Worked answer or implementation

### 2.1 Reference scoring scale (local exercise context)

Replace with the organisation's scale in production. The scale here is
5×5 because many enterprise risk programmes already use 5×5; the method
also works with 3×3 or 4×4.

**Likelihood (per rolling 12 months, assuming controls in-place):**

| L | Label | Guide |
| --- | --- | --- |
| 1 | Rare | Not seen in the industry; theoretical. |
| 2 | Unlikely | Seen in the industry; not in this control environment. |
| 3 | Possible | Plausible once per year for a system of this profile. |
| 4 | Likely | Expected multiple times per year. |
| 5 | Almost certain | Ongoing / observed continuously. |

**Impact (per single occurrence, worst plausible, this system):**

| I | Label | Guide |
| --- | --- | --- |
| 1 | Negligible | Inconvenience; no documented harm; no policy breach. |
| 2 | Minor | Documented harm to a few individuals; contained; internal ticket. |
| 3 | Moderate | Harm to many individuals or a regulated-advice incident; management attention. |
| 4 | Major | Group-level harm, regulator-notifiable event, or material business disruption. |
| 5 | Severe | Systemic harm, sanction exposure, or safety-of-person consequence. |

**Score** = L × I, 1–25. **Residual** = L × I after planned controls land
and the analyst has credited the uplift.

**Risk bands (reference):** 1–4 Low · 5–9 Moderate · 10–14 High · 15–25
Critical. Map to the organisation's acceptance / escalation thresholds.

### 2.2 Scoring sheet — PolicyAssistant on the twelve AI 600-1 risks

Columns:

- **L / I / Inh.** — likelihood, impact, inherent score before controls.
- **Key controls** — planned or in-place.
- **L' / I' / Res.** — likelihood, impact, residual score after controls.
- **Band → routing** — see `exercise-05` for the routing drill.

| # | AI 600-1 risk | L | I | Inh. | Key controls | L' | I' | Res. | Band → routing |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | CBRN information / capabilities | 1 | 2 | 2 | Scope guardrails (HR only); vendor safety filters; corpus has no CBRN. | 1 | 1 | 1 | Low — accept. |
| 2 | Confabulation | 4 | 3 | 12 | Factuality eval tied to policy corpus; citations in responses; "I don't know" fallback; staged rollout. | 3 | 2 | 6 | Mod — accept with monitoring. |
| 3 | Dangerous, violent, or hateful content | 2 | 3 | 6 | Vendor content safety filters; application-level filters; use-reporting. | 1 | 2 | 2 | Low — accept. |
| 4 | Data privacy | 3 | 4 | 12 | Enterprise tenancy with DPA; prompt redaction; retention policy; need-to-know retrieval filter. | 2 | 3 | 6 | Mod — accept with monitoring. |
| 5 | Environmental impacts | 3 | 1 | 3 | Batching, caching, no fine-tune; report in sustainability metrics. | 3 | 1 | 3 | Low — accept. |
| 6 | Harmful bias & homogenisation | 3 | 3 | 9 | Segmented factuality & quality evals by language and job family; staged locale rollout. | 2 | 3 | 6 | Mod — accept with monitoring. |
| 7 | Human–AI configuration | 4 | 3 | 12 | UI-level disclaimers; scope guardrails; training module; link to HR BP for case work. | 3 | 2 | 6 | Mod — accept with monitoring. |
| 8 | Information integrity | 2 | 3 | 6 | Internal-only system; conversation logs available for audit; manager playbook unchanged. | 1 | 2 | 2 | Low — accept. |
| 9 | Information security | 3 | 4 | 12 | Red team on retrieval-side prompt injection; tool allowlist (none exposed); output filters; SSO + role-based retrieval. | 2 | 3 | 6 | Mod — accept with monitoring. |
| 10 | Intellectual property | 1 | 2 | 2 | Corpus is first-party; vendor contract covers training-data posture. | 1 | 1 | 1 | Low — accept. |
| 11 | Obscene / degrading / abusive content | 1 | 3 | 3 | Vendor safety filters; use is authenticated and attributable. | 1 | 2 | 2 | Low — accept. |
| 12 | Value chain & component integration | 4 | 3 | 12 | Vendor change-notice SLA; eval harness runs on each model version; deprecation plan; secondary-vendor evaluation. | 3 | 2 | 6 | Mod — accept with monitoring. |

**Tally:** 0 Critical, 0 High, 6 Moderate, 6 Low after controls. The risks
most load-bearing for this system are confabulation (#2), data privacy
(#4), human–AI configuration (#7), information security (#9), and value
chain (#12) — consistent with the `exercise-01` register.

### 2.3 Decision rationale

- **Likelihood is per-year, per-system, with controls in place.** Scoring
  "without controls at all" is possible but less useful because
  real-world decisions use the controlled state. Showing inherent and
  residual separately lets a reviewer see where controls are doing work
  and where they are not.
- **Impact is "worst plausible per single occurrence", not "expected
  value".** Expected-value scoring collapses the rare-but-severe tail
  that governance exists to catch. The guide anchors impact to
  consequence severity (reportable? regulator-notifiable? safety?).
- **Scores are integers on named anchors.** Decimal scores produce false
  precision. An auditor should be able to read the anchor paragraph for
  "3 — Possible" and recognise the reasoning.
- **Risks that score Low both inherent and residual (e.g., CBRN, IP,
  obscene content) are still listed.** The AI 600-1 list is the
  taxonomy; the exercise is to show the system has been scored against
  every risk, not to drop risks that score Low.
- **Control credit is conservative.** Default to one notch of L
  reduction, or one notch of I reduction, when the control is
  documented and operational — not both. The rubric penalises stacks
  of unsupported credit.
- **Residual scores are inputs to the routing drill, not decisions in
  themselves.** `exercise-05` picks up the Band → routing column.

## 3. Validation steps

1. Confirm every one of the twelve AI 600-1 risks is scored exactly once
   per system (no risk missing, no risk duplicated).
2. Confirm every non-zero score cell has an evidence note: either a
   named control (for L / I reduction) or a one-line justification for
   the inherent L and I.
3. Confirm the scale anchors used in the sheet are the organisation's
   anchors. Reference anchors in §2.1 are not a substitute for the
   actual approved scale.
4. Spot-check three rows against the AI 600-1 action tables: is the
   control claimed in the sheet one that AI 600-1 actually suggests for
   that risk under Measure / Manage? Ad-hoc controls are allowed but
   must be labelled as such.
5. Confirm the residual-to-band mapping uses the organisation's bands
   (or the reference bands in §2.1 if no local policy exists), and that
   routing ties to the acceptance/escalation thresholds defined in
   governance.
6. Peer review: a second analyst re-scores two randomly selected rows
   without seeing the first analyst's L / I. Agreement within one band
   is acceptable; a two-band gap means the anchors or the evidence need
   revision.

## 4. Rubric or review checklist

Score each risk row out of 4; passing average ≥ 2.5 with no 0s.

| Score | Criterion |
| --- | --- |
| **4 — strong** | L and I anchored; controls named and traceable to AI 600-1 actions or ISO/IEC 23894 objectives; residual credit conservative; routing lines up with band. |
| **3 — acceptable** | L and I anchored; controls named; residual credit mostly conservative; one or two rows missing a cite. |
| **2 — weak** | L and I present but one anchor uncited; residual uplift larger than one notch without evidence; routing roughly aligned. |
| **1 — poor** | Scores given without anchor; residual credit stacks ≥ two notches on both L and I with no evidence; routing not aligned. |
| **0 — fail** | Risk missing, duplicated, or renamed; scale used is not the organisation's; routing contradicts band. |

Sheet-level checks (binary, all must pass):

- [ ] All twelve AI 600-1 risks scored, exactly once each.
- [ ] Inherent **and** residual scored for every row.
- [ ] Each control credit has a named control, not "operational
      controls" / "best practices".
- [ ] Residual scores feed the routing drill (`exercise-05`).
- [ ] An un-involved reviewer signed the sheet after a two-row spot-check.

## 5. Common mistakes

- **Scoring only inherent or only residual.** Inherent-only hides the
  value of controls; residual-only hides where controls are load-bearing
  and could fail.
- **Credit stacking.** Taking two notches off L **and** two notches off
  I for every control. Default to one, with evidence. Document the
  evidence.
- **Decimal scores.** A score of 2.3 is false precision. If the right
  answer feels between 2 and 3, write 3 and note the hedge in the
  evidence column.
- **Dropping Low-scoring risks from the sheet.** The sheet is a scored
  version of the AI 600-1 list. Dropping Low-scoring risks hides
  coverage and makes the next review redo the work.
- **Using the reference scale as an authority.** The reference 5×5 scale
  is an example; the operational scale is the one approved by the risk
  committee. Mixing them is the fastest way to disagree with ERM.
- **Treating the score as the decision.** The score is an input to the
  routing drill. Acceptance, mitigation, transfer, and avoidance are
  governance decisions — not functions of a cell colour.
- **No inter-rater check.** Two analysts scoring independently is the
  cheapest control against score drift. Without it, scores reflect the
  loudest voice.

## 6. References

- NIST, _AI 600-1: Artificial Intelligence Risk Management Framework —
  Generative Artificial Intelligence Profile_, NIST AI 600-1, July 2024
  — the twelve risks, risk-management actions, and cross-walk to RMF
  functions. <https://nvlpubs.nist.gov/nistpubs/ai/NIST.AI.600-1.pdf>
- NIST, _AI Risk Management Framework (AI RMF 1.0)_, January 2023 — the
  Core functions the scoring feeds into. <https://www.nist.gov/itl/ai-risk-management-framework>
- ISO 31000:2018 — _Risk management — Guidelines_ — the basis for using
  organisation-specific likelihood / impact scales. <https://www.iso.org/iso-31000-risk-management.html>
- ISO/IEC 23894:2023 — _Information technology — Artificial intelligence
  — Guidance on risk management_ — AI-specific overlay used to justify
  the controls credited in the sheet. <https://www.iso.org/standard/77304.html>
- OWASP, _Machine Learning Security Top 10_ — referenced for controls in
  the information-security row. <https://owasp.org/www-project-machine-learning-security-top-10/>
- MITRE ATLAS — referenced for adversary-technique vocabulary supporting
  the information-security and value-chain rows. <https://atlas.mitre.org/>
