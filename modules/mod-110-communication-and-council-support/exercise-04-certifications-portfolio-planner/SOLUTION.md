# Exercise 04 — Certifications Portfolio Planner — Reference Solution

## 1. Solution overview

Certifications for an AI governance analyst serve three purposes: they
force a structured pass through a body of knowledge the analyst would
otherwise cherry-pick; they signal baseline competence to internal
stakeholders (Legal, Security, Audit) and to an external regulator or
auditor; and they create a cadence for continuing education in a field
that revises its vocabulary every twelve months. They are **not**
qualifications to practise law or give privacy advice, and the planner
below says so explicitly.

This exercise produces a planner: a tiered portfolio recommendation
across the first 24 months of an analyst's career, with decision
rationale on what to prioritise, what to skip, what to defer, and how
to tell the difference between a credential and a course.

NIST AI RMF 1.0 **GOVERN 3.1** asks that workforce competencies be
evaluated and maintained; EU AI Act **Art. 4** ("AI literacy") makes an
explicit demand for staff literacy appropriate to role; ISO/IEC
42001:2023 Clauses 7.1 and 7.2 speak to resources and competence. The
planner is the analyst's answer to how that literacy and competence
are produced and documented.

This solution provides:

- A short set of **selection criteria** to judge any credential.
- A **tiered portfolio** (core, elective, deferred, skip) for an
  analyst serving a mid-sized multi-jurisdictional organisation.
- A **worked 24-month roadmap**.
- A **budget and effort model** (ranges, not specific prices).
- Decision rationale and a grading rubric.

Scope: this is a design exercise. The portfolio below is a reference;
every analyst's actual plan should reflect their org's regulatory
footprint, industry, prior experience, and employer's professional
development programme.

## 2. Worked answer or implementation

### 2.1 How to judge any credential — six criteria

Before spending time or money on a credential, score it on six
dimensions. These are the decision criteria used throughout the
planner.

1. **Scope fit.** Does the body of knowledge cover the domain this
   role actually works in (AI governance, AI risk, privacy, audit,
   security) rather than an adjacent one?
2. **Issuer standing.** Is the issuer a recognised professional
   association, standards body, or accredited training provider
   (e.g. IAPP, ISACA, (ISC)², BCS, a PECB-accredited partner)?
3. **Independent validity.** Is there a sat exam or audited project,
   or is the credential issued on completion of a self-paced
   course? "Completion certificates" are not credentials.
4. **Maintenance requirement.** Is the credential maintained with
   CPE/CPD hours over time? A credential with no maintenance quickly
   decouples from current practice.
5. **External recognition.** Will an auditor, regulator, or
   counter-party recognise the credential? For a GRC or audit
   function this matters more than the analyst's own preference.
6. **Hands-on component.** Does the credential require applied work
   (an implementation project, an audit exercise, a case study), or
   is it pure recall? Pure-recall credentials age faster.

A credential scoring ≥ 4/6 is worth considering. A credential scoring
≤ 2/6 is a course, not a credential; treat it as continuing education,
not as a line on the plan.

### 2.2 Tiered portfolio — reference recommendation

The tiers below are a recommendation for an analyst whose organisation
operates in the US (including NYC and California), the EU, and the UK,
and whose role sits inside a second-line governance function (not a
first-line ML team, not a third-line internal audit). Analysts in
other postures should rebuild the tiers using the criteria in §2.1.

**Tier 1 — Core (take within 12 months)**

