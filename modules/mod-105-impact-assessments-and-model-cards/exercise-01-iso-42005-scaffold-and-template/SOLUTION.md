# Exercise 01 — ISO/IEC 42005 Scaffold and Template — Reference Solution

## 1. Solution overview

**ISO/IEC 42005:2025** — *Information technology — Artificial intelligence
— AI system impact assessment* — is the first horizontal international
standard that tells an organisation how to **document** an AI-system
impact assessment (AIIA). It is deliberately framework-neutral: it does
not tell you whether to call the artefact an AIA, a FRIA, an AIIA, or a
"risk and impact note" — it tells you what a defensible AI impact-
assessment report has to contain and how to decide when to do one.

Its siblings matter: ISO/IEC 42001:2023 (AI management system) expects
an organisation to have an impact-assessment process in place, and
ISO/IEC 23894:2023 provides the risk-management vocabulary. 42005 is
what gets filled in per AI system.

A governance analyst who is standing up an AIIA programme needs a
**reusable scaffold**: a Markdown or Word template an intake team can
fill in without re-reading the standard every time. This solution
delivers:

- A section-by-section scaffold aligned to ISO/IEC 42005 (clauses on
  context, when to perform, and documentation).
- A completed worked example filling the scaffold for a small reference
  system (`PolicyAssistant` from `mod-103`) so the learner can see what
  "good" looks like.
- Decision rationale for every section (what goes in, what does not).
- A rubric for grading a learner's own scaffold and worked example.

Scope note: 42005 is structured around **documentation**. The scaffold
below mirrors that intent — it is a report template, not a risk
register. Risk-scoring mechanics live in ISO/IEC 23894 and NIST AI RMF
1.0 (`mod-103`).

## 2. Worked answer or implementation

### 2.1 The reference scaffold (template)

The scaffold is published as `scaffold.md` alongside this solution. Each
section corresponds to content required or recommended by ISO/IEC
42005. Section ordering follows the standard; wording is paraphrased for
practitioner use so this file does not reproduce the standard verbatim.

```markdown
# AI System Impact Assessment — <system name>

| Field | Value |
| --- | --- |
| System name | |
| Internal system ID | |
| AIIA version / date | |
| AIIA owner (role) | |
| Approver (role) | |
| Related artefacts | Intake record, risk assessment, model card, DPIA, FRIA if applicable |

## 1. Scope and purpose of this assessment
- Why this AIIA is being performed now (new deployment, material change,
  periodic review, trigger event — see §3).
- What system version and release boundary this AIIA covers.
- Which prior AIIA version it supersedes, if any.

## 2. AI system description
- Intended purpose and intended users.
- Operational context (geography, sector, environment, language).
- Functional description: inputs, outputs, decisions produced, human
  role in the decision loop.
- System architecture at a block-diagram level: model(s), data stores,
  retrieval components, third-party components, monitoring hooks.
- Lifecycle stage covered (design, pre-deployment, deployed, retired).
- Data categories used in training, validation, and inference,
  including whether any personal data or special-category data is
  processed (link to DPIA if yes).
- Reasonably foreseeable misuse and out-of-scope uses.

## 3. Trigger for this assessment
State which of the following produced this AIIA and cite the governing
policy section:
- Pre-deployment AIIA on a new AI system.
- Material change (model swap, new modality, new jurisdiction, new user
  population, new decision authority, new data category).
- Periodic review per policy.
- Incident-driven re-assessment.

## 4. Stakeholders and affected parties
- Internal stakeholders (owners, operators, users).
- External affected parties: individuals, groups, communities,
  third-party organisations, society at large.
- Vulnerable or marginalised populations likely affected.
- How affected parties were (or will be) consulted or represented.

## 5. Benefits
- Intended benefits for the organisation, for users, and for affected
  parties.
- How the benefit is measured and by whom.
- Baseline / status-quo comparison.

## 6. Potential harms and impacts
For each identified impact, record:
- **Impact description** — what could happen.
- **Affected party** — who bears it.
- **Reversibility** — reversible / partially reversible / irreversible.
- **Severity** — scale of harm if it occurs, with the level definitions
  the organisation uses.
- **Likelihood** — with the level definitions the organisation uses.
- **Source of risk** — data, model, deployment, use, third party, misuse.
- **Evidence or reasoning** — why this harm is plausible.

Impact categories to canvas (not exhaustive):
- Physical safety.
- Psychological harm.
- Fundamental rights (privacy, non-discrimination, freedom of
  expression, due process, access to services).
- Financial harm.
- Health harms.
- Societal and group-level impacts (information integrity,
  concentration of power, environmental impact).
- Economic and labour impacts.
- Security impacts (OWASP ML Top 10, MITRE ATLAS).

## 7. Mitigations and controls
- Mitigations already in place and the residual impact after each.
- Mitigations committed to as a precondition for deployment, with owner
  and due date.
- How effectiveness of each mitigation will be verified.

## 8. Residual impact and acceptability decision
- Residual severity × likelihood per impact after mitigations.
- The acceptance decision (proceed / proceed with conditions / do not
  proceed) and who made it, with authority reference.
- Conditions attached to the acceptance.

## 9. Monitoring, review, and update plan
- Monitoring signals that would invalidate this AIIA.
- Scheduled review cadence and next review date.
- Change-management triggers that force re-assessment.
- How monitoring findings feed back into future AIIAs.

## 10. Cross-references
- Related risk assessment (NIST AI RMF / ISO 23894).
- DPIA under GDPR Art. 35 if personal data is processed.
- FRIA under EU AI Act Art. 27 if applicable.
- Model card(s) and datasheet(s).
- Intake record.

## 11. Sign-off
- AIIA author.
- Reviewer(s).
- Approver with authority to accept residual impact.
- Date of approval.
```

