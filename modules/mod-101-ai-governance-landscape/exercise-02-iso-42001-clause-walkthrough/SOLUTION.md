# Exercise 02 — ISO/IEC 42001 Clause Walkthrough — Reference Solution

## 1. Solution overview

ISO/IEC 42001:2023 is the first management-system standard for an **AI
Management System (AIMS)**. Like ISO/IEC 27001 (ISMS) and ISO 9001 (QMS), it
follows the Harmonised Structure: Clauses 4–10 are the auditable management
system, Clauses 1–3 are scope and terminology, and the annexes carry reference
controls and guidance.

A governance analyst's job in this exercise is to walk the clauses, name what
each one asks for, and identify the artefact or decision that answers it.
The deliverable below is the worked clause-by-clause walkthrough, the decision
rationale for why each artefact was chosen, and a rubric for grading a
learner's version.

## 2. Worked answer or implementation

### 2.1 Clause-by-clause walkthrough

| Clause | Title | What it requires (plain language) | Analyst artefact |
| --- | --- | --- | --- |
| 1 | Scope | States the standard applies to any org providing or using AI, regardless of size or sector. | Scope statement in the AIMS manual. |
| 2 | Normative references | Lists referenced ISO documents (notably ISO/IEC 22989 terminology). | Reference list — maintained, not drafted. |
| 3 | Terms and definitions | Points to ISO/IEC 22989 and ISO/IEC 23894 for AI vocabulary. | Internal glossary mapped to the ISO terms. |
| 4 | Context of the organisation | Determine internal/external issues, interested parties, scope of the AIMS, and the AIMS itself. | Context analysis, stakeholder register, AIMS scope statement. |
| 4.1 | Understanding the organisation and its context | Identify issues relevant to AI purpose and outcomes. | PESTLE-style context note scoped to AI. |
| 4.2 | Needs and expectations of interested parties | Capture requirements of users, regulators, affected persons, workforce, suppliers. | Interested-parties register. |
| 4.3 | Scope of the AIMS | Define boundaries — which AI systems, roles, products are in or out. | Signed AIMS scope statement. |
| 4.4 | AIMS itself | Establish, implement, maintain, continually improve. | AIMS manual / wiki index. |
| 5 | Leadership | Top-management commitment, AI policy, roles, responsibilities, authorities. | AI policy signed by leadership; RACI. |
| 5.2 | AI policy | Policy that is appropriate to purpose, includes commitments to requirements and continual improvement. | AI policy document. |
| 5.3 | Roles, responsibilities, authorities | Defined and communicated. | RACI / role charters including AI ethics owner, AI risk owner, AI system owner. |
| 6 | Planning | Address risks and opportunities, do AI risk assessment, AI risk treatment, AI system impact assessment, set objectives, plan changes. | Risk register, treatment plan, impact assessments, objective register. |
| 6.1.1 | General | Determine risks and opportunities. | Entry in the risk register with context links. |
| 6.1.2 | AI risk assessment | Repeatable process to identify, analyse, and evaluate AI risks. | Risk-assessment procedure + per-system risk assessment record. |
| 6.1.3 | AI risk treatment | Select treatment options, determine controls (with reference to Annex A), produce a Statement of Applicability (SoA), produce treatment plan, get residual-risk acceptance. | Statement of Applicability, treatment plan, residual-risk sign-off. |
| 6.1.4 | AI system impact assessment | Assess potential consequences to individuals, groups, and societies. | AI system impact assessment per system. |
| 6.2 | AI objectives and planning | Measurable objectives aligned with the AI policy. | Objectives register with owners, metrics, due dates. |
| 6.3 | Planning of changes | Changes to the AIMS done in a planned manner. | Change log for AIMS scope, policy, controls. |
| 7 | Support | Resources, competence, awareness, communication, documented information. | Training records, competency matrix, comms plan, document control. |
| 7.2 | Competence | Determine and ensure competence for AI-affecting roles. | Competency matrix tied to RACI. |
| 7.3 | Awareness | Workforce aware of AI policy, their contribution, implications of non-conformance. | Awareness training log. |
| 7.4 | Communication | Internal and external AI communications determined. | Communications plan including incident disclosures. |
| 7.5 | Documented information | Create, update, and control records. | Document-control procedure, version history, retention schedule. |
| 8 | Operation | Operational planning and control, run AI risk assessment, risk treatment, impact assessment at planned intervals and after changes. | Operating-rhythm calendar, re-assessment triggers, change gates. |
| 8.2 | Operational AI risk assessment | Perform at planned intervals and when significant changes occur. | Re-assessment records. |
| 8.3 | Operational AI risk treatment | Implement treatment plan and retain evidence. | Control implementation evidence (tickets, screenshots, logs). |
| 8.4 | AI system impact assessment (operational) | Run impact assessments across the lifecycle. | Impact assessment refreshes tied to lifecycle gates. |
| 9 | Performance evaluation | Monitoring, measurement, analysis, evaluation, internal audit, management review. | Monitoring plan, internal audit programme, management-review minutes. |
| 9.1 | Monitoring, measurement, analysis, evaluation | Decide what to monitor, how, when, by whom; evaluate performance and AIMS effectiveness. | Monitoring plan with metrics and cadence. |
| 9.2 | Internal audit | Planned internal audit at planned intervals. | Audit programme, audit reports, non-conformities. |
| 9.3 | Management review | Top management reviews AIMS at planned intervals. | Management-review agenda and minutes. |
| 10 | Improvement | Continual improvement, non-conformity and corrective action. | NC/CA log, lessons-learned log. |
| 10.1 | Continual improvement | Improve suitability, adequacy, effectiveness of the AIMS. | Improvement register. |
| 10.2 | Non-conformity and corrective action | React to NCs, evaluate need for action, implement, review effectiveness. | Corrective-action records. |