| Credential                                                        | Issuer | Why it is core                                                                                                                                                | Notes                                                     |
| :---------------------------------------------------------------- | :----- | :------------------------------------------------------------------------------------------------------------------------------------------------------------ | :-------------------------------------------------------- |
| **IAPP AIGP — Artificial Intelligence Governance Professional**   | IAPP   | Covers AI governance body of knowledge (risk, impact assessment, lifecycle, regulation) at breadth. Directly aligned with this role.                          | Scored 5/6 against criteria. Maintained via CPE.          |
| **IAPP CIPP (regional edition matching footprint)** — CIPP/E for EU/UK; CIPP/US for US focus | IAPP   | Privacy is the adjacent discipline AI governance routinely leans on (GDPR Art. 22, cross-border transfer, lawful basis). An analyst who cannot read a DPIA fluently is handicapped. | Pick one regional edition first; the other can follow as an elective. |

**Tier 2 — Elective, pick one in the first 18 months**

| Credential                                                                    | Issuer                                   | Fit                                                                                                           |
| :---------------------------------------------------------------------------- | :--------------------------------------- | :------------------------------------------------------------------------------------------------------------ |
| **ISO/IEC 42001 Lead Implementer / Lead Auditor** (via an accredited training provider, e.g. PECB or equivalent) | Accredited training providers (ISO's certification scheme is run by third parties, not ISO itself). | Essential if the organisation intends to certify its AI management system, is a vendor into procurement where ISO alignment is asked for, or has an internal-audit function that will audit the AIMS. |
| **ISACA AAIA — Advanced in AI Audit**                                          | ISACA                                    | Essential if the analyst role pairs with Internal Audit or is in an organisation planning an AI audit programme. Aligned with ISACA's audit framing. |
| **ISACA CRISC — Certified in Risk and Information Systems Control**           | ISACA                                    | A fit if the analyst sits close to the enterprise risk function and needs the ERM vocabulary (not AI-specific, but widely recognised). |
| **BCS Foundation Certificate in Artificial Intelligence** (and the Essentials / Practitioner variants in AI-related topics) | BCS, The Chartered Institute for IT     | A lower-stakes early credential in jurisdictions where BCS is recognised (UK and Commonwealth-adjacent markets). Useful as a *starter*, not as a core credential. |

**Tier 3 — Deferred (12–24 months, if the role evolves into them)**

| Credential                                                       | Issuer | When to take it                                                                                                                      |
| :--------------------------------------------------------------- | :----- | :----------------------------------------------------------------------------------------------------------------------------------- |
| **ISO/IEC 27001 Lead Implementer / Lead Auditor** (via accredited training provider) | Accredited training providers | Only if the role is being stretched into information-security management. The overlap with AI governance is real but secondary.      |
| **(ISC)² CISSP**                                                 | (ISC)² | Only if the analyst's role evolves toward security architecture. Heavy pre-req: five years of security experience across eight domains. |
| **IAPP CIPM / CIPT**                                             | IAPP   | After CIPP, if the role is being stretched into programme management (CIPM) or technical privacy (CIPT).                             |
| **ISACA CISA**                                                   | ISACA  | Only if the analyst is moving into internal audit. Audit-adjacent, not AI-native.                                                   |
| **IAPP Fellow of Information Privacy (FIP)**                     | IAPP   | A designation (not an exam) awarded after multiple IAPP certifications and work-experience review. Appropriate once Tier 1 and one Tier 2 CIPP are done. |

**Tier 4 — Skip, or treat as continuing education only**

- Vendor "AI governance" courses issued on completion with no sat
  exam. These are continuing education, not credentials — track the
  CPE hours but do not list them next to AIGP.
- Marketing-driven credentials from training providers with no
  recognised accreditation body behind them. Score them on §2.1
  first; the shiny ones often fail criterion 2 (issuer standing)
  and criterion 3 (independent validity).
- Very narrow "AI prompt engineer" credentials that do not speak to
  governance, risk, or compliance.
- Credentials whose exam dumps circulate widely — the recall value
  evaporates once the question bank is known.

### 2.3 Continuing education that is *not* a credential, but worth logging

These are courses, standards, or playbooks the analyst should work
through regardless of the credential plan, and log as CPE hours
against whichever credentials they maintain.

- **NIST AI RMF 1.0** full read-through, including the Generative
  AI Profile (NIST AI 600-1) and the applicable AI RMF Playbook
  sections. NIST publishes the framework and companion materials;
  work through them as structured self-study.
  <https://www.nist.gov/itl/ai-risk-management-framework>
- **OWASP Machine Learning Security Top 10** — read the current
  release in full; map each risk to a control in the
  organisation's catalogue (see `mod-108`).
  <https://owasp.org/www-project-machine-learning-security-top-10/>
- **MITRE ATLAS** — walk the published case studies; the point is
  to recognise adversary tactics in incident framing (see
  `mod-109`).
  <https://atlas.mitre.org/>
- **ISO/IEC 42001:2023** and **ISO/IEC 23894:2023** standards —
  read at least Clause 4–10 of 42001 and the main body of 23894
  even if not pursuing the Lead Implementer credential.
- **EU AI Act** and the implementing acts as they publish — read
  the primary text and track secondary acts through the regulatory
  tracker (see `mod-106`).
- **NIST SP 800-53 Rev. 5 / 800-218 (SSDF)** where the role has
  any touch with the secure-development side of model deployment.

### 2.4 Worked 24-month roadmap

A reference schedule. Dates are placeholders expressed in months
from role start.

| Month   | Activity                                                                            | Why it lands here                                                                             |
| :------ | :---------------------------------------------------------------------------------- | :-------------------------------------------------------------------------------------------- |
| M0–M1   | Onboarding; complete AI Literacy training (Art. 4 baseline); read NIST AI RMF 1.0.  | Baseline competence; makes subsequent credential study meaningful.                            |
| M1–M3   | Read OWASP ML Top 10, MITRE ATLAS case studies, ISO/IEC 42001 and 23894.            | Build vocabulary used across all Tier 1 and Tier 2 bodies of knowledge.                       |
| M3–M6   | **IAPP AIGP — study and sit the exam.**                                             | AIGP is the role-closest credential; take it first so subsequent work has a scaffolding.      |
| M6–M12  | **IAPP CIPP (regional edition matching footprint) — study and sit the exam.**        | Privacy fluency is needed on every DPIA / GDPR Art. 22 call.                                  |
| M12–M18 | One Tier 2 elective — the one that matches the organisation's trajectory.           | Choice depends on whether the org is going for ISO/IEC 42001 certification, audit, or ERM.    |
| M18–M24 | One second CIPP region (if cross-regional footprint); or a second Tier 2.           | Broadens regional coverage or deepens the primary axis.                                       |
| Ongoing | Track CPE hours; attend one professional conference per year (IAPP GPS, IAPP AI, ISACA, or an ISO working group event). | Credentials require maintenance; conferences expose the analyst to new patterns before they land in exam blueprints. |

### 2.5 Budget and effort model

Exam fees, member/non-member fees, and training-provider fees vary
by issuer, region, and year. Rather than list specific numbers that
go stale fast, the planner uses ranges and a simple decision model.

- **Exam-based credentials** (AIGP, CIPP, CRISC, AAIA, CISSP):
  expect per-credential costs in the low- to mid-hundreds of USD
  for members, higher for non-members. IAPP members often see
  bundled pricing for multi-credential paths. Confirm current
  IAPP, ISACA, and (ISC)² fee schedules against the issuer's
  pricing page for the analyst's region and year before
  submitting the plan.
- **Training-based credentials** (ISO/IEC 42001 and 27001 Lead
  Implementer / Lead Auditor via accredited training providers):
  training-plus-exam packages can run into low four figures USD.
  Choose a provider accredited to deliver against the relevant
  scheme (check the accreditation badge on the provider's page).
  Confirm current accredited-training-provider pricing and
  schedule directly with the provider before committing.
- **Membership fees** (IAPP, ISACA): low- to mid-hundreds per year
  depending on membership class; usually reduce exam fees and
  grant access to study materials.
- **Study effort**: budget **80–120 hours** of study per exam-based
  credential (less for AIGP if coming in with strong AI RMF
  familiarity; more for CISSP or CIPM).
- **Maintenance effort**: budget **20 CPE hours per year per
  credential** as a rough planning figure, with the specific
  cadence set by the issuer (IAPP CPEs renew annually; ISACA
  and (ISC)² CPEs are on three-year cycles). Confirm current
  policy with the issuer.

A conservative two-year total for an analyst taking Tier 1 and one
Tier 2 lands in the **low-four-figures USD** range for exam-based
Tier 1 plus Tier 2 electives, excluding ISO/IEC 42001 Lead
Implementer if that path is chosen (that single credential often
exceeds the Tier 1 total on its own because of the training-provider
component). Employer professional-development budgets commonly
cover exam fees and membership; training packages are more often
negotiated.

### 2.6 Decision rationale

- **AIGP first, not last.** The role-closest credential should be
  the first one earned. Earning it first changes how the analyst
  reads every subsequent exam blueprint.
- **CIPP before CIPM/CIPT.** Privacy fluency starts with the
  foundational privacy law (CIPP/E for the EU/UK; CIPP/US for US
  focus). CIPM is a management credential; CIPT is technical. Both
  are elective in a governance-analyst plan.
- **ISO/IEC 42001 Lead Implementer only if the org is going there.**
  It is a strong credential; it is also expensive and intensive.
  If the organisation is not pursuing ISO/IEC 42001 certification
  and has no customer-side pressure to align to it, it is a
  deferred, not a core, pick.
- **AAIA pairs with internal audit.** The ISACA AAIA credential
  speaks to the audit function more than to a second-line
  governance analyst. If the analyst is paired with (or rotating
  into) audit, it moves up; otherwise it is elective.
- **CISA and CISSP are not AI credentials.** They are information-
  systems audit and security credentials respectively. They are
  excellent in their own right and widely recognised; they are
  not the right first picks for an AI governance role.
- **BCS as a starter in the right market.** BCS credentials are
  short, foundational, and well-regarded in UK and
  Commonwealth-adjacent jurisdictions. They are starters, not
  equivalents to AIGP.
- **No vendor completion certificates on the plan.** They can be
  excellent learning (and many vendors publish strong material),
  but they are continuing education, not credentials. Mixing them
  on the same list devalues the credentials that are.
- **Maintenance is part of the plan, not an afterthought.** A plan
  that lists five credentials and ignores the maintenance cycle
  is a plan to lapse.
- **The plan is not a qualification to practise.** The analyst is
  not giving legal advice, is not a privacy officer unless
  separately appointed, and is not a Lead Auditor in a conformity-
  assessment sense unless the credential's accreditation requires
  it. The planner says this up-front.
- **Choice depends on organisation posture.** The Tier 2 pick is a
  function of whether the org is ISO-aligned, audit-aligned, or
  ERM-aligned. Writing a single Tier 2 recommendation would be
  false precision.
- **Conference time is part of the learning plan.** Credential
  bodies run conferences precisely because the body of knowledge
  moves faster than the exam blueprint; attend one annually.

### 2.7 Decision aid — pick-your-Tier-2 flowchart (text)

- If the organisation **plans to certify to ISO/IEC 42001** in the
  next 24 months → **ISO/IEC 42001 Lead Implementer**.
- Else if the **Internal Audit function** is planning an AI audit
  programme and the analyst is paired with Audit → **ISACA AAIA**.
- Else if the analyst's role **sits inside or adjacent to the
  enterprise risk team** and needs the ERM vocabulary →
  **ISACA CRISC**.
- Else if the organisation's **regulatory footprint is heavily
  UK/Commonwealth** and the analyst is early in the field →
  **BCS Foundation Certificate in Artificial Intelligence** as a
  starter, then revisit after AIGP.
- Else → defer Tier 2 and take the second regional CIPP for the
  footprint covered.

## 3. Validation steps

1. **Issuer check.** Every credential on the plan comes from an
   issuer that matches criterion 2 (recognised professional
   association, standards body, or accredited training provider).
2. **Independent-validity check.** Every credential on the plan
   has a sat exam, an audited project, or an equivalent
   independent assessment.
3. **Maintenance check.** Every credential has a documented CPE/
   CPD maintenance requirement; the plan budgets the hours.
4. **Footprint match.** The regional CIPP edition chosen matches
   the organisation's primary jurisdictional footprint; the Tier 2
   pick matches organisation posture (ISO / audit / ERM / starter).
5. **Budget realism.** Total two-year costs (exam fees + training
   where applicable + membership) are added up and submitted with
   the analyst's professional-development request.
6. **Employer policy alignment.** Confirm the plan aligns with
   the employer's professional-development programme, any
   claw-back requirement, and any bonding or notice obligations.
7. **Claims discipline.** The plan does not claim that any
   credential confers a right to practise law, hold out as a
   privacy officer, or sign a conformity assessment except where
   the credential's accreditation actually permits it.
8. **Local requirements.** Where the role interacts with
   regulated activities (financial services, healthcare,
   children's data), check that any sector-specific credential
   requirement (e.g. CBAP, CCEP, HIPAA training) is handled —
   this planner does not replace sector-specific obligations.
9. **Information currency.** Any dollar figure, exam-fee range,
   or exam-blueprint claim in the plan is confirmed against the
   issuer's current page before the plan is finalised, and any
   claim the analyst cannot verify is left as a
   "verify-with-issuer" footnote in the analyst's own notes.

## 4. Rubric or review checklist

Grade the learner's submission on six dimensions, 3 points each
(total 18). Pass = total ≥ 13 with no 0.

| Criterion | 3 — strong | 2 — acceptable | 0–1 — weak |
|---|---|---|---|
| **Criteria-driven selection** | Each credential is scored on the six criteria; Tier 4 skip-list exists and is justified. | Credentials are listed with brief justifications. | Credentials are listed by name only. |
| **Tier 1 fit** | AIGP plus the right regional CIPP is on Tier 1; rationale references the org's footprint. | AIGP is on Tier 1. | No AIGP, or vendor-course completion certificates placed alongside AIGP. |
| **Tier 2 choice** | Tier 2 pick matches organisation posture (ISO / audit / ERM / starter), and the flowchart in §2.7 is used or equivalent reasoning is shown. | A Tier 2 is chosen with some rationale. | Tier 2 pick looks copied from another plan with no fit reasoning. |
| **Roadmap** | 24-month roadmap with month markers, study time estimates, and ordering rationale. | Roadmap is present but without ordering rationale. | A bare list of credentials. |
| **Budget and maintenance** | Both initial exam/training costs and ongoing CPE/CPD hours are modelled; prices expressed as ranges and flagged for verification against issuer pages where current figures vary. | Initial costs noted; maintenance missing. | No cost or effort estimate. |
| **Claims discipline** | Plan explicitly disclaims any practising-law or regulated-role-equivalence claim; cites AI RMF, ISO/IEC 42001, EU AI Act Art. 4 for workforce competence anchors. | Mostly disciplined; one over-claim. | Over-claims a credential as a licence or a conformity-assessment right. |

Document-level checks (binary, all must pass):

- [ ] At least one credential on the plan requires a sat exam or
      audited project.
- [ ] Each credential's issuer is identified (not "the AIGP cert"
      but "IAPP AIGP").
- [ ] The plan distinguishes credentials from continuing education.
- [ ] Vendor completion certificates are not listed as credentials.
- [ ] Maintenance cadence (CPE/CPD cycle length and per-cycle
      hours) is captured per credential, or flagged for
      verification where current issuer policy must be checked.
- [ ] The plan names the role (not a person) as owner of the
      analyst's professional-development log.

## 5. Common mistakes

- **Buying the credential before the knowledge.** Sitting an exam
  with no prior reading of the underlying material (AI RMF, GDPR,
  ISO clauses) burns the exam fee. The reading comes first.
- **Treating vendor completion certificates as credentials.** A
  vendor course can be excellent; the completion certificate is
  not a credential. Log the CPE, do not elevate it.
- **Picking ISO/IEC 42001 Lead Implementer "because it's hot"
  without organisation pull.** It is a strong credential; without
  an organisation path to certification or a customer asking for
  alignment, the ROI thins out.
- **Chasing every new credential.** The field is producing new
  credentials rapidly. Each one gets scored against §2.1 before
  it goes on the plan.
- **Ignoring maintenance.** Falling off CPEs on one credential
  produces a cascade — the credential lapses, the body of
  knowledge drifts, and re-certification costs rise.
- **Over-claiming.** AIGP, CIPP, and AAIA do not make the holder
  a lawyer, a DPO, or an auditor-of-record in a conformity-
  assessment sense. Holding the credential and holding a
  regulated role are different things.
- **Picking both CIPP editions in year one.** Pick one region
  first; take the second as the elective in year two.
- **Pricing without ranges.** Specific dollar figures go stale
  inside a year. Ranges with a "verify-with-issuer" footnote for
  the current figure keep the plan honest.
- **Omitting conferences.** The exam blueprint is a lagging
  indicator. Conferences expose patterns before the next
  revision.
- **Writing the plan as a shopping list.** Without the
  decision criteria, the next analyst cannot tell why each
  credential is on the list and will add noise.
- **Forgetting sector-specific requirements.** A financial-
  services or healthcare employer may require sector-specific
  training (BCS or CFA for finance, HIPAA Awareness for US
  healthcare) independently of this plan.

## 6. References

- NIST, *AI Risk Management Framework (AI RMF 1.0)*, January
  2023 — `GOVERN 3.1` (workforce competencies evaluated and
  maintained) is the planner's workforce-competence anchor.
  <https://www.nist.gov/itl/ai-risk-management-framework>
- NIST, *Artificial Intelligence Risk Management Framework:
  Generative AI Profile* (NIST AI 600-1) — continuing-education
  reading.
  <https://www.nist.gov/itl/ai-risk-management-framework>
- ISO/IEC 42001:2023 — *Information technology — Artificial
  intelligence — Management system*, Clause 7.2 (competence).
  <https://www.iso.org/standard/81230.html>
- ISO/IEC 23894:2023 — *Information technology — Artificial
  intelligence — Guidance on risk management*.
  <https://www.iso.org/standard/77304.html>
- Regulation (EU) 2024/1689 (EU AI Act), Art. 4 (AI literacy).
  <https://eur-lex.europa.eu/eli/reg/2024/1689/oj>
- OWASP Machine Learning Security Top 10 — continuing-education
  reading referenced by the planner.
  <https://owasp.org/www-project-machine-learning-security-top-10/>
- MITRE ATLAS — continuing-education reading.
  <https://atlas.mitre.org/>
- IAPP — body administering the AIGP, CIPP (regional editions),
  CIPM, CIPT, and the FIP designation.
  <https://iapp.org/>
- ISACA — body administering CRISC, CISA, CISM, and the
  Advanced in AI Audit (AAIA) credential.
  <https://www.isaca.org/>
- (ISC)² — body administering the CISSP.
  <https://www.isc2.org/>
- BCS, The Chartered Institute for IT — body administering the
  Foundation Certificate in Artificial Intelligence and related
  BCS credentials.
  <https://www.bcs.org/>
- Local exercise context: `mod-101` (governance landscape);
  `mod-103` (risk vocabulary); `mod-106` (regulatory tracker
  for keeping the plan current with the law); `mod-108`
  (controls catalogue for mapping ML security standards).