### 2.2 Worked example (PolicyAssistant, reference scenario)

A completed example of the scaffold is provided below so the learner can
see what passing content looks like. PolicyAssistant is a reference
scenario carried over from `mod-103/exercise-01`.

| Field | Value |
| --- | --- |
| System name | PolicyAssistant |
| Internal system ID | AI-0421 |
| AIIA version / date | v1.0 — 2026-02-10 |
| AIIA owner | Governance analyst, AI Governance Office |
| Approver | VP, People Operations |
| Related artefacts | Intake AI-0421, risk assessment RA-0421-v1, model card MC-0421-v1 |

**1. Scope and purpose.** Pre-deployment AIIA for the initial rollout of
PolicyAssistant to all ~6,000 employees, English-language only, covering
system version 1.0 released 2026-01-28. Supersedes no prior AIIA.

**2. AI system description.** Internal RAG assistant that answers
employee HR-policy questions by retrieving from an approved corpus and
summarising with a vendor-hosted LLM under the enterprise tenancy.
No fine-tuning. Single-tenant retrieval index. Users: all employees
and managers. Reasonably foreseeable misuse: using the assistant as a
substitute for legal, medical, tax, or disciplinary advice; attempting
to coax disciplinary rulings on named colleagues.

**3. Trigger.** New AI system entering production (policy §4.1.a).

**4. Stakeholders.** Internal: HR, HRBPs, IT Platform, Legal, Works
Council (EU), InfoSec. Affected parties: all employees, with particular
attention to employees on performance plans, employees pursuing
accommodation requests, and non-native-English speakers.

**5. Benefits.** Reduce HR ticket volume on policy lookups; faster
time-to-answer for employees; free HR staff for case work. Measured via
ticket deflection, CSAT, and HR staff time studies.

**6. Potential harms (top items from the full register).**