### 2.2 Annex walkthrough

| Annex | Role in the standard | Analyst use |
| --- | --- | --- |
| **Annex A** (normative) | Reference AI control objectives and controls. Used in 6.1.3 to pick controls and justify in the Statement of Applicability. | Pull control titles into the SoA; justify include/exclude for each. |
| **Annex B** (informative) | Implementation guidance for Annex A controls. | Reference text for "how" a control is typically implemented. |
| **Annex C** (informative) | Potential AI-related organisational objectives and risk sources. | Seed list when building the risk register and objectives register. |
| **Annex D** (informative) | Use of the AIMS across domains or sectors, and interaction with other management systems (e.g., ISO 27001, 9001, 27701). | Integration notes when the org already runs an ISMS or QMS. |

### 2.3 Decision rationale

- **Walkthrough lists artefacts, not activities.** An auditor evaluates the
  AIMS by reading evidence. Keeping the right-hand column artefact-first makes
  the walkthrough double as an evidence checklist.
- **Clauses 6.1.2 / 6.1.3 / 6.1.4 kept distinct.** 42001 is unusual among
  harmonised-structure standards because it adds an explicit **AI system
  impact assessment** (6.1.4) on top of the usual risk assessment and risk
  treatment. Collapsing them hides the obligation most analysts need to own.
- **Annex A is normative; Annexes B–D are informative.** The Statement of
  Applicability references Annex A. Analysts who cite Annex B/C/D as
  obligations misread the standard.
- **Operational clauses (8.x) duplicate planning clauses (6.1.x) on purpose.**
  Planning is "designed the process"; Operation is "ran it on schedule and
  after changes". The walkthrough mirrors this duplication rather than hiding
  it.
- **No specific control counts stated.** The reference list of Annex A
  controls evolves between editions; the walkthrough teaches structure, not
  memorisation of control numbering.

## 3. Validation steps

1. Read the clause titles against the ISO/IEC 42001:2023 table of contents.
   Clauses 4 through 10 must all be present; no clauses from the Harmonised
   Structure should be missing.
2. For each operational clause (8.x), confirm the planning clause it mirrors
   (6.1.x). If 8.x cannot be traced back to 6.1.x, the walkthrough has drifted.
3. For each artefact listed, ask: "could an auditor pick this up and reach a
   conclusion without asking a human?" If no, tighten the artefact.
4. Cross-check Clause 5.2 (AI policy) against Clause 7.3 (awareness): awareness
   training content must trace to the AI policy. Walkthroughs that treat them
   as independent should be revised.
5. Confirm Annex A is labelled normative and B–D informative; this is the
   single most common citation error.

## 4. Rubric or review checklist

Score each major clause out of 3; passing threshold is ≥ 2.0 average and no
Clause 6 or Clause 9 scoring below 2.

| Score | Criterion |
| --- | --- |
| **3 — strong** | Clause number and title correct; plain-language description matches the standard; named artefact is auditable and specific. |
| **2 — acceptable** | Clause number and title correct; description matches; artefact is listed but generic. |
| **1 — weak** | Clause number correct but description drifts between clauses, OR artefact is a process step rather than an output. |
| **0 — fail** | Clause number misidentified, invented sub-clauses, or description contradicts the standard. |

Deck-level checks (binary, all must pass):

- [ ] All clauses 4–10 covered.
- [ ] Clauses 6.1.2, 6.1.3, and 6.1.4 appear as distinct items.
- [ ] Statement of Applicability is cited under 6.1.3 (not elsewhere).
- [ ] Annex A is labelled normative; B–D labelled informative.
- [ ] Management review (9.3) and internal audit (9.2) are both present
      as separate artefacts.
- [ ] Impact assessment (6.1.4) is distinguished from risk assessment (6.1.2).

## 5. Common mistakes

- **Treating ISO/IEC 42001 as a controls catalogue.** It is a management
  system standard. Annex A is the controls catalogue; Clauses 4–10 are the
  system that runs it.
- **Collapsing risk assessment and impact assessment.** They answer different
  questions — "what could go wrong for the org/system" vs. "what could be the
  consequences for individuals and society". 42001 asks for both.
- **Forgetting the Statement of Applicability.** The SoA is the artefact that
  proves which Annex A controls apply, with justification. Audits fail without
  one.
- **Citing clauses from older AI governance drafts.** 42001 is the published
  2023 standard; 38507 and 23894 are related but different documents (one is
  governance implications for the governing body, the other is risk
  management guidance). The walkthrough should quote the 42001 clauses, not
  mix them.
- **Documenting activities without evidence.** "Run a risk review" is not an
  artefact. "Signed risk-review minutes with attendees and decisions" is.

## 6. References

- ISO/IEC 42001:2023, _Information technology — Artificial intelligence —
  Management system_ — the clauses and annex structure walked above.
- ISO/IEC 23894:2023, _Information technology — Artificial intelligence — Guidance on risk management_ — cited for AI-risk terminology used in 42001.
- ISO/IEC 22989:2022, _Information technology — Artificial intelligence — Artificial intelligence concepts and terminology_ — the normative vocabulary reference from Clause 2/3.
- NIST, _AI Risk Management Framework 1.0_ (used for the parallel Govern /
  Map / Measure / Manage vocabulary cited in rationale). <https://www.nist.gov/itl/ai-risk-management-framework>