| Impact | Affected party | Reversibility | Severity | Likelihood | Source | Evidence |
| --- | --- | --- | --- | --- | --- | --- |
| Employee relies on hallucinated policy to make an irreversible decision (e.g., resignation, benefit opt-out). | Employees | Partially reversible | High | Medium | Model (RAG+LLM) | Known LLM hallucination failure mode, documented in red-team runs. |
| Disclosure of policy content that should be restricted (e.g., EU-only policies to US employees, or draft policies). | Employees, legal | Partially reversible | Medium | Medium | Data (corpus partitioning) | Prior incident class with intranet search; see security review. |
| Assistant produces answers that differ by language or accent of the employee's query. | Non-native English-speaking employees | Partially reversible | Medium | Medium | Model (multilingual quality) | Known LLM behaviour on low-resource prompts. |
| Perceived surveillance (employees avoid asking legitimate questions because they fear the log). | Employees | Reversible | Medium | Low | Deployment (logging) | Internal survey evidence from similar rollouts. |
| Content-injection attack via policy document (MITRE ATLAS TA0043 / OWASP ML05 input-manipulation class). | Enterprise, employees | Reversible with effort | High | Low | Third-party + data | Corpus ingestion path accepts Word/PDF uploads from HR editors. |

**7. Mitigations.**
- Answers include citations back to source policy sections and a
  "contact HR" CTA.
- Disclaimer: "informational, not advice" on every answer.
- Retrieval scoped by employee locale; cross-locale retrieval blocked.
- Logs anonymised after 30 days; log access requires two-party approval.
- Policy-document ingestion pipeline runs a prompt-injection scrubber
  and a diff review against the prior version before indexing.

**8. Residual impact and decision.** After mitigations, the top harm
(reliance on hallucinated policy for irreversible action) scores Medium
severity × Low likelihood. Approved to proceed with conditions: ship
with English only; re-assess before adding any non-English locale; keep
the informational-not-advice disclaimer non-dismissable.

**9. Monitoring.** Quarterly re-assessment; triggered re-assessment on
model swap, new locale, or any incident involving reliance-on-answer.
Monitoring signals: hallucination red-team pass rate, HR escalation
volume, works-council feedback.

**10. Cross-references.** DPIA DPIA-0421 (personal data in logs). No
FRIA — PolicyAssistant is not a high-risk system under EU AI Act
Annex III and the deployer is not an obliged entity under Art. 27.
Model card MC-0421-v1. Intake AI-0421.

**11. Sign-off.** Author: J. Doe, AI Governance analyst. Reviewers:
Legal, Privacy, InfoSec. Approver: VP People Operations, 2026-02-14.

### 2.3 Decision rationale

- **Scaffold maps to ISO/IEC 42005 clauses, but is not a verbatim
  reproduction.** The standard is copyrighted; practitioner templates
  paraphrase and cite. The scaffold maps cleanly to the clauses on
  context, when-to-perform, and documentation content.
- **Severity, likelihood, and reversibility are separate columns.**
  ISO/IEC 23894 and 42005 both treat these as independent axes;
  collapsing them into a single "risk score" hides the severity-
  dominated items a council would want to escalate.
- **Affected parties go beyond users.** 42005 explicitly expects the
  AIIA to consider individuals, groups, and society — not just the
  system's users. The template has a dedicated column for this.
- **Trigger section is non-negotiable.** Without it, the AIIA fleet
  cannot be audited for coverage: an auditor cannot tell whether an
  AIIA is current, late, or missing.
- **The acceptance decision is named and attributable.** An AIIA with
  no approver is not an acceptance; it is a note.
- **Cross-references, not duplicates.** The AIIA does not re-run the
  risk assessment, DPIA, or FRIA; it points at them. This keeps
  artefacts in sync and auditable one at a time.

## 3. Validation steps

1. Read each scaffold section against the ISO/IEC 42005 documentation
   requirements (clauses on AIIA content and the when-to-perform
   guidance). Mark any required content the scaffold does not surface.
2. Hand the blank scaffold to an analyst who has not seen this exercise
   and ask them to complete it for a different reference system
   (e.g., a résumé-screening tool). If they cannot complete section 6
   without the reviewer's help, the "impact categories to canvas"
   list is too thin.
3. For the worked example, confirm every row in §6 has a plausible
   severity / likelihood / reversibility triple and a mitigation in §7
   or an explicit "accepted as-is" note.
4. Audit cross-references: if the AIIA mentions a DPIA, the DPIA must
   exist; if it mentions a model card, the model card must exist.
5. Walk a fictitious change (e.g., adding a Spanish locale) through
   §9 and confirm the trigger is picked up by the scaffold.

## 4. Rubric or review checklist

Score each learner submission on both scaffold and worked example.

### Scaffold (0–3 per section; must cover all eleven sections)

| Score | Criterion |
| --- | --- |
| **3 — strong** | Section is present, headings match the standard's intent, prompts in the template are specific enough that a non-author can fill them in. |
| **2 — acceptable** | Section present but some prompts are vague ("describe risks" with no breakdown of severity / likelihood / reversibility / affected party). |
| **1 — weak** | Section present but mis-scoped (e.g., mixes the risk assessment into the AIIA) or missing material sub-items (e.g., no reasonably-foreseeable misuse). |
| **0 — fail** | Section missing, or scaffold confuses AIIA with model card / DPIA / risk register. |

### Worked example (binary, all must pass)

- [ ] All eleven scaffold sections are completed.
- [ ] Section 6 has at least one impact per affected-party category
      that applies to the system, with severity, likelihood, and
      reversibility populated.
- [ ] Every impact in §6 has a corresponding mitigation in §7 or an
      explicit "accepted as-is" statement.
- [ ] Residual impact and acceptance decision in §8 are consistent
      with §6 and §7 (no "all residual Low" when §6 is full of Highs
      and §7 does not address them).
- [ ] At least one monitoring signal in §9 is directly tied to a §6
      impact.
- [ ] Cross-references in §10 either exist or are flagged as
      not-applicable with reason.
- [ ] Trigger (§3) is present and consistent with policy.

## 5. Common mistakes

- **Treating the AIIA as the risk assessment.** The AIIA documents
  impact on affected parties; the risk assessment prioritises what to
  do. Both exist; one is not the other.
- **Collapsing severity, likelihood, and reversibility into a single
  score.** Hides exactly the harms a reviewer wants to see.
- **Users-only stakeholder list.** 42005 expects individuals, groups,
  and society; a stakeholder list that only names internal teams is a
  fail.
- **Skipping reasonably foreseeable misuse.** This is where the
  deployer's duty of care lives. Omitting it is the single most common
  gap in first-draft AIIAs.
- **No trigger.** An AIIA with no trigger cannot be audited for
  coverage across the fleet.
- **Mitigations without owners or verification method.** Commitments
  without owners are not commitments.
- **Acceptance without authority.** Residual impact "accepted" by the
  AIIA author rather than an accountable approver.
- **Duplicating DPIA/FRIA content inline.** Drifts out of sync; cite
  instead.

## 6. References

- ISO/IEC 42005:2025, *Information technology — Artificial intelligence
  — AI system impact assessment*. Content paraphrased for practitioner
  use; obtain the standard from ISO for normative wording.
- ISO/IEC 42001:2023, *Information technology — Artificial intelligence
  — Management system*. The management-system standard that expects
  this impact-assessment activity to exist.
- ISO/IEC 23894:2023, *Information technology — Artificial intelligence
  — Guidance on risk management*. The sibling risk-management vocabulary
  used for severity/likelihood framings.
- NIST, *AI Risk Management Framework (AI RMF 1.0)*, January 2023 —
  Map 5 "Impacts to individuals, groups, communities, organizations,
  and society" is the AI RMF analogue of the impact-assessment scope.
  <https://www.nist.gov/itl/ai-risk-management-framework>
- OWASP Machine Learning Security Top 10 — used as the ML-security
  checklist feeding §6 of the AIIA.
  <https://owasp.org/www-project-machine-learning-security-top-10/>
- MITRE ATLAS — adversarial-ML tactics and techniques used as the
  misuse / attack-path lens for §6.
  <https://atlas.mitre.org/>
